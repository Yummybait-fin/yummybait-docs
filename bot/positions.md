# Positions

```
/list_positions
```

Returns up to the latest five **open** positions per active wallet.
Each row shows:

- **Token pair** — e.g. `USDC/WETH`.
- **Pool fee tier** — 0.01% / 0.05% / 0.3% / 1%.
- **In-range status** — whether the current pool price sits between
  the position's lower and upper bounds.
- **Liquidity value** — current USD value of the tokens held.
- **Uncollected fees** — fees accrued but not yet collected, in USD.
- **P/L vs entry** — net profit/loss in USD relative to the value at
  position open.
- **P/L vs HODL** — comparison vs simply holding the entry tokens
  (captures impermanent-loss impact).

## How positions are discovered

The bot reads on-chain Uniswap V3 (`NonfungiblePositionManager`) and
Uniswap V4 (`PositionManager`) events, indexed in real time. As soon as
a wallet you've added opens or modifies a position, it appears in the
next `/list_positions` call.

## Position state

Liquidity, ticks, and uncollected fees are derived from event history
— no values are cached. This means every refresh reflects the current
state of the chain (within indexer lag, typically <1 block).
