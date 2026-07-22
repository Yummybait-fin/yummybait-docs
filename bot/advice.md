# AI Advice

```
/advice
```

Bundles every metric in the [Metrics Catalog](../reference/metrics-catalog.md)
for each of your open positions, sends them to an LLM along with global
market context (ETH/BTC indicators, Fear & Greed), and returns
per-position recommendations.

## What the advisor sees

For every open position, the LLM receives:

- **Position core** — ID, pair, chain, P/L vs entry, P/L vs HODL,
  liquidity value, uncollected fees, gas cost estimate, age, in-range
  flag, pair RSI(14).
- **Pool snapshot** — TVL, 24h volume, 24h fees, fee tier, tx count,
  AI risk classification with rationale, recent open/close activity.
- **Similar pools** — DefiLlama matches with the same pair / fee tier
  on other protocols, used as a yield benchmark.
- **Range analysis** — distance to bounds, annualized pair volatility,
  random-walk ETA to each bound, daily fee run-rate, days to
  breakeven, and a `will_likely_exit_before_breakeven` flag.
- **Market context** — ETH and BTC 24h indicators, Fear & Greed Index.

The full schema is documented in the [Metrics Catalog](../reference/metrics-catalog.md).

## Acting on advice

Every recommendation comes with one or more inline buttons to **prepare
a transaction** for the suggested action — collect fees, mint a new
position with adjusted bounds, or close the position.

Tapping a button asks the bot to build an unsigned transaction. The bot
returns the calldata and asks you to sign and broadcast from your own
wallet. **YummyBait never holds keys and never broadcasts on your
behalf.**

## Caveats

- **Not financial advice.** The advisor is a tool, not a recommendation.
  Verify every action before signing.
- **Latency.** A single `/advice` call can take 10–30 seconds depending
  on how many positions you hold and current LLM load.
- **Stale on-chain state.** Indexer lag is typically <1 block, but if
  you've just made a transaction, give it a moment to settle before
  asking for advice.
