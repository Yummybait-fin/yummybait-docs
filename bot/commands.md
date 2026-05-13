# Commands

Every command the bot understands.

| Command | What it does |
|---------|--------------|
| `/start` | Register your Telegram account with YummyBait. Required once before anything else. |
| `/help` | Show the full command list. |
| `/info` | Short description of what the bot does. |
| `/tour` | Replay the onboarding tour. |
| `/add_wallet` | Add an Ethereum-format wallet address. The bot tracks all Uniswap V3 / V4 positions it holds. |
| `/list_wallets` | List your linked wallets, with toggles to enable/disable or remove each one. |
| `/list_positions` | Show your most recent open positions across every active wallet. |
| `/advice` | Run AI advisor on all open positions and return per-position guidance. |
| `/leave_feedback` | Send feedback directly to the YummyBait team. |
| `/language` | Switch the bot's display language. |

## Inline buttons

Many bot messages include inline buttons:

- **Wallet management** — delete or pause/resume a wallet from
  `/list_wallets`.
- **Advice actions** — when `/advice` returns a recommendation, you
  can ask the bot to **prepare a transaction** for it (mint, collect,
  or close). The bot returns an unsigned transaction; you sign and
  broadcast from your own wallet.
- **Out-of-range opt-out** — when an OOR notification fires, you can
  silence further alerts for that position.
