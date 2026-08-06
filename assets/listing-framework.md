---
description: The criteria an asset has to satisfy before its market opens, and the tier system that fixes its parameters.
---

# Listing framework

Listing works as a checklist rather than a judgement call. Either an asset satisfies the criteria for a tier or it does not; the evidence is published alongside the listing, and the parameters follow mechanically from the tier.

## Requirements for any listing

Whatever its tier, every market must satisfy all of the following:

| Requirement | Why |
| --- | --- |
| A live Chainlink Data Feed on chain 4663 for the exact token | Anchor pricing values the token that sits in the vault, never a wrapper or a derived rate |
| Data Streams coverage with market status, or an equivalent session signal | An authoritative session source is required by the [regime engine](../protocol/trading-regimes.md) |
| A regulated issuer with published terms and 1:1 backing | Claims on real assets are listed; synthetics are not |
| A bytecode review of the deployed token | Pause, freeze, blacklist and forced-transfer roles are discovered before listing rather than after, and the finding is published |
| ERC-20 with standard semantics | No fee-on-transfer, and no rebasing apart from ERC-8056-style multipliers that the feed reflects |

If an asset fails any one requirement it is not listed at any tier. No discretionary override exists.

## Tiers

Every market parameter is set by the tier. Initial values:

| Parameter | Tier A | Tier B | Tier C |
| --- | --- | --- | --- |
| Base half-spread | 10 bps | 20 bps | 40 bps |
| Max skew term | 15 bps | 25 bps | 50 bps |
| Oracle band | 75 bps | 150 bps | 300 bps |
| Regular clip | 50,000 USDG | 20,000 USDG | 5,000 USDG |
| Daily volume cap (guarded launch) | 2M USDG | 750K USDG | 200K USDG |
| Vault TVL cap (guarded launch) | 5M USDG | 2M USDG | 500K USDG |

Tier assignment takes into account the liquidity of the underlying (average daily volume and typical spread on the primary market), how often the feed updates, and how the token is distributed on-chain. Each market's assignment is published together with the evidence behind it; a reassignment is a timelocked parameter change like any other.

* **Tier A**: index ETFs and mega-caps (SPY, QQQ, AAPL, MSFT, NVDA). Deep books in the underlying and dense feed updates.
* **Tier B**: liquid single names beyond the mega-caps.
* **Tier C**: the long tail. Rather than being excluded outright, wide underlying spreads and sparser updates are priced through the wider band and spread, because serving the long tail properly is a design goal. Caps keep the exposure honest.

## Delisting

When an asset stops meeting the listing requirements, its market retires: the feed is discontinued, the issuer winds the token down, or a change at the bytecode level introduces a disqualifying control. Retirement is timelocked, is published together with its rationale, and puts the vault into withdrawals-only mode. See [Corporate actions and dividends](../protocol/corporate-actions.md).

## Adding new asset classes

Tokenized treasuries and gold go through the same checklist, each with one addition: NAV-type assets need a NAV-consistent feed and trade inside a tight band around it, while bridged assets need the bridge review set out in the [Asset roadmap](asset-roadmap.md).
