---
description: Every material risk the venue carries, with its owner and its mitigation named.
---

# Risk framework

Listing real-world assets means carrying real-world risks. The honest response is to list them, say who bears each one, and write down the mitigation. That list is this section. If a risk belongs here and is missing, that is a documentation bug; report it.

## The map

| Risk | Borne by | Mitigation | Detail |
| --- | --- | --- | --- |
| Oracle failure or manipulation | LPs, traders | The guard stack, staleness bounds, cross-checks, the band, halts | [Oracles and market sessions](oracles.md) |
| Weekend and overnight gaps | LPs | Spread multipliers and clip reductions in the closed regime | [Trading regimes](../protocol/trading-regimes.md) |
| Issuer credit and freeze rights | Anyone holding a Stock Token | Disclosure, bytecode review, tier caps, reporting of single-issuer exposure | [Issuer risk](issuer.md) |
| Sequencer outage or censorship | Traders mid-flow, LPs | Recovery grace, forced inclusion through L1, halts | [Sequencer and chain risk](sequencer-and-chain.md) |
| Contract bugs | Everyone | Audits, the invariant suite, a bounty, guarded caps, minimal upgradeability | [Security programme](../architecture/security.md) |
| Adverse or toxic flow | LPs | Inventory bands, skew pricing, one-sided quoting, clips | [Anchor vaults](../protocol/anchor-vaults.md) |
| Quote-asset depeg | Everyone | A native MiCA-regulated issuer; disclosed as a concentration | [Quote asset: USDG](../assets/usdg.md) |
| Parameter error | LPs, traders | The timelock, a published rationale, the band as backstop, guarded caps | [Parameter governance](../transparency/governance.md) |
| Maker misbehaviour | Takers | Signature verification, expiry, nonces, the band, revocation of attestations | [RFQ settlement](../protocol/rfq.md) |
| Regulatory change | The venue | Boundary enforcement that a parameter can tighten, access gated by jurisdiction | [Compliance model](../compliance/model.md) |

## The band is the common backstop

The oracle band is where most of the failures above terminate. Whatever breaks upstream (a bad parameter, a compromised maker key, a misrouted quote), no fill can clear further from the last guarded oracle price than the band width. That turns a long list of tail scenarios into a bounded cost per fill, and it is what gives the guarded launch caps their meaning: worst-case exposure is a calculation, not a hope.

## What the venue does not protect against

Stating the residual plainly is part of the framework:

* **Market risk.** Buying tokenized NVDA means owning NVDA exposure. The venue prices transactions; it does not insure positions.
* **Issuer insolvency.** Tier caps and disclosure bound the exposure and make it visible; they do not eliminate it.
* **A correct oracle reporting a violent market.** Halts fire on staleness, pauses and implausible jumps, never on genuine volatility. A crash prices through the venue exactly as it prices through the underlying.

## How this section is maintained

Every incident, wherever it falls on this map, gets a published post-mortem and, where justified, a parameter or design change through the timelock. The [Lessons from RWA trading](lessons.md) page records the industry's incidents alongside this venue's answer to each; the intent is that MirageFi's own record is kept to the same standard.
