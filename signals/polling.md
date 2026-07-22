# Polling

Your agent drives signals by **polling** `POST /v1/signals` on a cadence
you choose. There is no subscription to create or delete — every request
is self-contained: it carries your key, your wallets, and your rules.

## Auth

Every request is authenticated with a per-user **API key**, minted for
you when you register with the Telegram bot. Pass it as a Bearer token:

```
Authorization: Bearer ybt_live_…
```

Keys are prefixed `ybt_live_`. A missing or invalid token returns:

```http
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{ "error": "invalid or missing bearer token" }
```

## The request loop

```http
POST /v1/signals
Content-Type: application/json
Authorization: Bearer ybt_live_…
```

```json
{
  "cursor": null,
  "wallets": ["0xabc…", "0xdef…"],
  "rules": [
    {
      "name": "il_high",
      "when": "il_pct > 5.0",
      "for": "15m",
      "cooldown": "6h",
      "severity": "warn",
      "note": "Impermanent loss above 5%"
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `cursor` | string \| null | Opaque position from your previous response. Send `null` on the first poll. |
| `wallets` | string[] | Owner addresses whose positions you want evaluated. |
| `rules` | object[] | Your rules (see [Rules](#rules) below). |

The response returns an advanced `cursor` and the rules that fired.
**Persist the cursor and send it back on your next poll** to continue
where you left off:

```json
{
  "cursor": "1717761600",
  "fires": [
    {
      "rule": "il_high",
      "severity": "warn",
      "note": "Impermanent loss above 5%",
      "position_id": "1:12345",
      "chain_id": 1,
      "token_id": 12345,
      "value": true,
      "fired_at": 1717761600
    }
  ],
  "rule_errors": []
}
```

Poll as often as makes sense for your use case — the underlying metrics
refresh roughly once a minute, so polling faster than that returns the
same values.

## Rules

A rule is a named [CEL](https://github.com/google/cel-spec) condition
over the [Metrics Catalog](../reference/metrics-catalog.md), with
optional Prometheus-style firing semantics.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | yes | Identifier for the rule. Echoed back on every fire. |
| `when` | string | yes | CEL expression evaluated per position. Must return a bool. |
| `for` | duration | no (default `0`) | The condition must hold continuously for this long before firing. |
| `cooldown` | duration | no (default `0`) | After a fire, suppress re-fires for this long. |
| `severity` | string | no (default `"info"`) | Free-form level (e.g. `info`, `warn`). Echoed on each fire. |
| `note` | string | no | Human-readable context. Echoed on each fire. |

**Durations** accept either an integer number of seconds or a string
with a unit suffix: `s`, `m`, `h`, `d` (e.g. `"30s"`, `"15m"`, `"6h"`,
`"2d"`).

### What `when` can reference

Expressions are validated against the catalog, so a typo'd field name is
caught before evaluation. Today the sampler populates this **live**
subset of catalog fields per position:

- **Current values:** `pnl_usd`, `pnl_hodl`, `liquidity_value_usd`,
  `uncollected_fees_usd`, `tvl_usd`, `il_pct`, `breakeven_margin`,
  `in_range`.
- **Windowed deltas** (current minus the value one window ago) for
  `pnl_usd`, `pnl_hodl`, `liquidity_value_usd`, `uncollected_fees_usd`,
  `tvl_usd`, and `breakeven_margin` — each with `_change_1h`,
  `_change_1d`, and `_change_1w` suffixes
  (e.g. `pnl_usd_change_1d`, `breakeven_margin_change_1w`).

Other catalog groups (`pool.*`, `range_analysis.*`, `similar_pools`,
`market.*`) are part of the schema but **not yet populated on the
signals path** — a rule referencing them evaluates to null and will not
fire. The catalog marks the live surface; treat the rest as planned.

A null or cold-start metric makes the whole expression evaluate to
**false** (never an error), so rules degrade safely while a position is
warming up.

Example rules:

```json
[
  { "name": "il_high",        "when": "il_pct > 5.0",             "for": "15m", "cooldown": "6h", "severity": "warn" },
  { "name": "pnl_dropping",   "when": "pnl_usd_change_1d < -50.0", "cooldown": "12h", "severity": "warn" },
  { "name": "near_breakeven", "when": "breakeven_margin < 1.0 && breakeven_margin > -1.0", "for": "30m" },
  { "name": "went_oor",       "when": "!in_range",                "cooldown": "1h" }
]
```

For a complete polling loop and rule set in context, see the
[reference agent example](https://github.com/Yummybait-fin/cdp-wallet-agent-example)
— a forkable Claude Code agent config built around this endpoint.

## Dry run

Append `?dry_run=1` (also accepts `true` / `yes`) to validate and
explain your rules **without firing or advancing state**:

```http
POST /v1/signals?dry_run=1
Authorization: Bearer ybt_live_…
```

The response reports, per compiled rule, which positions matched vs.
were suppressed by `for:`/`cooldown:`. Rules that fail to compile are
omitted from the dry-run response — submit a plain `POST` (no
`?dry_run`) to get compile failures back in `rule_errors`. See
[Payload Schema](payload.md#dry-run-response) for the shape.

Use dry run while iterating on expressions; switch to a plain `POST` once
you're happy.
