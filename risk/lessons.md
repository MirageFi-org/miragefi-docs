---
description: The lessons of the first years of tokenized-asset trading, and where each one is built into the venue.
---

# Lessons from RWA trading

On-chain rails change settlement; they do not change market structure. The record from 2020 to 2026 shows exactly what happens when tokenized assets trade on venues that ignore what those assets are. MirageFi is built around that record, not in defiance of it.

## Case studies

### FTX and CM-Equity tokenized stocks, 2020 to 2022

| | |
| --- | --- |
| What happened | Through a custodial arrangement with CM-Equity, FTX offered tokenized stocks. After FTX collapsed in November 2022, holders' "tokenized stocks" turned out to be claims in a bankruptcy, and the backing arrangements proved opaque. |
| Lesson | The venue's custody is the risk. A tokenized share held at a custodial exchange is no better than the exchange holding it. |
| In MirageFi | The venue takes custody of nothing. Assets sit in user wallets or in immutable vault contracts from which LPs can withdraw in kind in every state. Nothing is held at the venue, so nothing at the venue can be lost. |

### Tokenized-equity launch dislocations, July 2025

| | |
| --- | --- |
| What happened | During the first weeks of tokenized stocks on Solana, thin DEX pools allowed tokens to trade at large premiums to the underlying, in some episodes at multiples of fair value. Pool prices were set by reserves, and nothing tied them to the reference price. |
| Lesson | Pricing by reserve ratio holds no view on fair value. On a thin book it will quote any number. |
| In MirageFi | Quotes are constructed outward from the Chainlink mid, and the band makes it a contract-level invariant that no fill clears further than the band width from the reference price, no matter how thin the market. |

### The standing cost of curve liquidity on reference-priced assets

| | |
| --- | --- |
| What happened | Not a single incident but a structural drain: on every reference-price move, LPs in constant-product RWA pools are arbitraged, a cost the literature names loss-versus-rebalancing. Where an asset is repriced continuously on an external exchange, a passive curve transfers LP value to arbitrageurs systematically. |
| Lesson | When an asset is priced elsewhere, the venue must consume that reference price rather than rediscover it. |
| In MirageFi | Anchor vaults quote around the oracle, so what LPs earn is the spread on real flow, not whatever remains after arbitrage. Rebalancing is paid for explicitly through skew rather than leaked. |

### Edel Finance and wGOOGLx, July 2026

| | |
| --- | --- |
| Loss | About $403K of bad debt |
| What happened | The Chainlink oracle for tokenized Google stock was correct. What was trusted was a wrapper token's exchange rate, which had been manipulated 78 times higher, and the value derived from it. |
| Lesson | Price the exact token you hold. Every derived rate is an attack surface. |
| In MirageFi | `OracleRouter` is configured with the feed for the token in the vault and with nothing else. Wrappers, vault shares and derived rates are never listed and never priced. |

### Weekend gaps and 24/7 tokens

| | |
| --- | --- |
| What happened | Tokens that trade 24/7 on top of underlyings that trade 32 hours a week drift over the weekend and gap at the open. Venues that quoted weekend hours at weekday spreads left their LPs on the wrong side of every Monday morning; venues that closed simply gave those hours back to custodial books. |
| Lesson | Sessions are a property of the asset, and a venue has to price them. |
| In MirageFi | When the underlying is closed, the regime engine widens spreads and shrinks clips, discloses this on the ticket, and halts on the oracle's own market status instead of a hard-coded calendar. |

## The common thread

Every one of these failures traces to one of three sources: custody held at the venue, prices cut off from the reference, or values derived rather than fed. MirageFi removes all three by design:

1. **No custody**, ever: settlement is atomic and withdrawal is in kind.
2. **No price without an anchor**: every fill is bounded against the guarded Chainlink mid.
3. **No derived values**: the exact token with its own feed, or no listing at all.

The risks that remain (issuer, gaps, sequencer, contracts) are real, and the rest of this section documents each of them with its mitigation.
