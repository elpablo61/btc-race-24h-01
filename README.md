# btc-race-24h-01 — Dip Rider

[![make.fun · btc-race-24h-20261117-0603](https://make.fun/badge/btc-race-24h-20261117-0603.svg)](https://make.fun/bounty/btc-race-24h-20261117-0603?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-01)
[![rank](https://make.fun/badge/btc-race-24h-20261117-0603/elpablo61/btc-race-24h-01/rank.svg)](https://make.fun/bounty/btc-race-24h-20261117-0603/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-01)
[![reward](https://make.fun/badge/btc-race-24h-20261117-0603/elpablo61/btc-race-24h-01/reward.svg)](https://make.fun/bounty/btc-race-24h-20261117-0603?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-01)
[![license: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

A cbBTC trading bot for the
[make.fun btc-race-24h-20261117-0603 bounty](https://make.fun/bounty/btc-race-24h-20261117-0603):
race a bot live for 24 hours on Coinbase Wrapped BTC (cbBTC) minute bars,
paper $1,000 start, long only, ranked by net PnL after fees.

## Strategy — Dip Rider

BTC mean-reverts at the 1–2% scale on the dev window (dips more than 0.3%
below the 120-minute mean gain +0.18% in the next hour, 71% of the time;
rallies fade). A round trip costs ~37 bps in fees + slippage + price
impact, so only deep dips with a wide target clear the cost. The bot:

- **Entry:** close falls 1.20% or more below the highest high of the
  previous 240 bars (4h).
- **Exit:** close recovers 1.50% above the entry close, **or** falls
  0.75% below it (stop-loss), **or** 480 bars (8h) pass without either.
- **Cooldown:** 30 bars after every exit, so it never re-enters the same
  whipsaw.
- **Size:** full weight room (100% of equity) on every entry, at most one
  open position.
- **Late-window insurance:** if no round trip has completed by bar 1200
  (last 4h of the race), the entry dip loosens to 0.60% so the bot never
  ends the race with zero trades.

Trend and breakout variants were tried first and rejected on the dev
window: they churn 16–68 trades and lose $23–106 to fees. This bot
trades 3–4 times in 24h and keeps the fees small. Parameters sit in the
middle of a profitable neighbourhood (dip 1.10–1.30%, target 1.25–1.75%,
stop 0.60–0.75% all positive on the dev window), not at a lone spike.

## Dev grade (frozen 24h window, official local grader)

- **Net PnL: +$13.65** on $1,000 paper (dev gate: > $0 — passed)
- 7 trades, max drawdown 1.64% (limit 25%), constraints compliant

```sh
node cli/makefun.mjs grade-local --kit . --bot . --driver process --json
```

(run from the bounty kit; the kit is not part of this repo)

## Files

- `main.ts` — the whole bot (Node 22, TypeScript, integer math only).
- `vendor/makefun-bars.ts` — the official bars-replay shim (byte-for-byte
  copy from the bounty kit, excluded from authorship counts).

No network, no wall clock, no files, no randomness: the same bars always
produce the same orders.

## Referral

Built as [@pablo-crypto](https://make.fun/r/vky0ybyt) on make.fun.
