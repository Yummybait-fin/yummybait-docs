# Metrics Catalog

!!! note "Auto-generated"

    This page is generated from
    `libs/common/src/common/metrics_catalog.py` on every docs release.
    Do not edit by hand — your changes will be overwritten.

**Catalog version:** `v1`
**Generated:** 2026-07-22 12:59 UTC

The catalog is the single source of truth for two consumers:

1. The **Telegram bot's `/advice`** LLM payload.
2. The **signals** payload (see [Signals](../signals/index.md)).

Producers populate per-position bundles and a global market snapshot
from their data sources; the catalog defines what fields end up in the
emitted JSON.


## `position`

Per-position core metrics: identifiers, P/L, size, fees collected/uncollected, gas, age, and pair RSI orientation.

- **Scope** — Emitted **once per position** in the `positions[]` array.
- **Cardinality** — Single object whose keys are this group's metrics.

| Key | Type | Unit | Nullable | Description |
|-----|------|------|----------|-------------|
| `position_id` | `integer` | — | no | Position NFT id (uint256; fits in int64 in practice). |
| `token_pair` | `string` | — | no | Display label of the position's token pair, e.g. 'USDC/WETH'. |
| `chain` | `string` | — | yes | Display name of the blockchain hosting the position. |
| `composition` | `string` | — | no | Pool composition class: stable_stable, stable_major, stable_alt, major_major, major_alt, alt_alt. |
| `pair_rsi_14` | `number` | — | yes | Wilder RSI(14) on the pool exchange-rate series, oriented to the displayed pair direction. |
| `pair_rsi_quote` | `string` | — | no | Reading direction of pair_rsi_14, e.g. 'USDC per WETH'. |
| `in_range` | `boolean` | — | yes | Whether the current pool price is within the position's range. |
| `pnl_usd` | `number` | `usd` | yes | Profit/loss vs entry, in USD. |
| `pnl_hodl` | `number` | `usd` | yes | Profit/loss vs simply HODLing the entry tokens, in USD. |
| `liquidity_value_usd` | `number` | `usd` | no | Current USD value of tokens held in the position. |
| `uncollected_fees_usd` | `number` | `usd` | no | USD value of fees accrued but not yet collected. |
| `gas_cost_usd` | `number` | `usd` | no | Estimated gas cost (USD) of an action on this position. |
| `opened_at` | `timestamp` | — | yes | ISO-8601 timestamp when the position was opened. |

## `pool`

Snapshot of the pool the position lives in: TVL, volume, fees, AI risk classification, recent open/close activity.

- **Scope** — Emitted **once per position** in the `positions[]` array.
- **Cardinality** — Single object whose keys are this group's metrics.

| Key | Type | Unit | Nullable | Description |
|-----|------|------|----------|-------------|
| `tvl_usd` | `number` | `usd` | no | Total value locked in the pool. |
| `volume_24h_usd` | `number` | `usd` | no | Trading volume over the last 24 hours. |
| `fees_24h_usd` | `number` | `usd` | no | Fees accrued by all LPs over the last 24 hours. |
| `fee_tier_bps` | `integer` | `bps` | no | Pool fee tier in basis points (×100; e.g. 3000 = 0.3%). |
| `tx_count_24h` | `integer` | — | no | Number of swap transactions in the last 24 hours. |
| `ai_risk_level` | `string` | — | no | Coarse AI-classified risk label (e.g. 'low', 'medium', 'high'). |
| `ai_risk_reason` | `string` | — | no | Short human-readable rationale produced by the risk classifier. |
| `positions_opened_24h` | `integer` | — | no | Distinct positions opened in the pool in the last 24h. |
| `positions_closed_24h` | `integer` | — | no | Distinct positions closed in the pool in the last 24h. |
| `volume_change_24h_pct` | `number` | `percent` | yes | Percent change of yesterday's vs the day-before's 24h volume. Null until at least two daily snapshots exist. |
| `volume_trend_7d_pct_per_day` | `number` | `percent` | yes | Slope of ln(daily_volume_usd) over the last ~7 daily snapshots, expressed as percent change per day. Negative = sustained decline. Null until at least 3 daily readings exist. |

## `similar_pools`

DefiLlama matches for pools similar in pair / fee / chain — used as a yield benchmark.

- **Scope** — Emitted **once per position** in the `positions[]` array.
- **Cardinality** — Array of objects; each object's keys are this group's metrics.

