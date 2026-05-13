# Telegram Bot

The YummyBait Telegram bot is your personal Uniswap V3 / V4 LP copilot.
Add a wallet, and the bot tracks every position it holds — surfacing
P/L, fees accrued, time in range, and AI-generated advice on when to
collect, rebalance, or close.

## What it does

- **Wallet tracking** — add as many Ethereum / Unichain / Base wallets
  as you like; the bot indexes their open Uniswap V3 and V4 positions.
- **Position summaries** — at-a-glance views of P/L, uncollected fees,
  in-range status, and pool health.
- **AI-powered advice** — on demand, the bot bundles every metric in
  the [catalog](../reference/metrics-catalog.md) into a single LLM call
  and returns specific, actionable guidance per position.
- **Out-of-range notifications** — opt-in alerts when one of your
  positions drifts out of its price range.

## Getting started

1. Open [@yummybait_bot](https://t.me/yummybait_bot) on Telegram.
2. Send `/start` to register.
3. Send `/add_wallet 0xYourAddress` to link a wallet.
4. Send `/list_positions` to see what we found.

From there, `/advice` will give you AI guidance for each open position,
and `/help` shows the full command list.

## What the bot does NOT do

- **It does not custody funds.** YummyBait only ever reads on-chain
  data; it never holds keys or signs transactions.
- **It does not execute trades.** When the bot generates a transaction
  (e.g. a "collect fees" intent), it returns an unsigned transaction
  payload — you sign and broadcast it from your own wallet.
- **It is not financial advice.** AI-generated guidance is a tool, not
  a recommendation. Always verify before acting.
