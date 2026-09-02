---
description: How parameters get changed, who is able to change them, and the limits placed on that power.
---

# Parameter governance

Every tunable quantity in MirageFi lives as a `ParamController` value: spreads, multipliers, clips, caps, bands, fees, oracle adapters, eligibility issuers and tier assignments. Governance is the process by which those values change, and its scope is deliberately narrow: it can tune the venue, and nothing beyond that.

## The process

1. **Proposal.** The foundation multisig puts forward a change together with a written rationale: what is changing, why, and the supporting data (typically the [execution quality](execution-quality.md) record or a listing review).
2. **Timelock.** The proposal sits public and pending for the timelock period (48 hours for routine parameters; 7 days for oracle adapters, eligibility issuers and fee changes). Anyone affected can see it coming, and an LP who objects to a pending change can withdraw before it takes effect, with no conditions attached.
3. **Execution.** The change is executed on-chain and emits the old value, the new value and the proposal hash. The governance log in the [trade explorer](../users/trade-explorer.md) is the venue's full parameter history.

## The emergency path

The guardian can pause quoting and settlement at once, because a live incident cannot wait 48 hours. The asymmetry is what makes this safe: pausing is immediate and harmless (withdrawals are unaffected), whereas unpausing and every substantive change pass through the timelock. Every guardian action produces an incident note, and a pause without explanation is itself a breach of the [commitments](commitments.md).

## What governance cannot do

| Cannot | Because |
| --- | --- |
| Move or freeze user funds | No contract contains such a function |
| Gate or delay withdrawals | Withdrawal is protected by invariant in every state |
| Change settlement logic | The logic is immutable; new logic means a new deployment that users opt into |
| Apply fees retroactively | Fees are read at fill time, for that fill |
| Bypass the band | There is no override path for the band check |
| Act instantly on substance | Apart from pause, everything is timelocked |

## Who governs

At present, the foundation multisig, with named signers and published thresholds (see [Corporate structure](../compliance/corporate-structure.md)). The planned governance module inherits precisely the powers listed above and the same prohibitions; it changes who proposes, not what a proposal is able to do. Governance is not a yield product: the only thing at stake in a MirageFi parameter vote is how good the venue is.
