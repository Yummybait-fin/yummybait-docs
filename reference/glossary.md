# Glossary

**APY** — Annual Percentage Yield. The yield a position would earn
over a year if current conditions held.

**Composition** — A coarse classification of a pool's token pair:
`stable_stable`, `stable_major`, `stable_alt`, `major_major`,
`major_alt`, `alt_alt`. Used to bucket risk.

**Concentrated liquidity** — Uniswap V3 / V4's model where LPs choose
a price range to provide liquidity in. Earns more fees per dollar
inside the range, zero outside it.

**Fee tier** — The fraction of every swap that goes to LPs, expressed
in basis points (`100` = 0.01%, `3000` = 0.3%, `10000` = 1%).

**HODL P/L** — Profit/loss compared to simply holding the entry
tokens. Captures the cost of impermanent loss.

**Impermanent loss (IL)** — The opportunity cost of providing
liquidity vs. holding: when the pool's pair ratio changes, an LP ends
up with a different token mix than they started with, often worth less
in USD than just holding.

**In-range** — Whether the current pool price sits between the
position's lower and upper price bounds. Out-of-range positions earn
no fees.

**LP** — Liquidity Provider. You, when you open a Uniswap position.

**OOR** — Out-Of-Range. Used in the bot for the alert that fires when
your position's range stops covering the current price.

**Pool** — A Uniswap market for one specific token pair at one
specific fee tier. `USDC/WETH 0.05%` and `USDC/WETH 0.3%` are
different pools.

**Position** — Your individual stake in a pool, identified by an NFT
(Uniswap V3) or a token ID under the V4 PositionManager.

**Range** — A position's lower and upper price bounds. Liquidity is
only active when price sits inside this range.

**RSI** — Relative Strength Index. A momentum indicator (0–100). For
the bot, `pair_rsi_14` is computed on the pool's exchange-rate series,
not on a single asset price.

**TVL** — Total Value Locked. The USD value of all tokens currently
deposited in a pool.
