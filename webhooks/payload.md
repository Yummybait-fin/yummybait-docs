# Payload Schema

Every webhook delivery carries a JSON body whose shape is **defined by
the [Metrics Catalog](../reference/metrics-catalog.md)**. The catalog
is the single source of truth — this page describes the wrapping shape
and points to the catalog for field-level details.

## Envelope

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
| `schema_version` | Matches `CATALOG_VERSION`. Bumps in lockstep with the catalog. |
| `event` | What triggered the delivery (`advice_cycle`, `out_of_range`, …). |
| `subscription_id` | The subscription this delivery belongs to. |
| `delivered_at` | Server-side ISO-8601 timestamp at the moment of dispatch. |
| `data` | The catalog-driven payload — see below. |

## `data.positions[i]`

Each position object **flattens the `position` group at the top**, then
nests every other position-scope group under its name:

```json
{
  "position_id": 12345,
  "token_pair": "USDC/WETH",
  "chain": "Ethereum",
  "...": "(other position-group fields)",

  "pool":           { /* pool group fields */ },
  "similar_pools":  [ /* zero or more similar-pool items */ ],
  "range_analysis": { /* range_analysis group fields */ }
}
```

If a pool snapshot is unavailable for a position (e.g. brand-new pool
not yet indexed), `pool` is emitted as `{}` rather than omitted.

`range_analysis` and `market` use **omit-on-null** semantics — a key
whose value is unavailable is omitted from the payload entirely. Every
other group emits null for missing nullable fields.

## `data.market`

A single object shared across all positions in the payload:

```json
{
  "eth":       { "price_usd": 3210.4, "change_24h_pct": 1.2, "...": "..." },
  "btc":       { "price_usd": 64210.0, "change_24h_pct": -0.4, "...": "..." },
  "sentiment": { "fear_greed_index": 52, "classification": "Neutral" }
}
```

Field-level documentation for every key is in the
[Metrics Catalog](../reference/metrics-catalog.md).
