---
description: How a market's state is derived from the underlying session, and what changes in each state.
---

# Trading regimes

In regular session, equity markets are open for roughly 32 hours a week. Stock Tokens trade at all hours. The regime engine is MirageFi's way of pricing that gap rather than pretending it is not there.

## The four regimes

| Regime | Derived from | Quoting |
| --- | --- | --- |
| `OPEN` | Regular session according to the oracle's market status | Tightest spreads, full clips |
| `EXTENDED` | Extended-hours session | Spreads ×1.5, clips ×0.75 |
| `CLOSED` | Underlying market closed | Spreads ×3.0, clips ×0.5 |
| `HALTED` | Stale oracle, `oraclePaused()`, corporate action, sequencer recovery or guardian pause | No quotes; deposits and withdrawals unaffected |

Regime state is held per market in `OracleRouter` and every quote reads it. The multipliers are per-tier `ParamController` values.

## How regime is derived

`OracleRouter` classifies a market using two independent signals:

* the `marketStatus` field of Chainlink Data Streams (v11 RWA schema; a value of 5 means closed), and
* the `updatedAt` timestamp of the Data Feed, measured against the session's staleness bound (1 hour in regular session, 2 in extended, and longer bounds when closed).

Either signal can only tighten the regime, never loosen it: if the feed looks live but the stream reports closed, the market is treated as closed. The transition function can be poked by anyone; the keeper service does so within seconds of a session boundary, and any swap that lands before the poke recomputes the regime inside the same transaction, so at no point can stale-session pricing be used.

## Why `CLOSED` trades at all

Both alternative designs are worse. Declining to quote on weekends reintroduces market hours on a 24/7 chain and sends users back to custodial venues. Filling weekend trades at weekday spreads turns LPs into the free counterparty for every Monday gap. MirageFi takes the honest middle path: liquidity exists on weekends, it costs more, it comes in smaller size, and the ticket states exactly how much more. The closed-session multiplier is the price of gap risk, set conservatively at launch and retuned against the Monday-open gaps actually observed, with each change going through the timelock.

## `HALTED` in detail

A market halts whenever any of the following holds, and resumes once none of them does:

| Trigger | Cleared by |
| --- | --- |
| Feed staleness past the session bound | A fresh oracle round |
| `oraclePaused()` on the feed (a corporate action in progress) | The feed resuming; see [Corporate actions](corporate-actions.md) |
| A single-round price move beyond the 25% guard | Timelocked review with a published finding |
| Sequencer recovery grace (1 hour following an outage) | Expiry of the grace period; see [Sequencer and chain risk](../risk/sequencer-and-chain.md) |
| Guardian pause | Unpause, accompanied by a published incident note |

A halt stops quoting and nothing more. LP deposits and withdrawals, RFQ cancellations and all view functions continue to work. The trade explorer shows the reason for the halt live, taken from the same contract state the router enforces.

## Calendar independence

Nowhere in the protocol is a market calendar hard-coded. Holidays, half-days and unscheduled closures all come in through the oracle's market status, so the venue is never open while the data says the market is closed, and never stuck closed while the data says it is open.
