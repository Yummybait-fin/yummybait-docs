# Payload Schema

This page is the exact wire contract for `POST /v1/signals`. Field-level
meaning for every metric you can reference in a rule lives in the
[Metrics Catalog](../reference/metrics-catalog.md) — this page covers the
request and response envelopes.

## Request

```json
{
  "cursor": "1717761540",
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

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `cursor` | string \| null | no | The `cursor` from your previous response. Omit or send `null` on the first poll. |
| `wallets` | string[] | no (default `[]`) | Owner addresses to evaluate. No wallets → no positions → no fires. |
| `rules` | object[] | no (default `[]`) | Rules to evaluate. See the [rule fields](polling.md#rules). |

Unknown top-level fields are ignored.

## Live response

A plain `POST` (no `dry_run`) returns the fires since your last cursor.

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
  "rule_errors": [
    { "rule": "typo_rule", "error": "undeclared reference to 'pnl_uds'" }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `cursor` | string | Advanced position marker. Send it back on the next poll. |
| `fires` | object[] | One entry per `(rule × position)` that fired this cycle. |
| `rule_errors` | object[] | Rules that failed to compile (others still evaluate). |

### `fires[i]`

| Field | Type | Description |
|-------|------|-------------|
| `rule` | string | The `name` of the rule that fired. |
| `severity` | string | The rule's `severity`, echoed. |
| `note` | string \| null | The rule's `note`, echoed. |
| `position_id` | string | Stable id, `"{chain_id}:{token_id}"` (e.g. `"1:12345"`). |
| `chain_id` | integer | Chain the position is on. |
| `token_id` | integer | Position NFT / PositionManager token id. |
| `value` | boolean | Always `true` in v1 (reserved for multi-valued metrics). |
| `fired_at` | integer | Epoch seconds when the fire was recorded. |

### `rule_errors[i]`

| Field | Type | Description |
|-------|------|-------------|
| `rule` | string \| null | The offending rule's `name` (null if it couldn't be identified). |
| `error` | string | Compilation/validation message. |

## Dry-run response

`POST /v1/signals?dry_run=1` returns a per-rule explanation and **does
not fire or advance any cursor**.

```json
{
  "evaluated": [
    {
      "rule": "il_high",
      "compiled": true,
      "error": null,
      "matched": [
        { "position_id": "1:12345", "chain_id": 1, "token_id": 12345, "value": true }
      ],
      "suppressed": [
        { "position_id": "1:67890", "chain_id": 1, "token_id": 67890, "reason": "cooldown" }
      ]
    }
  ],
  "rule_errors": []
}
```

| Field | Type | Description |
|-------|------|-------------|
| `evaluated` | object[] | One entry per rule that **compiled successfully**. Rules that fail to compile are omitted (see note below). |
| `rule_errors` | object[] | Same shape as the live response, but **always empty in dry-run** — compile errors are not reported here. |

!!! note "Compile errors and dry-run"

    Dry-run only evaluates rules that compiled, so every `evaluated[i]`
    has `compiled: true` and `error: null`, and a rule with a bad CEL
    expression is simply left out of the response (it does **not** appear
    in `rule_errors` either). To see compile errors, submit a live
    `POST /v1/signals` (without `?dry_run`) — the `rule_errors` array
    there reports each rule that failed to compile.

### `evaluated[i]`

| Field | Type | Description |
|-------|------|-------------|
| `rule` | string | Rule name. |
| `compiled` | boolean | Always `true` in dry-run (only compiled rules are evaluated). |
| `error` | string \| null | Always `null` in dry-run (present for forward-compat with the live `rule_errors` shape). |
| `matched` | object[] | Positions where the rule **would** fire (condition true, `for:`/`cooldown:` permit it). |
| `suppressed` | object[] | Positions where the condition was true but a fire was held back. |

`matched[i]` carries `position_id`, `chain_id`, `token_id`, `value`.
`suppressed[i]` carries the same plus a `reason`:

| `reason` | Meaning |
|----------|---------|
| `for_not_met` | The condition hasn't held long enough to satisfy `for:`. |
| `cooldown` | Within `cooldown:` of the last fire. |
