# Source: https://docs.bluumfinance.com/resources/error-codes

All Bluum errors follow the pattern `BLUM-{HTTP_STATUS}-{SEQUENCE}`. This page provides the complete catalog.

## 

[​](https://docs.bluumfinance.com/#400-—-bad-request)

400 — Bad Request

Validation failures and malformed requests.

| Code | Message | Resolution |
| --- | --- | --- |
| `BLUM-400-001` | Generic validation error | Check the error message for specific field details |
| `BLUM-400-002` | Missing required field | Include all required fields in your request |
| `BLUM-400-003` | Invalid field format | Match expected types: dates as ISO 8601, phone as E.164, country as ISO alpha-2 |
| `BLUM-400-004` | Invalid enum value | Use one of the documented enum values |
| `BLUM-400-005` | Invalid amount | Amount must be a positive decimal string (e.g., `"100.00"`) |
| `BLUM-400-006` | Invalid quantity | Quantity must be a positive number |
| `BLUM-400-007` | Invalid date range | Start date must be before end date |
| `BLUM-400-008` | Request body too large | Reduce payload size (file uploads: max 10MB) |

## 

[​](https://docs.bluumfinance.com/#401-—-unauthorized)

401 — Unauthorized

Authentication failures.

| Code | Message | Resolution |
| --- | --- | --- |
| `BLUM-401-001` | Missing authorization header | Include `Authorization: Basic <credentials>` |
| `BLUM-401-002` | Malformed authorization header | Format: `Basic <base64(API_KEY:API_SECRET)>` |
| `BLUM-401-003` | Invalid API key or secret | Verify credentials are correct and not revoked |
| `BLUM-401-004` | API key inactive | Contact support to activate your API key |
| `BLUM-401-005` | API key expired | Generate new credentials in the dashboard |

## 

[​](https://docs.bluumfinance.com/#403-—-forbidden)

403 — Forbidden

Authorization and permission errors.

| Code | Message | Resolution |
| --- | --- | --- |
| `BLUM-403-001` | Insufficient permissions | Your API key doesn’t have access to this resource |
| `BLUM-403-002` | Account not approved | Complete KYC verification before trading |
| `BLUM-403-003` | Production access required | Request production access from support |
| `BLUM-403-004` | Operation not allowed | The current account state doesn’t permit this operation |

## 

[​](https://docs.bluumfinance.com/#404-—-not-found)

404 — Not Found

Resource doesn’t exist or isn’t accessible.

| Code | Message | Resolution |
| --- | --- | --- |
| `BLUM-404-001` | Resource not found | Verify the ID exists and belongs to your tenant |
| `BLUM-404-002` | Endpoint not found | Check the URL path against the API reference |
| `BLUM-404-003` | Asset not found | Verify the symbol or asset ID |

## 

[​](https://docs.bluumfinance.com/#409-—-conflict)

409 — Conflict

Duplicate resources or state conflicts.

| Code | Message | Resolution |
| --- | --- | --- |
| `BLUM-409-001` | Duplicate resource | Resource with these attributes already exists |
| `BLUM-409-002` | Idempotency conflict | Same idempotency key used with different request body |
| `BLUM-409-003` | State conflict | Resource is in a state that prevents this operation |

## 

[​](https://docs.bluumfinance.com/#422-—-unprocessable-entity)

422 — Unprocessable Entity

Business rule violations.

| Code | Message | Resolution |
| --- | --- | --- |
| `BLUM-422-001` | Insufficient funds | Wallet balance too low for this operation |
| `BLUM-422-002` | Market closed | Place order during market hours or use `gtc` |
| `BLUM-422-003` | Asset not tradable | This asset is not available for trading |
| `BLUM-422-004` | Position insufficient | Not enough shares to sell |
| `BLUM-422-005` | Withdrawal limit exceeded | Exceeds available withdrawal balance |

## 

[​](https://docs.bluumfinance.com/#429-—-too-many-requests)

429 — Too Many Requests

Rate limit exceeded.

| Code | Message | Resolution |
| --- | --- | --- |
| `BLUM-429-001` | Rate limit exceeded | Wait and retry with exponential backoff; check `Retry-After` header |

## 

[​](https://docs.bluumfinance.com/#500-—-internal-server-error)

500 — Internal Server Error

Server-side errors. These are retryable.

| Code | Message | Resolution |
| --- | --- | --- |
| `BLUM-500-001` | Internal error | Retry with exponential backoff; contact support if persistent |
| `BLUM-500-017` | External service error | Upstream service temporarily unavailable; retry |

## 

[​](https://docs.bluumfinance.com/#handling-errors-programmatically)

Handling errors programmatically

```
async function handleBluumResponse(response) {
  if (response.ok) return response.json();

  const error = await response.json();
  const [, status] = error.code.match(/BLUM-(\d+)-/) || [];

  switch (status) {
    case '400': // Fix request and don't retry
    case '401': // Fix credentials
    case '403': // Fix permissions
    case '404': // Fix resource ID
    case '422': // Fix business logic
      throw new Error(`Client error: ${error.code} - ${error.message}`);

    case '429': // Retry with backoff
      const retryAfter = response.headers.get('Retry-After') || 1;
      await sleep(retryAfter * 1000);
      return retry();

    case '500': // Retry with backoff
      await sleep(1000);
      return retry();
  }
}
```

[AI-Assisted Development](https://docs.bluumfinance.com/resources/ai-tools) [Glossary](https://docs.bluumfinance.com/resources/glossary)

⌘I