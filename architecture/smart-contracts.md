---
description: Each contract in the protocol, the one job it does, and whether it can change.
---

# Smart contracts

The contract set is kept deliberately small. Every contract does exactly one thing, logic is immutable within a version, and anything that can be tuned sits in `ParamController` behind the timelock.

## The set

| Contract | Job | Mutability |
| --- | --- | --- |
| `SwapRouter` | The only trader entry point: eligibility checks, venue choice, two-leg composition, enforcement of band and bound | Immutable |
| `AnchorVault` (one per market) | Holds inventory, quotes around the mid, mints and burns LP shares | Immutable; parameters via `ParamController` |
| `VaultFactory` | Deploys vaults from listed configurations | Immutable |
| `RfqSettlement` | Verifies maker EIP-712 quotes and settles them atomically | Immutable |
| `OracleRouter` | The single price door: Chainlink adapters, guards, session and regime state | Immutable; adapters via `ParamController` |
| `EligibilityRegistry` | The single permission door: role checks against attestations | Immutable; policy adapters via `ParamController` |
| `ParamController` | Home of every tunable value, behind the timelock | Timelocked writes, public reads |
| `FeeCollector` | Accrues protocol fees | Immutable |
| `Guardian` | Pauses quoting and settlement during an incident | Halt only; cannot move funds or block withdrawals |

## Interfaces that matter to integrators

```solidity
// SwapRouter: the one call traders make
function swap(SwapParams calldata p) external returns (uint256 amountOut);
struct SwapParams {
    address tokenIn; address tokenOut;
    uint256 amountIn; uint256 minAmountOut;
    uint256 deadline;
    MakerQuote[] quotes;     // optional RFQ candidates, verified on-chain
    bytes permit;            // Permit2 pull for tokenIn
}

// AnchorVault: LP side
function deposit(uint256 usdgAmount, uint256 tokenAmount, address receiver) external returns (uint256 shares);
function withdraw(uint256 shares, address receiver) external returns (uint256 usdgOut, uint256 tokenOut);
function quote(bool isBuy, uint256 amountIn) external view returns (uint256 amountOut, QuoteBreakdown memory b);

// OracleRouter: the state everyone can read
function priceOf(address token) external view returns (uint256 mid, uint8 regime, uint64 updatedAt);
```

`QuoteBreakdown` breaks out mid, spread, skew and fee. It is emitted with every fill, so the on-chain record carries exactly the decomposition the ticket displayed.

## Events

Each fill emits a single event carrying the pair, the size, the venue (vault or maker), the complete breakdown, and the oracle round that was used. Each parameter change emits the previous value, the new value and the hash of the timelock proposal. The [trade explorer](../users/trade-explorer.md) is derived purely from these events; it shows nothing sourced from anywhere else.

## Upgrade philosophy

No proxies exist. A new protocol version means a fresh deployment, and markets move over when LPs choose to withdraw in kind and redeposit. The cost is migration convenience; what it buys is that the code holding funds today is the audited code, permanently. The single exception is the adapter pattern within `OracleRouter` and `EligibilityRegistry`: the set of accepted adapters there is a timelocked parameter, because oracle products and attestation schemes change faster than settlement logic ought to.

## Access control summary

| Role | Held by | Powers |
| --- | --- | --- |
| Timelock proposer | Foundation multisig | Proposes parameter changes, each published with its rationale |
| Guardian | Foundation multisig (smaller quorum) | Pause; unpausing goes through the timelock |
| Keeper functions | Nobody: permissionless | Regime pokes, halt pokes |
| Everything else | Nobody | No further privileged functions exist |
