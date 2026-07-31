---
description: How dividends, splits and other corporate actions pass through a market without breaking it.
---

# Corporate actions and dividends

Corporate actions are where naive RWA venues quietly leak money: a 10-for-1 split reprices a token overnight, and a pool that has not been told hands the difference to the first arbitrageur who is awake. MirageFi gets most of the answer from the asset design and the oracle, and covers what remains with a halt.

## Dividends: nothing to do, by design

Robinhood Stock Tokens implement ERC-8056: `uiMultiplier()` reports how many underlying shares each token represents, and dividends are reinvested by raising the multiplier instead of being paid out as a separate distribution. The Chainlink price already incorporates the multiplier. For a trading venue, every consequence is favourable:

* No dividend token exists to be claimed, swept or allocated between LPs and traders.
* There is no ex-dividend price cliff for the vault to get wrong; value accrues continuously into the token and into its feed.
* The vault accounting requires no dividend logic whatsoever. An LP's share value grows with the multiplier in the same way as with any other price move.

The trade explorer shows holdings in both token and share terms by reading `uiMultiplier()` next to the price.

## Splits, mergers and delistings: halt, adjust, resume

Around a split, a merger, a symbol change or a similar event, the Chainlink feed sets `oraclePaused()`. That flag moves the market to [`HALTED`](trading-regimes.md) automatically: the vault stops quoting, no RFQ settles inside the market, and deposits and withdrawals carry on as normal.

Once the feed resumes, its price and multiplier already reflect the action, and the market unhalts with quoting anchored on the post-event price. The vault held the same tokens the whole time, so there is no inventory to adjust; a split changes the number the feed reports, not what the vault owns.

| Event | During | After |
| --- | --- | --- |
| Dividend | Nothing; the multiplier and the price accrete | Nothing |
| Split or reverse split | `HALTED` for as long as `oraclePaused()` is set | Resume on the adjusted feed; inventory untouched |
| Merger, acquisition | `HALTED` | Resume, or [retirement](#delisting-and-retirement) if the token is being wound down |
| Delisting of the underlying | `HALTED` | Retirement |

## Delisting and retirement

When an underlying is delisted or the issuer winds down a token, the market is moved to `RETIRED` through the timelock: quoting stops for good and only withdrawals remain. LPs take out their pro-rata inventory in kind and, if they wish, deal directly with the issuer's redemption process; the protocol never takes a position in a wind-down. The reason for the retirement is published in the governance log, as with any parameter change.

## The residual risk

The design relies on the feed pausing before any stale price can be hit. Chainlink's equity feeds set `oraclePaused()` around corporate actions, and the [staleness guard](../risk/oracles.md) covers the case of a feed that misbehaves rather than pausing: a feed that falls silent triggers the staleness halt by itself. The window that is left, a feed confidently publishing wrong prices around an event, is the same oracle risk every fill carries, and the [band](pricing-and-spreads.md) limits the damage from any one fill to the width of the band.