| Key | Type | Unit | Nullable | Description |
|-----|------|------|----------|-------------|
| `project` | `string` | — | no | DefiLlama project slug. |
| `symbol` | `string` | — | no | Pool pair symbol on DefiLlama. |
| `tvl_usd` | `number` | `usd` | no | TVL on the matched pool. |
| `apy` | `number` | `percent` | no | Total APY (base + reward). |
| `apy_base` | `number` | `percent` | no | Base APY (excluding rewards). |
| `volume_usd_1d` | `number` | `usd` | no | 1-day trading volume on the matched pool. |
| `il_risk` | `string` | — | no | Impermanent-loss risk label from DefiLlama. |

## `range_analysis`

Derived range / fee-economics signals: distance to bounds, volatility-modeled ETA to bound, daily fee run-rate, breakeven days. Unavailable metrics are omitted from the payload.

- **Scope** — Emitted **once per position** in the `positions[]` array.
- **Cardinality** — Single object whose keys are this group's metrics.
- **Null handling** — fields with no value are **omitted** from the payload (rather than emitted as `null`).

| Key | Type | Unit | Nullable | Description |
|-----|------|------|----------|-------------|
| `position_age_seconds` | `integer` | `seconds` | yes | Age of the position in seconds. |
| `position_age_days` | `number` | `days` | yes | Age of the position in days. |
| `distance_to_lower_pct` | `number` | `percent` | yes | Distance from current price to the lower bound, as a percent of current price. Negative = price is below the bound. |
| `distance_to_upper_pct` | `number` | `percent` | yes | Distance from current price to the upper bound, as a percent of current price. Negative = price is above the bound. |
| `pool_pair_volatility_ann_pct` | `number` | `percent` | yes | Annualized volatility of the pool exchange rate, in percent. |
| `est_hours_to_lower_bound` | `number` | `hours` | yes | Random-walk first-passage-time estimate of hours until the price reaches the lower bound. |
| `est_hours_to_upper_bound` | `number` | `hours` | yes | Random-walk first-passage-time estimate of hours until the price reaches the upper bound. |
| `est_hours_to_nearest_bound` | `number` | `hours` | yes | Min of the two bound ETAs. |
| `est_daily_fee_income_usd` | `number` | `usd` | yes | Run-rate fee income per day, derived from total fees over position age. |
| `est_days_to_breakeven` | `number` | `days` | yes | Days of fee income required to recover the current loss; only set when pnl_usd < 0. |
| `will_likely_exit_before_breakeven` | `boolean` | — | yes | True when est_days_to_breakeven exceeds est_hours_to_nearest_bound / 24. |
| `is_full_range` | `boolean` | — | yes | True when the position spans the full price range (ticks at MIN/MAX). When set, bound-proximity signals (distance_*, est_hours_to_*, will_likely_exit_before_breakeven) are omitted from the payload because they are meaningless for such positions — fee income is volume-driven, not range-driven. |

## `windowed`

Rolling-window changes of selected metrics (_1h / _1d / _1w), computed server-side from tiered position history. Flattened at the top of each position object and referenced bare in CEL rules, e.g. `pnl_usd_change_1d < -5`. Unavailable windows are omitted.

- **Scope** — Emitted **once per position** in the `positions[]` array.
- **Cardinality** — Single object whose keys are this group's metrics.
- **Null handling** — fields with no value are **omitted** from the payload (rather than emitted as `null`).

