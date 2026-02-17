# Error Handling Reference

Complete error catalog for the Blowfish Agent API.

## Error Response Format

All errors follow a consistent format:

```json
{
  "error": "Human-readable error message"
}
```

## HTTP Status Codes

### 400 — Bad Request

Validation failures on request parameters.

| Scenario | Error Message | Fix |
|----------|---------------|-----|
| Missing `name` | Field validation error | Provide `name` (1–255 chars) |
| Missing `ticker` | Field validation error | Provide `ticker` (2–10 uppercase alphanumeric) |
| `ticker` too short | Field validation error | Use at least 2 characters |
| `ticker` too long | Field validation error | Use at most 10 characters |
| `ticker` has invalid chars | Field validation error | Use only A-Z and 0-9 |
| `name` too long | Field validation error | Keep under 255 characters |
| `description` too long | Field validation error | Keep under 1000 characters |
| `imageUrl` invalid | Field validation error | Provide a valid URL under 255 chars |
| Invalid signed transaction | Transaction validation error | Verify keypair and serialization |

### 401 — Unauthorized

Authentication failures.

| Scenario | Error Message | Fix |
|----------|---------------|-----|
| No Authorization header | Missing auth token | Include `Authorization: Bearer <jwt>` |
| Expired JWT | Token expired | Re-authenticate via challenge-response |
| Invalid JWT | Invalid token | Re-authenticate — do not reuse old tokens |
| Invalid nonce | Nonce expired or invalid | Request a fresh nonce |
| Invalid signature | Signature verification failed | Verify ed25519 signing with correct keypair |

### 404 — Not Found

Resource does not exist.

| Scenario | Error Message | Fix |
|----------|---------------|-----|
| Unknown eventId | Event not found | Verify the eventId from the launch response |
| Unknown mintAddress | Token not found | Verify the mint address from your token list |

### 409 — Conflict

Duplicate resource.

| Scenario | Error Message | Fix |
|----------|---------------|-----|
| Ticker already exists | Ticker already taken | Choose a different ticker |

### 429 — Rate Limited

Too many requests.

| Scenario | Error Message | Fix |
|----------|---------------|-----|
| Daily launch limit | Rate limit exceeded | Wait until UTC midnight to try again |

## Retry Strategy

| Error Type | Retryable | Strategy |
|-----------|-----------|----------|
| 400 | No | Fix the request and retry |
| 401 | Yes | Re-authenticate, then retry |
| 404 | No | Verify resource identifiers |
| 409 | No | Choose different parameters |
| 429 | Yes | Wait until UTC midnight |
| 5xx | Yes | Exponential backoff (2s, 4s, 8s, max 30s) |

## Best Practices

1. **Always validate locally first** — check ticker format and field lengths before making API calls
2. **Handle 401 gracefully** — re-authenticate automatically when JWT expires
3. **Log error responses** — include the full error message for debugging
4. **Respect rate limits** — track your daily launch count and avoid unnecessary retries
