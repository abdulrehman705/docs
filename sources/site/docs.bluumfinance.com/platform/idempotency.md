# Source: https://docs.bluumfinance.com/platform/idempotency

Idempotency ensures that retrying a request doesn’t create duplicate side effects. This is critical for financial operations like deposits and withdrawals where a duplicate could move money twice.

## 

[​](https://docs.bluumfinance.com/#how-it-works)

How it works

1. Generate a unique `Idempotency-Key` for each distinct operation
2. Include it in the request header
3. If you retry the same request with the same key, Bluum returns the original response instead of processing it again

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/deposits" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: dep-abc123-unique-id" \
  -d '{
    "amount": "5000.00",
    "currency": "USD",
    "method": "ach",
    "plaid_options": {
      "item_id": "item_abc123",
      "investor_id": "inv_1234567890"
    }
  }'
```

## 

[​](https://docs.bluumfinance.com/#when-to-use-idempotency-keys)

When to use idempotency keys

| Operation | Required | Why |
| --- | --- | --- |
| Deposits | Yes | Prevent duplicate fund transfers |
| Withdrawals | Yes | Prevent duplicate disbursements |
| Orders | Recommended | Prevent duplicate trades (use `client_order_id` as alternative) |
| Account creation | Optional | Account uniqueness is enforced by other means |
| Read operations (GET) | Not needed | GETs are naturally idempotent |

## 

[​](https://docs.bluumfinance.com/#generating-keys)

Generating keys

Use any unique string. Common approaches:

```
// UUID
const key = crypto.randomUUID();
// dep-550e8400-e29b-41d4-a716-446655440000

// Prefixed with operation type
const key = `dep-${crypto.randomUUID()}`;

// Derived from business logic (user + operation + timestamp)
const key = `dep-${userId}-${Date.now()}`;
```

## 

[​](https://docs.bluumfinance.com/#behavior)

Behavior

| Scenario | Result |
| --- | --- |
| First request with key | Processed normally, response stored |
| Retry with same key, same body | Original response returned (no reprocessing) |
| Retry with same key, different body | Error — key is already associated with a different request |
| Different key, same body | Processed as a new request (potential duplicate) |

## 

[​](https://docs.bluumfinance.com/#key-expiration)

Key expiration

Idempotency keys expire after 24 hours. After expiration, the same key can be reused for a new request.

## 

[​](https://docs.bluumfinance.com/#best-practices)

Best practices

- Generate a new key for each distinct operation — don’t reuse keys across different deposits or withdrawals
- Store the key alongside your local transaction record for debugging
- Use the key consistently across retries of the same operation
- Include the operation type in the key prefix for readability (`dep-`, `wdr-`)

[Error Handling](https://docs.bluumfinance.com/platform/errors)

⌘I