---
description: How assets are priced, and what the venue does when the underlying market is closed or the feed cannot be trusted.
---

# Oracles and market sessions

Every price in MirageFi passes through a single contract, `OracleRouter`. It wraps Chainlink and applies the protocol's guards before any price reaches a quote, a fill check or the regime engine. The anchor price carries the venue's entire design, which makes this the load-bearing page.

## Sources

| Source | Used for | Notes |
| --- | --- | --- |
| Chainlink **Data Feeds** (24/5 equities) | The mid used for vault quoting and band checks | Push-based and on-chain; updates on deviation or heartbeat |
| Chainlink **Data Streams** (v11 RWA schema) | Session classification; a sub-second cross-check on RFQ fills | Pull-based; includes `marketStatus` (a value of 5 means closed) |
| Chainlink **Sequencer Uptime Feed** | The trading grace after an outage | See [Sequencer and chain risk](sequencer-and-chain.md) |
| ERC-8056 `uiMultiplier()` | Share-denominated display in the explorer | Read from the token itself; the Chainlink price already reflects the multiplier |

Consuming both feeds and streams provides a cross-check: no fill can clear at a price that diverges from the other source by more than a configured tolerance, and the stream's market status can only tighten the regime relative to what the feed alone would give.

## Sessions

From the stream's `marketStatus` and the feed's `updatedAt` timestamp, `OracleRouter` classifies each market as **regular**, **extended** or **closed**. The [regime engine](../protocol/trading-regimes.md) then maps that classification to spreads and clips.

| Session | Staleness bound | Effect |
| --- | --- | --- |
| Regular | 1 hour | Tier spreads as published |
| Extended | 2 hours | Spreads ×1.5, clips ×0.75 |
| Closed | 4 days | Spreads ×3.0, clips ×0.5; weekend quoting is anchored to the Friday close |

Monday's open can be a long way from the previous Friday's close, which is why the closed-session multiplier exists. LPs who quote through the weekend charge for that gap explicitly. A trader who wants to deal at 3am on a Sunday pays a disclosed premium for a real service, rather than an undisclosed one for a mispriced service.

## Guards

`OracleRouter` rejects or halts before any price is used when:

| Condition | Action |
| --- | --- |
| Price is zero or negative | Revert |
| `updatedAt` is older than the staleness bound for the session | Market `HALTED` until a fresh round arrives |
| A single-round move exceeds the 25% cap | Market `HALTED` pending a timelocked review and a published finding |
| The feed reports `oraclePaused()` | Market `HALTED` (a corporate action is in progress); see [Corporate actions](../protocol/corporate-actions.md) |
| Stream and feed diverge beyond tolerance | The fill reverts |

A halt stops quoting and settlement in the affected market and nothing more. Withdrawals, cancellations and reads all continue to work.

## Price the exact token

Each market's `OracleRouter` configuration points at the feed for the token the vault actually holds. A price is never derived through a wrapper, a vault share, or an exchange rate between two tokens. That is the lesson of the Edel Finance wGOOGLx incident of July 2026: the oracle for the underlying was correct, but a wrapper's exchange rate was pushed 78 times higher and was trusted. See [Lessons from RWA trading](lessons.md).

## Changing an oracle

Each market's oracle adapter is a `ParamController` value. Replacing one is a timelocked action with a published rationale. For every market, `OracleRouter` exposes a view of the current feed, session, staleness, multiplier and pause flag, and the [trade explorer](../users/trade-explorer.md) shows it live. The state the router enforces is therefore always the state the public can see.
