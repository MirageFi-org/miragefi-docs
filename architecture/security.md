---
description: How the contracts and services are secured, tested and monitored.
---

# Security programme

The security posture is a consequence of the design: logic that cannot change, a small privileged surface, and a band that limits the blast radius of nearly anything. What the programme has to do is shrink the surface that remains and keep it under constant watch.

## Audits

Before mainnet exposure grows beyond the guarded caps, two independent audits take place: one firm concentrating on economic and pricing logic (the spread formula, share accounting, band enforcement), and a general firm covering the entire contract set. Reports are published without redaction. Any later change touching settlement logic is a new deployment and receives its own review; parameter changes are not reviewed this way, which is a large part of why behaviour is kept in parameters.

## Invariant and fuzz suite

The Foundry suite expresses the protocol's promises as machine-checked properties. It runs in CI on each commit and is fuzzed continuously:

| Invariant | Meaning |
| --- | --- |
| Band safety | Under any state, regime or parameter set the fuzzer can reach, no fill clears outside the oracle band |
| Share monotonicity | A swap never lowers value per share; rounding on mint and burn always favours the vault |
| Withdrawal liveness | `withdraw` succeeds in every vault state, `HALTED` and `RETIRED` included, even while the pause is active |
| Inventory bounds | No amount of vault quoting can push inventory beyond the band |
| Conservation | Value is conserved exactly across trader, vault or maker, and `FeeCollector` on every fill |
| Decimals | Arithmetic holds across the 6/18 decimal split at both extremes of size |

Oracle-failure drills are treated as first-class scenarios: stale rounds, paused feeds, a divergent stream and feed, 25% jumps, and sequencer restarts each have their expected halt behaviour asserted rather than assumed.

## Bug bounty

A public programme runs on an Immunefi-style platform from mainnet onward, with the top tier held back for anything that breaks one of the invariants above. The quote service and SDK are in scope, since a client that misleads a user into signing is a genuine attack even where the chain would enforce correctly.

## Operational security

* Named signers with published thresholds hold the timelock multisig and guardian keys in hardware. Because the guardian can only pause and unpausing is timelocked, a compromised guardian amounts to a denial of quoting rather than a loss.
* No service holds a key that can touch funds. The system's only hot keys belong to makers, who carry their own key risk and whose potential damage is bounded by the band.
* Monitoring raises alerts on band-margin compression, oracle divergence, skew saturation and abnormal fill patterns. The incident runbook always opens with the same step, pause quoting, because pausing is guaranteed to be harmless.

## Disclosure

Vulnerabilities go to security@miragefi.org, which has a published PGP key. Every incident produces a public post-mortem within 14 days, and any change that follows lands through the timelock with that post-mortem as its rationale. The venue's credibility is its [transparency commitments](../transparency/commitments.md) held under pressure, and this page is where that gets tested.
