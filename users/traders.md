---
description: A working guide to swapping on MirageFi, from first connection through to reading a fill receipt.
---

# For traders

## Before your first swap

1. **Wallet.** Connect Robinhood Wallet, MetaMask or any standard wallet, or set up an embedded account with a passkey on the [platform](https://miragefi.org/platform).
2. **Eligibility.** Go through the attestation flow a single time. The KYC provider checks identity, sanctions status and jurisdiction, then issues a `TRADER` attestation to your wallet; none of your personal data is written on-chain. Your eligibility state is always visible on the platform. See [Eligibility registry](../architecture/eligibility.md).
3. **USDG.** Every market is quoted against USDG. Stock Tokens you already hold work just as well as a starting asset; a token-to-token swap is routed through USDG without any extra step on your part.

## Reading the ticket

Each quote breaks down into the same four lines:

| Line | What it is |
| --- | --- |
| Mid | The guarded Chainlink price the quote is built from, together with its oracle round |
| Spread | What the venue charges for immediacy, in bps and USDG, inventory skew included |
| Fee | The protocol fee, shown on its own line |
| Regime badge | `OPEN`, `EXTENDED` or `CLOSED`, plus the multiplier currently applied |

A `CLOSED` badge means you are paying about three times the normal spread for weekend liquidity, and the maximum size is smaller. That is the true cost of trading an asset whose underlying market is not open. If the trade can wait until Monday's open, waiting costs less, and the ticket is saying as much.

## Slippage and expiry

The quote you sign includes a minimum-output bound and a deadline. Should price or vault state move against you past that bound before inclusion, the swap reverts instead of filling; with preconfirmations of about 100 ms this is rare in practice. RFQ quotes also expire within seconds, and the platform requests a fresh one automatically.

## Large trades

Once size exceeds the vault clip, the router prices you through [RFQ](../protocol/rfq.md) with nothing different on your side: the ticket and its breakdown are the same, and the venue line shows "maker" rather than "vault". For orders that should be worked over time, splitting is manual for now; execution algorithms sit on the [roadmap](../roadmap/phases.md).

## After the fill

The receipt sets the fill beside the on-chain event: the breakdown the ticket quoted, the venue that executed it, and a Blockscout link. The [execution quality](../transparency/execution-quality.md) page aggregates every fill on the venue against the oracle mid, your own included, so execution never has to be taken on trust.

## What you hold

A Stock Token is a tokenized debt claim on Robinhood Assets (Jersey) Ltd that tracks the underlying share; it is not the share. Dividends accrete into the token through the ERC-8056 multiplier instead of being paid out as cash. Before trading size, read [Stock Tokens](../assets/stock-tokens.md) and [Issuer risk](../risk/issuer.md) once; the disclosure shown at first swap summarises both.

## When markets halt

A halt (stale oracle, corporate action, sequencer recovery) stops quoting in the affected market and nowhere else. Your assets stay in your wallet; nothing is locked. The platform displays the reason for the halt and the state that will clear it. See [Trading regimes](../protocol/trading-regimes.md).
