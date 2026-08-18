---
description: The whole system, both on-chain and off, and how trust is divided between its parts.
---

# System overview

MirageFi consists of a handful of immutable contracts on Robinhood Chain, wrapped in stateless services whose only purpose is convenience. One rule shapes the entire design: **the chain enforces, services only assist.** Any service can vanish and every guarantee still stands; the loss is convenience, never funds or fairness.

## The pieces

```
          Traders, LPs, Makers, Integrators
                        │
        ┌───────────────┼────────────────────┐
        │               │                    │
   Platform UI     Quote service        Maker gateways
   (Next.js)       (stateless)          (RFQ streaming)
        │               │                    │
        └───────────────┼────────────────────┘
                        │  signed transactions only
   ┌────────────────────▼─────────────────────────┐
   │              Robinhood Chain                  │
   │                                               │
   │  SwapRouter ── AnchorVaults ── RfqSettlement  │
   │       │              │               │        │
   │  EligibilityRegistry │        FeeCollector    │
   │       │         OracleRouter                  │
   │  ParamController (timelock)                   │
   └───────────────────────────────────────────────┘
                        │
        Indexer (Ponder / Envio) ── Trade explorer,
        execution-quality dashboards, keeper service
```

## On-chain

[Smart contracts](smart-contracts.md) lists the full contract set and the single job each one performs. Across all of them, these design properties hold:

* **Immutable logic, timelocked parameters.** Settlement and vault logic never upgrades; the only way behaviour changes is through `ParamController` values behind the timelock, each accompanied by a published rationale.
* **One oracle door.** Prices reach contracts only through `OracleRouter` and its guards; no contract reads one any other way.
* **One eligibility door.** Whether an address may act is decided only by the `EligibilityRegistry`; no contract makes that call itself.
* **Pause can only stop.** Quoting and settlement can be halted by the guardian; withdrawals cannot be blocked and funds cannot be moved by anyone.

## Off-chain

Three services, none of which holds state that touches funds:

* **Quote service**: runs router pricing in simulation and gathers maker quotes for the UI and API. It has no power to change a fill, because the router re-derives or re-verifies everything on-chain. See [Off-chain services](off-chain-services.md).
* **Keeper service**: triggers regime transitions and halt conditions. The functions are permissionless, the reference implementation is open source, and anyone may run a keeper.
* **Indexer**: turns chain events into the trade explorer and execution-quality datasets. A faulty indexer can display things wrongly; it can never settle them wrongly.

## Trust map

| Party | Must be trusted for | Cannot do |
| --- | --- | --- |
| Chainlink | Accurate prices and market status, inside the guard stack | Move funds; the band and halts bound the damage of a bad price |
| Robinhood (sequencer) | Ordering and liveness | Steal funds; forced inclusion bounds censorship |
| Robinhood Assets (issuer) | Stock Token terms and backing | Touch the venue's contracts; see [Issuer risk](../risk/issuer.md) |
| Attestation issuer | Sound eligibility decisions | Price, settle or hold anything |
| MirageFi Labs (services) | Uptime and convenience | Misprice a fill, take custody, or block a withdrawal |
| Timelock multisig | Parameter changes, in public, after the delay | Move funds, block withdrawals, or act without waiting |

## Failure stance

Each component carries a designed response to failure: oracles halt, the sequencer has a grace path, services fall back to direct contract interaction, and parameters stay bounded by the band. The [Risk framework](../risk/framework.md) enumerates all of them.
