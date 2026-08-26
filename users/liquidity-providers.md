---
description: "A working guide to funding vaults: how to deposit, earn, monitor and exit."
---

# For liquidity providers

Providing liquidity on MirageFi means funding one or more [anchor vaults](../protocol/anchor-vaults.md) and earning the spread on their fills. It resembles underwriting a market-making book far more than farming: the return is genuine flow revenue, and the risks are inventory risks, set out in full in [Liquidity provision](../protocol/liquidity-provision.md). Start there; this page covers the practical steps in order.

## Onboarding

Taking part as an LP requires an `LP` attestation, which at launch is issued to professional clients through the same flow used for trading eligibility. Because the vaults you fund hold Stock Tokens, the [issuer-risk disclosure](../risk/issuer.md) forms part of onboarding.

## Choosing markets

Every vault is a separate decision, and the [trade explorer](trade-explorer.md) publishes the data needed to underwrite it:

* realised spread revenue and volume by regime, from listing onward,
* the current inventory mix and its skew relative to the band,
* the market's tier parameters and caps,
* the halt history, with reasons.

Tier A markets turn over more at tighter spreads; Tier C collects wider spreads on thinner flow that gaps more. There is deliberately no pooled product that makes the choice for you.

## Depositing

Deposit USDG, the market's token, or both; the vault prices the deposit at the guarded mid and mints shares in return. There are no lockups and no deposit fees. Throughout the guarded launch each vault carries a TVL cap, shown on its page; a full vault takes no further deposits until the cap is raised through the timelock.

## While deposited

Value per share grows with every fill, and your position page shows it live alongside your share of the current inventory. Points worth keeping an eye on:

* **Skew.** A vault sitting at the edge of its band is one-sided and earns less; skew that never reverts to the mean means flow in that market is persistently one-directional.
* **Regime mix.** Fills in the closed regime carry the gap risk premium you are being paid for; the explorer shows the share of your vault's fills made at the weekend.
* **Halts.** While halted, a vault earns nothing, and your withdrawal rights are untouched.

## Exiting

Withdrawing burns shares for a pro-rata, in-kind mix of the USDG and tokens the vault holds at that moment, in every vault state, without exception. Plan for two consequences:

* Depending on the vault's mix at exit, you may come away with more token and less USDG than you put in (or the reverse). Swapping the token leg back is a normal trade at normal cost.
* An exit during the `CLOSED` regime leaves you holding inventory you cannot cheaply flatten until the underlying market opens. Exiting is always possible; a well-timed exit is still worth timing.

## Taxes and accounting

Share value accrues price movement and spread revenue together, and an in-kind withdrawal counts as a disposal event under most regimes. The explorer exports the full history of each position as CSV; how to interpret it is a matter between you and your adviser. Nothing in these documents constitutes tax advice.
