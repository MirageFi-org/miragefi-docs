---
description: The market conditions in 2026 that make an RWA-native swap venue viable.
---

# Why now

Before a venue like MirageFi could work, three conditions had to hold: tokenized stocks had to exist as ordinary on-chain assets with dependable pricing, there had to be real demand to trade them, and the venue designs already in use had to leave a gap worth filling. Today all three hold.

## Tokenized stocks are now a real asset class

Transferable, non-stablecoin RWAs on public chains grew from roughly $7.9B at the end of 2024 to about $21B at the start of 2026, and stand at about $38.7B today. Within that total, tokenized stocks are the category growing fastest.

| Category (rwa.xyz, 28 Aug 2026) | On-chain, transferable value | Notes                                                                                   |
| ------------------------------- | ---------------------------- | --------------------------------------------------------------------------------------- |
| Stablecoins                     | ~$303B                       | The layer on which settlement happens                                                   |
| Tokenized US Treasuries         | ~$16.0B                      | USYC $2.9B, BUIDL $2.8B, USDY $2.2B, BENJI ~$2.4B                                       |
| Tokenized credit                | ~$7.5B distributed           | Around $35B once non-transferable "represented" assets such as Figure HELOCs are included |
| Commodities                     | ~$3.1B                       | XAUT, PAXG                                                                              |
| **Tokenized stocks**            | **~$2.6B**                   | Ondo Stocks past $1B TVL; xStocks past $25B cumulative volume; Robinhood, Coinbase, Binance |
| Private equity and VC           | ~$1.6B                       |                                                                                         |
| Real estate                     | ~$175M                       |                                                                                         |

On their own, tokenized stocks rose from about $424M in mid-2025 to about $2.59B in August 2026. Depending on whether non-transferable assets are included, figures differ by around 20 percent from one source to another.

## Issuance is solved. Trading is not

Nearly all of today's tokenized-stock volume takes place on the order books of centralised exchanges: custodial, off-chain and opaque. The on-chain alternatives are generic AMMs built for crypto-native pairs, and they handle RWAs badly in ways that are easy to predict:

* **Reserve-ratio pricing drifts.** A constant-product pool does not know what the asset is worth. Tokenized stocks have traded at material premiums to the underlying on thin books, and the launch-week dislocations on tokenized-equity pools in 2025 are on record. See [Lessons from RWA trading](../risk/lessons.md).
* **LPs are adversely selected.** Each move in the reference price on the underlying exchange is arbitraged against the pool before the curve adjusts. When an asset's price is set somewhere else, passive curve liquidity amounts to a subsidy for arbitrageurs.
* **Nights and weekends are unpriced.** In regular session, equity markets are open for about 32 hours a week. A pool that quotes the same spread on Saturday as on Tuesday misprices gap risk about 80 percent of the time.
* **Corporate actions break pools.** Splits and reverse splits reprice a token overnight, and a pool that is unaware of them gives away the difference.

Minting and redeeming against the issuer is exit liquidity, not a market. A venue that treats RWAs as what they are, assets with an authoritative external price, defined sessions and corporate actions, has not yet existed on-chain at scale.

## Robinhood Chain removes the last blocker

Robinhood Chain went live on mainnet on 1 July 2026. It is the only L2 on which a regulated broker issues 1:1-backed tokenized stocks as plain ERC-20s, alongside Chainlink 24/5 price feeds and Data Streams that carry market status, sub-cent transactions, ERC-4337 account abstraction and permissionless contract deployment. Assets, pricing and users are all in one place; what is missing is the RWA-native venue. See [Why Robinhood Chain](../chain/why-robinhood-chain.md).

## The incumbents are pointed the other way

* Centralised books (Kraken xStocks, Bybit, Binance) keep volume custodial and off-chain, which is precisely what tokenization was supposed to end.
* Ondo Global Markets reaches US exchange liquidity through its own broker pipe: the pricing is deep, but it is available only through the issuer and only while US markets are open.
* In July 2026 Uniswap v4 introduced permissioned pools, which settles who may trade but not how RWAs should be priced.

A non-custodial venue that is oracle-anchored and session-aware complements all of these and competes with none of their strengths. That is the position MirageFi holds. See [Design principles](design-principles.md).
