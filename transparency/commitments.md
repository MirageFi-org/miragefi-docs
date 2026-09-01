---
description: The specific, checkable promises the venue makes, and how each one can be verified.
---

# Commitments

Claims of transparency cost nothing; commitments can be checked. Every promise below states its own verification path. Should any of them fail verification, that is an incident and is treated as one.

## Pricing

* **Every fill is itemised on-chain.** Mid, spread, skew and fee are all carried in the fill event. Check: compare any fill on Blockscout with its receipt.
* **No fill outside the band.** This is a contract invariant rather than a policy. Check: the invariant suite is public, and the explorer flags the band margin on each fill.
* **Execution quality is published, unfiltered.** The distance of every fill from the oracle mid, aggregated each day and downloadable raw. Check: [Execution quality](execution-quality.md), which can be recomputed from events.
* **No payment for order flow, no privileged routing.** For each fill the router settles the best price that can be verified, on-chain. Check: re-derive the venue comparison for any fill from the oracle round and vault state in its event.

## Funds

* **No custody, ever.** Settlement is atomic, and vault inventory is the LPs' property. Check: no contract function moves user assets other than settlement and withdrawal; the audits confirm this.
* **Withdrawals are unconditional.** Whatever the vault state, whatever pauses are active. Check: the invariant suite; withdrawals made during halts appear in the explorer like any other.

## Change control

* **Every parameter change is timelocked and published with a rationale before it executes.** Check: the governance log records proposal, rationale, window and execution across the venue's full history.
* **No retroactive fees.** The fee in force at fill time is the fee you pay. Check: fill events record the fee applied.
* **Settlement logic never changes under an existing deployment.** New logic means a new deployment that users choose to move to. Check: bytecode is verified against tagged source, asserted in CI, and anyone can reproduce the verification.

## Disclosure

* **Listings publish their evidence.** The tier assignment data, the bytecode review findings and the issuer disclosures for each market, before that market opens. Check: the listing record in the explorer.
* **Incidents produce public post-mortems within 14 days.** Check: the incident log; if an incident has no post-mortem, that absence is itself a breach.
* **These documents state risks plainly.** Issuer risk, gap risk and oracle risk sit in every participant's reading path, not in an appendix. Check: read [Risk](../risk/framework.md).

## What is deliberately not promised

Best execution against every venue everywhere, the profitability of any position, or the uptime of convenience services. The promises above are the ones the design can genuinely enforce, and that is why they are the ones that are made.
