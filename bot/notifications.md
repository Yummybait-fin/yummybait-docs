# Notifications

The bot sends scheduled alerts when something material changes in your
positions. v1 ships with a single notification type; more are planned.

## Out-of-range (OOR) alerts

When one of your positions drifts **out of its price range**, the bot
sends a Telegram message naming the position, the pool, and the
direction (price moved above or below the range).

OOR positions are still earning yield on whichever side of the range
they're on, but they stop earning fees from active trading. The alert
gives you a chance to:

- Collect accumulated fees and rebalance into a new range, or
- Wait — if you expect price to mean-revert into your range.

### Opting out per position

Each OOR alert has an inline **Ignore future OOR for this position**
button. Tapping it silences further alerts for that one position
without affecting any others.

You can re-enable later from the same message thread (a `Notify me
again` button appears once the position is silenced).

## Coming soon

- Fee-collection thresholds (alert when uncollected fees exceed a
  configurable USD amount).
- Risk-level changes on your positions' pools.

For lower-latency, programmatic delivery of any signal in the
[Metrics Catalog](../reference/metrics-catalog.md), see
[Webhook Signals](../webhooks/index.md).
