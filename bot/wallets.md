# Wallets

A **wallet** is an Ethereum-format address (`0x…`, 42 chars) that the
bot watches on your behalf. You can add as many as you want.

## Adding a wallet

```
/add_wallet 0xYourAddress
```

The bot validates the address checksum, links it to your Telegram
account, and immediately scans for open Uniswap V3 / V4 positions on
every supported chain.

**Supported chains today:** Ethereum, Unichain, Base.

## Listing wallets

```
/list_wallets
```

Shows every wallet you've added, with two inline buttons per row:

- **Toggle status** — pause tracking without removing the wallet.
  Paused wallets don't appear in `/list_positions` or `/advice`, and
  don't trigger notifications.
- **Delete** — remove the wallet entirely.

## Privacy

Linking a wallet to your Telegram account is private to you. YummyBait
does not publish or expose the link between Telegram users and
addresses. See [Privacy & Data](privacy.md).