| Key | Type | Unit | Nullable | Description |
|-----|------|------|----------|-------------|
| `pnl_usd_change_1h` | `number` | `usd` | yes | Change in profit/loss vs entry (pnl_usd) over the last 1 hour: current value minus the value ~1 hour ago. Null until ~1 hour of position history has accrued. |
| `pnl_usd_change_1d` | `number` | `usd` | yes | Change in profit/loss vs entry (pnl_usd) over the last 24 hours: current value minus the value ~24 hours ago. Null until ~24 hours of position history has accrued. |
| `pnl_usd_change_1w` | `number` | `usd` | yes | Change in profit/loss vs entry (pnl_usd) over the last 7 days: current value minus the value ~7 days ago. Null until ~7 days of position history has accrued. |
| `pnl_hodl_change_1h` | `number` | `usd` | yes | Change in profit/loss vs HODLing the entry tokens (pnl_hodl) over the last 1 hour: current value minus the value ~1 hour ago. Null until ~1 hour of position history has accrued. |
| `pnl_hodl_change_1d` | `number` | `usd` | yes | Change in profit/loss vs HODLing the entry tokens (pnl_hodl) over the last 24 hours: current value minus the value ~24 hours ago. Null until ~24 hours of position history has accrued. |
| `pnl_hodl_change_1w` | `number` | `usd` | yes | Change in profit/loss vs HODLing the entry tokens (pnl_hodl) over the last 7 days: current value minus the value ~7 days ago. Null until ~7 days of position history has accrued. |
| `liquidity_value_usd_change_1h` | `number` | `usd` | yes | Change in USD value of tokens held in the position (liquidity_value_usd) over the last 1 hour: current value minus the value ~1 hour ago. Null until ~1 hour of position history has accrued. |
| `liquidity_value_usd_change_1d` | `number` | `usd` | yes | Change in USD value of tokens held in the position (liquidity_value_usd) over the last 24 hours: current value minus the value ~24 hours ago. Null until ~24 hours of position history has accrued. |
| `liquidity_value_usd_change_1w` | `number` | `usd` | yes | Change in USD value of tokens held in the position (liquidity_value_usd) over the last 7 days: current value minus the value ~7 days ago. Null until ~7 days of position history has accrued. |
| `uncollected_fees_usd_change_1h` | `number` | `usd` | yes | Change in USD value of uncollected fees (uncollected_fees_usd) over the last 1 hour: current value minus the value ~1 hour ago. Null until ~1 hour of position history has accrued. |
| `uncollected_fees_usd_change_1d` | `number` | `usd` | yes | Change in USD value of uncollected fees (uncollected_fees_usd) over the last 24 hours: current value minus the value ~24 hours ago. Null until ~24 hours of position history has accrued. |
| `uncollected_fees_usd_change_1w` | `number` | `usd` | yes | Change in USD value of uncollected fees (uncollected_fees_usd) over the last 7 days: current value minus the value ~7 days ago. Null until ~7 days of position history has accrued. |
| `tvl_usd_change_1h` | `number` | `usd` | yes | Change in total value locked in the pool (tvl_usd) over the last 1 hour: current value minus the value ~1 hour ago. Null until ~1 hour of position history has accrued. |
| `tvl_usd_change_1d` | `number` | `usd` | yes | Change in total value locked in the pool (tvl_usd) over the last 24 hours: current value minus the value ~24 hours ago. Null until ~24 hours of position history has accrued. |
| `tvl_usd_change_1w` | `number` | `usd` | yes | Change in total value locked in the pool (tvl_usd) over the last 7 days: current value minus the value ~7 days ago. Null until ~7 days of position history has accrued. |
| `breakeven_margin_change_1h` | `number` | `percent` | yes | Change in percent margin to the nearest breakeven price (breakeven_margin) over the last 1 hour: current value minus the value ~1 hour ago. Null until ~1 hour of position history has accrued. |
| `breakeven_margin_change_1d` | `number` | `percent` | yes | Change in percent margin to the nearest breakeven price (breakeven_margin) over the last 24 hours: current value minus the value ~24 hours ago. Null until ~24 hours of position history has accrued. |
| `breakeven_margin_change_1w` | `number` | `percent` | yes | Change in percent margin to the nearest breakeven price (breakeven_margin) over the last 7 days: current value minus the value ~7 days ago. Null until ~7 days of position history has accrued. |

## `derived`

Per-position current-value derived signals computed server-side by the signals sampler from position math: impermanent-loss percentage and percent margin to the nearest breakeven price. Flattened at the top of each position object and referenced bare in CEL rules, e.g. `il_pct > 5`. Omitted when not computable (single-sided entry / no reachable breakeven).

- **Scope** — Emitted **once per position** in the `positions[]` array.
- **Cardinality** — Single object whose keys are this group's metrics.
- **Null handling** — fields with no value are **omitted** from the payload (rather than emitted as `null`).

| Key | Type | Unit | Nullable | Description |
|-----|------|------|----------|-------------|
| `il_pct` | `number` | `percent` | yes | Impermanent loss vs HODLing the entry tokens, as a positive percent (e.g. 3.2 = 3.2% divergence loss). Null when the entry rate cannot be recovered from the deposit legs (single-sided entry). |
| `breakeven_margin` | `number` | `percent` | yes | Signed percent margin from the current price to the nearest breakeven price. Positive = still profitable cushion; negative = underwater. Null when the position has no reachable breakeven. |

## `market`

Global market context shared across all positions in a payload: ETH / BTC indicators and crypto Fear & Greed sentiment.

- **Scope** — Emitted **once per payload** in the `market` object.
- **Cardinality** — Single object whose keys are this group's metrics.
- **Null handling** — fields with no value are **omitted** from the payload (rather than emitted as `null`).

| Key | Type | Unit | Nullable | Description |
|-----|------|------|----------|-------------|
| `eth` | `object` | — | yes | ETH 24h indicators object — fields: price_usd, change_24h_pct, rsi_14h, volatility_24h_ann, high_24h, low_24h, momentum. Opaque in v1; planned sub-group in v1.x. |
| `btc` | `object` | — | yes | BTC 24h indicators object — same fields as `eth`. |
| `sentiment` | `object` | — | yes | Crypto Fear & Greed Index — {fear_greed_index: int, classification: str}. Emitted only when fear_greed_index is set. |
