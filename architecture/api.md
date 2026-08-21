---
description: The public quote API, the TypeScript SDK, and the protocol makers speak.
---

# API and SDK

The API is what lets MirageFi act as a backend rather than only a website: wallets, portfolio apps, aggregators and agents plug into the same pricing the platform uses and settle through the same public router. There is no partner tier and no privileged endpoint; what is documented here is exactly what the reference front-end consumes.

## Quote API (REST)

```
GET /v1/quote?tokenIn=USDG&tokenOut=AAPL&amountIn=2000000000
```

```json
{
  "amountOut": "9873400000000000000",
  "breakdown": {
    "mid": "202.41",
    "spreadBps": 10,
    "skewBps": -2,
    "feeBps": 2,
    "regime": "OPEN",
    "oracleRound": "0x…"
  },
  "venue": "vault",
  "rfq": { "quote": { "…": "…" }, "signature": "0x…" },
  "validUntil": 1788316201,
  "tx": { "to": "0xSwapRouter…", "data": "0x…" }
}
```

Every response itemises the breakdown, names whichever venue priced best, carries any winning RFQ quote along with its signature for on-chain verification, and provides ready-to-sign calldata with the trader's bound already applied. A quote is a preview: because the chain re-derives or re-verifies everything, a misquote can push an integrator into a revert but never into a bad fill.

The remaining endpoints are `/v1/markets` for listings, tiers, parameters and regimes, `/v1/fills` for paginated fill history with breakdowns, `/v1/execution-quality` for the published dataset that feeds the [transparency page](../transparency/execution-quality.md), and `/v1/eligibility/:address` for a preview of the role check.

## WebSocket

`wss://api.miragefi.org/v1/stream` pushes regime changes, per-market quote updates at feed cadence, and fill events. The platform's tickets are fed by the same stream.

## TypeScript SDK

```ts
import { MirageFi } from "@miragefi/sdk";

const z = new MirageFi({ chainId: 4663, signer });
const quote = await z.quote({ tokenIn: "USDG", tokenOut: "AAPL", amountIn: 2_000_000000n });
console.log(quote.breakdown);          // mid, spread, skew, fee, regime
const receipt = await z.swap(quote, { maxSlippageBps: 15 });
```

Quoting, Permit2 signing, eligibility preflight and receipt decoding are all wrapped by the SDK, which also ships typed events for consumers of the indexer. Versioning is semantic with a published changelog; a breaking change never ships without notice.

## Maker protocol

Makers connect through an authenticated WebSocket, receive broadcast requests, and reply with EIP-712 signatures:

```
→ { "type": "rfq", "id": "…", "tokenIn": "USDG", "tokenOut": "NVDA", "amountIn": "250000000000" }
← { "type": "quote", "id": "…", "quote": { …EIP-712 fields… }, "signature": "0x…" }
```

A `MAKER` attestation is required for admission; the message schema, signing domain and guidance on nonce management live in the maker guide at [For market makers](../users/market-makers.md). There is no cost to losing an auction and no quoting obligation at launch, although makers who quote consistently earn priority in request routing.

## Rate limits and keys

The quote endpoints are open and rate-limited per IP; an authenticated key raises the limits and unlocks the WebSocket. Keys exist to identify integrators for support and abuse control, nothing more. They make no difference to pricing, and this is verifiable, because vault pricing is a public contract read.

## Versioning

`/v1` is stable. Any deprecation is announced in the changelog no less than 90 days in advance, and the SDK pins API versions explicitly. Per-deployment contract addresses are listed in [Deployment](deployment.md).
