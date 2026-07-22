# Push delivery (future)

!!! note "Status: future option — not built"

    Signals today are [pull-based](index.md): your agent polls
    `POST /v1/signals`. This page records a **planned** alternative —
    push (webhook) delivery — for integrators who'd rather receive
    callbacks than poll. None of the endpoints below exist yet; field
    names, auth, and paths are provisional and may change before any
    launch. Track the [changelog](../reference/changelog.md).

## Why push might be added

Polling is simple and stateless, but a push option would suit
integrators who want:

- **Lower latency** — delivered on the sampling cycle instead of on your
  poll interval.
- **No poll loop to run** — receive a callback instead of scheduling
  requests.
- **The same metric contract** — deliveries would reuse the
  [Metrics Catalog](../reference/metrics-catalog.md), so the data shape
  matches what `/advice` and the pull API already expose.

The trade-off is operational: push requires you to run a public HTTPS
endpoint and verify signatures, which is exactly the burden the
pull model avoids.

## Planned subscription model

A subscription would bind a callback URL to a set of wallets and events.

### Create a subscription

```http
POST /v1/webhooks
Content-Type: application/json
Authorization: Bearer ybt_live_…
```

```json
{
  "url": "https://your-agent.example.com/yummybait",
  "wallets": ["0xabc…", "0xdef…"],
  "events": ["advice_cycle", "out_of_range"],
  "secret": "whsec_your_signing_secret"
}
```

Returns the created subscription with an `id`.

### Delete a subscription

```http
DELETE /v1/webhooks/{id}
Authorization: Bearer ybt_live_…
```

### Verifying signatures

Every delivery would include an `X-YummyBait-Signature` header
containing an HMAC-SHA256 of the raw request body, keyed by the `secret`
you provided. Reject any request whose signature doesn't match.

### Retries

Failed deliveries (non-2xx, timeout >10s) would be retried with
exponential backoff for up to 24 hours, then dropped, with a `replay`
endpoint for manual re-delivery.

## Planned payload envelope

A push delivery would wrap the catalog-driven payload in a delivery
envelope (note: distinct from the pull API's
[response shape](payload.md)):

```json
{
  "schema_version": "v1",
  "event": "advice_cycle",
  "subscription_id": "sub_…",
  "delivered_at": "2026-05-06T12:34:56Z",
  "data": {
    "positions": [ /* one entry per position */ ],
    "market":    { /* global market context */ }
  }
}
```

| Field | Description |
|-------|-------------|
| `schema_version` | Matches `CATALOG_VERSION`; bumps in lockstep with the catalog. |
| `event` | What triggered the delivery (`advice_cycle`, `out_of_range`, …). |
| `subscription_id` | The subscription this delivery belongs to. |
| `delivered_at` | Server-side ISO-8601 timestamp at dispatch. |
| `data` | The catalog-driven payload (same field shape as the `/advice` LLM payload). |

`data.positions[i]` would flatten the `position` group at the top and
nest every other position-scope group (`pool`, `similar_pools`,
`range_analysis`) under its name; `data.market` would carry the shared
market context. Field-level documentation lives in the
[Metrics Catalog](../reference/metrics-catalog.md).
