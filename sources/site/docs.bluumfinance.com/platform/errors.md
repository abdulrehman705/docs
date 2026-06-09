# Source: https://docs.bluumfinance.com/platform/errors

All Bluum API errors follow a consistent format with structured error codes for programmatic handling.

## 

[​](https://docs.bluumfinance.com/#error-response-format)

Error response format

```
{
  "code": "BLUM-400-001",
  "message": "Invalid quantity. Must be a positive number."
}
```

| Field | Description |
| --- | --- |
| `code` | Structured error code: `BLUM-{HTTP_STATUS}-{SEQUENCE}` |
| `message` | Human-readable description of the error |

## 

[​](https://docs.bluumfinance.com/#error-code-categories)

Error code categories

| Prefix | HTTP Status | Category |
| --- | --- | --- |
| `BLUM-400-*` | 400 Bad Request | Validation failures, malformed requests |
| `BLUM-401-*` | 401 Unauthorized | Authentication errors |
| `BLUM-403-*` | 403 Forbidden | Authorization/permission errors |
| `BLUM-404-*` | 404 Not Found | Resource doesn’t exist or not accessible |
| `BLUM-409-*` | 409 Conflict | Duplicate resource or state conflict |
| `BLUM-422-*` | 422 Unprocessable | Business rule violation |
| `BLUM-429-*` | 429 Too Many Requests | Rate limit exceeded |
| `BLUM-500-*` | 500 Internal Error | Server-side error |

## 

[​](https://docs.bluumfinance.com/#common-errors)

Common errors

### 

[​](https://docs.bluumfinance.com/#authentication-401)

Authentication (401)

| Code | Message | Resolution |
| --- | --- | --- |
| `BLUM-401-001` | Missing authorization | Include `Authorization: Basic <credentials>` header |
| `BLUM-401-002` | Malformed credentials | Verify Base64 encoding of `API_KEY:API_SECRET` |
| `BLUM-401-003` | Invalid credentials | Check key and secret are correct and not revoked |

### 

[​](https://docs.bluumfinance.com/#validation-400)

Validation (400)

| Code | Message | Resolution |
| --- | --- | --- |
| `BLUM-400-001` | Generic validation error | Check the message for specific field issues |
| `BLUM-400-002` | Missing required field | Include all required fields in the request |
| `BLUM-400-003` | Invalid field format | Match expected types and formats (dates as ISO 8601, etc.) |

### 

[​](https://docs.bluumfinance.com/#not-found-404)

Not Found (404)

| Code | Message | Resolution |
| --- | --- | --- |
| `BLUM-404-001` | Resource not found | Verify the ID exists and belongs to your tenant |

### 

[​](https://docs.bluumfinance.com/#rate-limiting-429)

Rate Limiting (429)

| Code | Message | Resolution |
| --- | --- | --- |
| `BLUM-429-001` | Rate limit exceeded | Implement backoff; check `Retry-After` header |

## 

[​](https://docs.bluumfinance.com/#rate-limits)

Rate limits

| Environment | Limit |
| --- | --- |
| Sandbox | 10 requests/second per key pair |
| Production | 25 requests/second per key pair |

## 

[​](https://docs.bluumfinance.com/#retry-strategy)

Retry strategy

Implement exponential backoff with jitter for retryable errors (`429`, `500`, `502`, `503`, `504`):

```
async function requestWithRetry(url, options, maxRetries = 3) {
  for (let attempt = 0; attempt < maxRetries; attempt++) {
    const response = await fetch(url, options);

    if (response.status === 429) {
      const retryAfter = parseInt(response.headers.get('Retry-After') || '1');
      const delay = retryAfter * 1000 * Math.pow(2, attempt) + Math.random() * 1000;
      await new Promise(resolve => setTimeout(resolve, delay));
      continue;
    }

    if (response.status >= 500 && attempt < maxRetries - 1) {
      const delay = Math.pow(2, attempt) * 1000 + Math.random() * 1000;
      await new Promise(resolve => setTimeout(resolve, delay));
      continue;
    }

    return response;
  }
  throw new Error('Max retries exceeded');
}
```

**Do not retry** `400`, `401`, `403`, `404`, or `422` errors — these indicate client-side issues that won’t resolve on retry.

## 

[​](https://docs.bluumfinance.com/#http-status-codes)

HTTP status codes

| Code | Meaning | Retryable |
| --- | --- | --- |
| `200` | Success (GET, PUT, DELETE) | N/A |
| `201` | Created (POST) | N/A |
| `400` | Bad request | No |
| `401` | Unauthorized | No (fix credentials) |
| `403` | Forbidden | No (check permissions) |
| `404` | Not found | No |
| `409` | Conflict | No (resolve conflict) |
| `422` | Unprocessable | No (fix business logic) |
| `429` | Rate limited | Yes (with backoff) |
| `500` | Server error | Yes (with backoff) |

See [Error Code Reference](https://docs.bluumfinance.com/resources/error-codes) for the complete catalog.

[Webhooks](https://docs.bluumfinance.com/platform/webhooks) [Idempotency](https://docs.bluumfinance.com/platform/idempotency)

⌘I