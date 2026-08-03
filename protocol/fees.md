---
description: What the protocol charges, who pays it, and where the money goes.
---

# Fees

MirageFi charges explicit fees at the point where value changes hands, and each one is itemised on the ticket. There is no hidden markup and no payment for order flow.

| Fee | Rate (initial) | Paid by | When |
| --- | --- | --- | --- |
| Vault swap fee | 2 bps of notional, included in the quoted all-in price but shown separately | Trader | Per fill |
| Vault spread share | 10% of the spread the vault realises | LPs (netted out of spread revenue) | Per fill |
| RFQ settlement fee | 2 bps of notional | Trader | Per RFQ fill |
| Deposit and withdrawal | None | | |

A token-to-token swap in two legs pays the fee on each leg. Every rate is a `ParamController` value, changeable only through the timelock with a published rationale, and never applied retroactively: the fee that applies is the fee in force at the moment of the fill.

## The quote is the invoice

Before signing, every ticket shows mid, spread and fee as separate figures in bps and USDG, and the fill event writes the same breakdown on-chain. When a fee changes through the timelock, the change appears in the governance log before it appears on any ticket.

## Where fees go

Fees accumulate in the `FeeCollector` contract and pay for audits, the bug bounty, oracle and infrastructure costs, and operations. Once the governance module assumes parameter control, it directs the allocation. See [Parameter governance](../transparency/governance.md).

## Economics at scale

Assuming 10M USDG of average daily volume and an average all-in half-spread of 12 bps:

* Swap and RFQ fees: 10M × 2 bps × 365 ≈ 730K USDG per year.
* Spread share: 10M × 12 bps × 10% × 365 ≈ 438K USDG per year.

That is roughly 1.2M USDG per year at volumes that are modest relative to the growth of the asset class. Running the same arithmetic at 50M daily gives about 5.8M USDG per year. See [Business model](../resources/business-model.md).
