# Source: https://docs.bluumfinance.com/platform/funding

Funding covers how money moves between your user’s bank account and their investment wallet. Bluum supports ACH transfers via Plaid and manual bank transfers.

## 

[​](https://docs.bluumfinance.com/#deposit-methods)

Deposit methods

| Method | How it works | Settlement |
| --- | --- | --- |
| **ACH via Plaid** (`ach`) | Automated pull from linked bank account | 1-3 business days |
| **Manual Bank Transfer** (`manual_bank_transfer`) | User initiates a transfer from their bank using provided instructions | Varies (1-5 business days) |

## 

[​](https://docs.bluumfinance.com/#plaid-link-integration)

Plaid Link integration

Before using ACH deposits, the user must link a bank account through Plaid Link. This is a two-step process:

### 

[​](https://docs.bluumfinance.com/#1-create-a-link-token)

1\. Create a Link token

Your backend requests a Plaid Link token from Bluum:

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/funding-sources/plaid/link-token" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{ "enable_hosted_link": false }'
```

### 

[​](https://docs.bluumfinance.com/#2-exchange-the-public-token)

2\. Exchange the public token

Your frontend launches Plaid Link with the token. After the user selects their bank, Plaid returns a `public_token` to your frontend. Send it to Bluum:

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/funding-sources/connect" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{ "type": "plaid", "public_token": "public-sandbox-abc123" }'
```

The response includes the linked bank account details (`itemId`, `accountId`) needed for deposits and withdrawals.

```
Your Frontend                    Your Backend                   Bluum API
     │                                │                            │
     │                                │──── Create Link Token ────►│
     │                                │◄─── link_token ───────────│
     │◄─── Launch Plaid Link ────────│                            │
     │──── User selects bank ────────►│                            │
     │──── public_token ─────────────►│                            │
     │                                │──── Exchange Token ───────►│
     │                                │◄─── Bank account details ──│
```

Set `enable_hosted_link: true` for a Plaid-hosted redirect flow. The response includes a `hosted_link_url` you redirect the user to, instead of embedding the Plaid Link widget.

## 

[​](https://docs.bluumfinance.com/#deposit-lifecycle)

Deposit lifecycle

```
pending → processing → received → completed
                           │
                        failed
```

| Status | Meaning |
| --- | --- |
| `pending` | Deposit created, not yet initiated |
| `processing` | ACH transfer initiated with the bank |
| `received` | Funds received, awaiting final settlement |
| `completed` | Funds available in wallet |
| `failed` | Transfer failed (insufficient funds, bank rejection) |

## 

[​](https://docs.bluumfinance.com/#withdrawal-lifecycle)

Withdrawal lifecycle

```
pending → processing → submitted → completed
    │                       │
 canceled                failed
```

Withdrawals in `pending` status can be canceled.

## 

[​](https://docs.bluumfinance.com/#manual-bank-transfers)

Manual bank transfers

For `manual_bank_transfer` deposits, the response includes bank details and a reference code:

```
{
  "method_details": {
    "referenceCode": "BLUUM-ABC123XY",
    "bankDetails": {
      "bankName": "Choice Financial Group",
      "accountName": "Bluum Finance, Inc.",
      "accountNumber": "202534766488",
      "routingNumber": "091311229",
      "instructions": "Include reference code \"BLUUM-ABC123XY\" in your transfer memo."
    }
  },
  "expires_at": "2025-06-22T10:45:00.000Z"
}
```

Display the bank details and reference code to your user. The deposit expires at `expires_at` if funds are not received.

### 

[​](https://docs.bluumfinance.com/#wire-details-pdf)

Wire details PDF

Download a formatted PDF with wire transfer instructions for a manual bank transfer deposit:

```
curl -X GET "$BASE_URL/investors/$INVESTOR_ID/deposits/$DEPOSIT_ID/wire-details" \
  -H "Authorization: Basic $AUTH" \
  --output wire-details.pdf
```

The PDF includes domestic transfer details (and international SWIFT details if configured). Only available for deposits with method `manual_bank_transfer`.

## 

[​](https://docs.bluumfinance.com/#idempotency)

Idempotency

Always include an `Idempotency-Key` header on deposit and withdrawal requests:

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/deposits" \
  -H "Idempotency-Key: dep-unique-request-id-123" \
  ...
```

If a request is retried with the same idempotency key, Bluum returns the original response instead of creating a duplicate transfer. See [Idempotency](https://docs.bluumfinance.com/platform/idempotency) for details.

## 

[​](https://docs.bluumfinance.com/#key-endpoints)

Key endpoints

| Method | Path | Description |
| --- | --- | --- |
| `POST` | `/investors/{investor_id}/funding-sources/plaid/link-token` | Create Plaid Link token |
| `POST` | `/investors/{investor_id}/funding-sources/connect` | Exchange Plaid public token |
| `GET` | `/investors/{investor_id}/funding-sources` | List linked funding sources |
| `POST` | `/investors/{investor_id}/deposits` | Create a deposit |
| `GET` | `/investors/{investor_id}/deposits/{deposit_id}` | Get deposit status |
| `POST` | `/investors/{investor_id}/deposits/{deposit_id}/cancel` | Cancel a pending deposit |
| `GET` | `/investors/{investor_id}/deposits/{deposit_id}/wire-details` | Download wire details PDF |
| `POST` | `/investors/{investor_id}/withdrawals` | Create a withdrawal |
| `GET` | `/investors/{investor_id}/withdrawals/{withdrawal_id}` | Get withdrawal status |
| `POST` | `/investors/{investor_id}/withdrawals/{withdrawal_id}/cancel` | Cancel a pending withdrawal |

[Wallets](https://docs.bluumfinance.com/platform/wallets) [Trading](https://docs.bluumfinance.com/platform/trading)

⌘I