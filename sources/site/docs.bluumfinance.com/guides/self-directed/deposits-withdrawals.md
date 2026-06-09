# Source: https://docs.bluumfinance.com/guides/self-directed/deposits-withdrawals

This guide covers all funding methods, status tracking, and edge cases for moving money in and out of investment wallets.

## 

[​](https://docs.bluumfinance.com/#deposit-methods)

Deposit methods

### 

[​](https://docs.bluumfinance.com/#ach-via-plaid)

ACH via Plaid

Pull funds from a linked bank account. Requires a [linked bank account](https://docs.bluumfinance.com/guides/self-directed/bank-linking).

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/deposits" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: dep-$(uuidgen)" \
  -d '{
    "amount": "5000.00",
    "currency": "USD",
    "method": "ach",
    "description": "Initial funding",
    "plaid_options": {
      "item_id": "item_abc123",
      "investor_id": "inv_1234567890"
    }
  }'
```

### 

[​](https://docs.bluumfinance.com/#manual-bank-transfer)

Manual bank transfer

The user sends a bank transfer using instructions provided in the response:

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/deposits" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: dep-$(uuidgen)" \
  -d '{
    "amount": "5000.00",
    "currency": "USD",
    "method": "manual_bank_transfer",
    "description": "Wire transfer funding"
  }'
```

The response includes bank details and a reference code:

```
{
  "method_details": {
    "referenceCode": "BLUUM-ABC123XY",
    "bankDetails": {
      "bankName": "Choice Financial Group",
      "accountName": "Bluum Finance, Inc.",
      "routingNumber": "091311229",
      "accountNumber": "202534766488",
      "instructions": "Include reference code \"BLUUM-ABC123XY\" in your transfer memo."
    }
  },
  "expires_at": "2025-06-22T10:45:00.000Z"
}
```

Display the bank details and reference code to your user. The deposit expires at `expires_at` if funds are not received.

## 

[​](https://docs.bluumfinance.com/#tracking-deposit-status)

Tracking deposit status

```
curl -X GET "$BASE_URL/investors/$INVESTOR_ID/deposits/$DEPOSIT_ID" \
  -H "Authorization: Basic $AUTH"
```

| Status | Meaning | Next |
| --- | --- | --- |
| `pending` | Created, not yet initiated | Wait or cancel |
| `processing` | ACH initiated with bank | Wait |
| `received` | Funds received, settling | Wait |
| `completed` | Funds in wallet, ready to trade | Trade |
| `failed` | Transfer failed | Check `failure_reason`, retry |

Use webhooks (`transfer.deposit.*`) for real-time status updates instead of polling.

## 

[​](https://docs.bluumfinance.com/#withdrawals)

Withdrawals

Withdraw funds to a linked bank account:

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/withdrawals" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: wdr-$(uuidgen)" \
  -d '{
    "amount": "1000.00",
    "currency": "USD",
    "method": "ach",
    "description": "Withdrawal to checking",
    "plaid_options": {
      "item_id": "item_abc123",
      "investor_id": "inv_1234567890"
    }
  }'
```

### 

[​](https://docs.bluumfinance.com/#withdrawal-statuses)

Withdrawal statuses

| Status | Meaning |
| --- | --- |
| `pending` | Created, can be canceled |
| `processing` | ACH initiated |
| `submitted` | Submitted to bank |
| `completed` | Funds delivered |
| `failed` | Transfer failed |

### 

[​](https://docs.bluumfinance.com/#canceling-a-withdrawal)

Canceling a withdrawal

Only `pending` withdrawals can be canceled:

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/withdrawals/$WITHDRAWAL_ID/cancel" \
  -H "Authorization: Basic $AUTH"
```

## 

[​](https://docs.bluumfinance.com/#idempotency)

Idempotency

Always include `Idempotency-Key` headers on deposit and withdrawal requests. This prevents duplicate transfers if a request is retried due to network issues or timeouts.

```
-H "Idempotency-Key: dep-unique-operation-id"
```

See [Idempotency](https://docs.bluumfinance.com/platform/idempotency) for best practices.

## 

[​](https://docs.bluumfinance.com/#error-handling)

Error handling

| Error | Cause | Resolution |
| --- | --- | --- |
| `BLUM-400-*` | Invalid amount or missing fields | Check request body |
| `BLUM-422-*` | Insufficient funds (withdrawal) | Check wallet balance |
| `BLUM-409-*` | Duplicate idempotency key with different body | Use a new key for different operations |

[Link a Bank Account](https://docs.bluumfinance.com/guides/self-directed/bank-linking) [Placing Orders](https://docs.bluumfinance.com/guides/self-directed/placing-orders)

⌘I