# Privacy & Data

## What we store

To make the bot work, we link your **Telegram user ID** to:

- The **wallet addresses** you've added.
- Your **language preference** and per-position notification opt-outs.
- Anonymous **usage analytics** (commands invoked, errors, latency).

We store no chat content, no Telegram contact info beyond the user ID,
and no off-chain personal data.

## What we do NOT store

- **No private keys.** The bot is read-only on-chain and never has
  the ability to sign or broadcast transactions.
- **No seed phrases.** Never share one with the bot — no legitimate
  command will ever ask for it.
- **No off-platform IDs.** We don't link your Telegram account to
  anything outside Telegram unless you explicitly opt in (e.g. via a
  webhook subscription).

## On-chain data

All wallet → position data the bot serves is derived from **public
on-chain events**. Anyone can reconstruct it from a Uniswap V3 / V4
node. YummyBait does not surface any private information about your
wallets.

## Removing your data

Delete every wallet via `/list_wallets` and message
`/leave_feedback delete my account` — we'll purge your Telegram user
record on the next maintenance window. (A self-serve `/delete_account`
command is planned.)
