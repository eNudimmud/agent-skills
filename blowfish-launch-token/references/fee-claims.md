# Fee Claims Reference

Detailed guide for viewing and claiming accumulated trading fees from launched tokens.

## Fee Types

Each token accumulates two types of fees:

| Fee Type | Field Prefix | Description |
|----------|-------------|-------------|
| DBC (Dynamic Bonding Curve) | `dbc` | Fees from the bonding curve trading |
| LP (Liquidity Pool) | `lp` | Fees from liquidity pool activity |

## Fee Fields

```json
{
  "tokenMint": "mint-address",
  "poolAddress": "pool-address",
  "ticker": "MYTKN",
  "dbcClaimableFees": 0.5,    // Available to claim now
  "dbcTotalFees": 1.0,        // All-time total
  "dbcClaimedFees": 0.5,      // Already claimed
  "lpClaimableFees": 0.25,
  "lpTotalFees": 0.5,
  "lpClaimedFees": 0.25,
  "isMigrated": false          // Whether the pool has migrated
}
```

## Claim Workflow

### 1. Check Available Fees

```bash
curl -s https://api-blowfish.neuko.ai/api/v1/tokens/claims \
  -H "Authorization: Bearer <jwt>" | jq '.claims[] | select(.dbcClaimableFees > 0 or .lpClaimableFees > 0)'
```

### 2. Request Unsigned Transaction

```bash
curl -s -X POST https://api-blowfish.neuko.ai/api/v1/tokens/claims/<mintAddress> \
  -H "Authorization: Bearer <jwt>"
```

The response contains a base64-encoded Solana transaction that needs to be signed.

### 3. Sign the Transaction

```typescript
import { Transaction } from "@solana/web3.js";

// Decode the unsigned transaction
const txBuffer = Buffer.from(unsignedTx, "base64");
const transaction = Transaction.from(txBuffer);

// Sign with your keypair
transaction.sign(keypair);

// Re-encode as base64
const signedTx = transaction.serialize().toString("base64");
```

### 4. Submit Signed Transaction

```bash
curl -s -X POST https://api-blowfish.neuko.ai/api/v1/tokens/claims/<mintAddress> \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <jwt>" \
  -d '{"signedTransaction": "<base64-signed-tx>"}'
```

### 5. Verify Result

A successful claim returns:

```json
{
  "success": true,
  "transactionHash": "tx-signature-on-solana",
  "claimedSOL": 0.75
}
```

The `transactionHash` can be verified on a Solana explorer.

## Error Scenarios

| Error | Cause | Resolution |
|-------|-------|------------|
| 404 | Token not found | Verify mintAddress matches a deployed token |
| 401 | Expired JWT | Re-authenticate |
| 400 | Invalid signed transaction | Ensure correct keypair was used to sign |
| No claimable fees | Fees are zero | Wait for trading activity to generate fees |
