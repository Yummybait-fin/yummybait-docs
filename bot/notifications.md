# Notifications

The bot sends scheduled alerts when something material changes in your
positions. You select **one** alert type with `/alerts` — out-of-range
(the default) or one of the proactive PnL alerts described below.

## Choosing an alert type (`/alerts`)

`/alerts` opens a single-select menu. Each chat has exactly one active
alert type at a time:

- **Out of range** — the default; the lagging alert described below.
- **IL tolerance** — proactive; warns before divergence loss grows.
- **Breakeven** — proactive; warns before profit turns into a loss.
- **Off** — disable all alerts.

Picking **IL tolerance** or **Breakeven** reveals preset threshold
buttons. Every proactive alert includes a short, plain-language
explanation of what triggered it and why it matters, in your bot
language. Existing users keep out-of-range alerts unchanged until they
choose otherwise.

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

## Proactive PnL alerts

Out-of-range is a *lagging* signal — by the time price exits the range,
fees have already stopped and impermanent loss is already accruing. The
proactive alert types warn you **earlier**, while a position is still
profitable and in range. Both are evaluated only on positions that are
currently in range and earning fees.

### IL tolerance

You set an impermanent-loss tolerance (e.g. 1%, 3%, or 5%). The bot
computes the price level(s) that would produce that much divergence
loss for the position's specific range and alerts you when the current
price reaches them — before the loss grows further.

### Breakeven

The **breakeven price** is the pool price at which the position's PnL
crosses zero. Fees earned so far widen this cushion over time. A
breakeven alert fires while the position is still profitable when
**either**:

- **Proximity** — the margin between the current price and the
  breakeven price drops below your threshold, or
- **Velocity** — that margin is closing rapidly (percent of price per
  hour), even if it is still comfortably positive.

### Muting a position

Each proactive alert carries a **🔕 Mute** button that silences the
active alert type for that one position, without affecting your others.

For programmatic delivery of any metric in the
[Metrics Catalog](../reference/metrics-catalog.md) — polled into your own
agent with rules you define — see [Signals](../signals/index.md).
