# Source: https://docs.bluumfinance.com/get-started/quick-start-self-directed

This guide walks through the complete self-directed investing lifecycle: account creation, KYC, bank linking, funding, trading, and withdrawal. By the end, you’ll have a working integration in the sandbox.

**Prerequisites:** API credentials from the [sandbox environment](https://docs.bluumfinance.com/get-started/sandbox). A terminal or API client (cURL, Postman, or similar).

Set these variables once — every example below references them:

```
API_KEY="YOUR_API_KEY"
API_SECRET="YOUR_API_SECRET"
BASE_URL="https://dev-tapp-api.tappengine.com/v1"
AUTH=$(echo -n "$API_KEY:$API_SECRET" | base64)
```

---

## 

[​](https://docs.bluumfinance.com/#step-1-—-create-an-investment-account)

Step 1 — Create an investment account

When a user in your app is ready to invest, create an `individual` account on their behalf. This collects identity, contact information, regulatory disclosures, and agreements.

cURL

Node.js

Python

```
curl -X POST "$BASE_URL/accounts" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "account_type": "individual",
    "management_type": "self_directed",
    "contact": {
      "email_address": "jane.doe@example.com",
      "phone_number": "+14155551234",
      "street_address": ["456 Oak Avenue"],
      "city": "San Francisco",
      "state": "CA",
      "postal_code": "94102",
      "country": "US"
    },
    "identity": {
      "first_name": "Jane",
      "last_name": "Doe",
      "date_of_birth": "1990-05-15",
      "tax_id": "987-65-4321",
      "tax_id_type": "SSN",
      "country_of_citizenship": "US",
      "country_of_birth": "US",
      "country_of_tax_residence": "US",
      "funding_source": ["employment_income"]
    },
    "disclosures": {
      "is_control_person": false,
      "is_affiliated_exchange_or_finra": false,
      "is_politically_exposed": false,
      "immediate_family_exposed": false
    },
    "agreements": [
      {
        "agreement": "investor_agreement",
        "agreed": true,
        "signed_at": "2025-06-15T10:30:00Z",
        "ip_address": "203.0.113.42"
      },
      {
        "agreement": "investor_agreement",
        "agreed": true,
        "signed_at": "2025-06-15T10:30:00Z",
        "ip_address": "203.0.113.42"
      }
    ]
  }'
```

The response returns an account with `status: "ACTIVE"`. Store the `id` — you’ll need it for every subsequent call.

```
INVESTOR_ID="3d0b0e65-35d3-4dcd-8df7-10286ebb4b4b"
```

See [Accounts](https://docs.bluumfinance.com/platform/accounts) for details on account types, required fields, and lifecycle states.

---

## 

[​](https://docs.bluumfinance.com/#step-2-—-upload-kyc-documents)

Step 2 — Upload KYC documents

Identity verification requires supporting documents. Upload a government-issued ID (passport, driver’s license).

```
curl -X POST "$BASE_URL/documents" \
  -H "Authorization: Basic $AUTH" \
  -F "document_type=id_verification" \
  -F "file=@/path/to/drivers-license.jpg"
```

```
{
  "document_id": "doc_a1b2c3d4e5f6g7h8",
  "investor_id": "inv_01j9x8m2k7qpzwv3t5r6y8n0ab",
  "document_type": "id_verification",
  "upload_status": "processing",
  "uploaded_at": "2025-06-15T10:35:00Z"
}
```

Check the status until it changes to `approved`:

```
curl -X GET "$BASE_URL/documents/doc_a1b2c3d4e5f6g7h8" \
  -H "Authorization: Basic $AUTH"
```

In sandbox, documents are auto-approved. In production, use [webhooks](https://docs.bluumfinance.com/platform/webhooks) to receive notifications instead of polling.

---

## 

[​](https://docs.bluumfinance.com/#step-3-—-link-a-bank-account-via-plaid)

Step 3 — Link a bank account via Plaid

Before the user can deposit funds, they need to link a bank account. This is a two-step flow using Plaid Link.

### 

[​](https://docs.bluumfinance.com/#3a-create-a-plaid-link-token)

3a. Create a Plaid Link token

Generate a token your frontend uses to launch the Plaid Link widget:

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/funding-sources/plaid/link-token" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{ "enable_hosted_link": false }'
```

```
{
  "status": "success",
  "data": {
    "link_token": "link-sandbox-abc123def456",
    "hosted_link_url": null
  }
}
```

Set `enable_hosted_link: true` for a Plaid-hosted redirect flow instead of embedding the widget.

### 

[​](https://docs.bluumfinance.com/#3b-exchange-the-public-token)

3b. Exchange the public token

After the user completes Plaid Link, your frontend receives a `public_token`. Send it to Bluum to finalize the connection:

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/funding-sources/connect" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{ "public_token": "public-sandbox-abc123def456" }'
```

Store the returned `item.itemId` and `item.accounts[0].accountId` — you’ll need them for deposits and withdrawals.

```
PLAID_ITEM_ID="item_abc123"
PLAID_ACCOUNT_ID="acc_1234567890"
```

---

## 

[​](https://docs.bluumfinance.com/#step-4-—-deposit-funds)

Step 4 — Deposit funds

Fund the account via ACH using the linked bank account:

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/deposits" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: dep-$(uuidgen)" \
  -d '{
    "amount": "5000.00",
    "currency": "USD",
    "method": "ach",
    "description": "Initial account funding",
    "plaid_options": {
      "item_id": "'"$PLAID_ITEM_ID"'",
      "account_id": "'"$PLAID_ACCOUNT_ID"'"
    }
  }'
```

Deposit status progresses: `pending` → `processing` → `received` → `completed`.

Always include an `Idempotency-Key` header on deposit and withdrawal requests to prevent duplicate transfers on retry.

You can also use `manual_bank_transfer` as the method — the response includes bank details and a reference code your user provides to their bank. See [Deposits & Withdrawals](https://docs.bluumfinance.com/guides/self-directed/deposits-withdrawals) for all funding methods.

---

## 

[​](https://docs.bluumfinance.com/#step-5-—-place-a-buy-order)

Step 5 — Place a buy order

Once the deposit is `completed` and the wallet is funded, the user can trade.

### 

[​](https://docs.bluumfinance.com/#market-buy-—-by-quantity)

Market buy — by quantity

Buy 10 shares of AAPL at the current market price:

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/orders" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "symbol": "AAPL",
    "side": "buy",
    "type": "market",
    "time_in_force": "day",
    "quantity": "10"
  }'
```

### 

[​](https://docs.bluumfinance.com/#market-buy-—-by-dollar-amount-fractional-shares)

Market buy — by dollar amount (fractional shares)

Invest exactly $1,000 worth of GOOGL:

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/orders" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "symbol": "GOOGL",
    "side": "buy",
    "type": "market",
    "time_in_force": "day",
    "notional": "1000.00"
  }'
```

### 

[​](https://docs.bluumfinance.com/#response)

Response

```
{
  "id": "ord_x9y8z7a6b5c4d3e2",
  "investor_id": "inv_01j9x8m2k7qpzwv3t5r6y8n0ab",
  "symbol": "AAPL",
  "quantity": "10",
  "side": "buy",
  "type": "market",
  "time_in_force": "day",
  "status": "pending",
  "filled_quantity": "0",
  "average_price": "0.00",
  "submitted_at": "2025-06-15T14:30:00Z"
}
```

Order status progresses: `accepted` → `filled` (or `partially_filled`, `canceled`, `rejected`). Check order status:

```
curl -X GET "$BASE_URL/investors/$INVESTOR_ID/orders/ord_x9y8z7a6b5c4d3e2" \
  -H "Authorization: Basic $AUTH"
```

See [Trading](https://docs.bluumfinance.com/platform/trading) for limit, stop, and trailing stop orders.

---

## 

[​](https://docs.bluumfinance.com/#step-6-—-check-positions)

Step 6 — Check positions

After an order fills, view the account’s holdings:

```
curl -X GET "$BASE_URL/investors/$INVESTOR_ID/positions" \
  -H "Authorization: Basic $AUTH"
```

```
[
  {
    "id": "pos_a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "investor_id": "inv_01j9x8m2k7qpzwv3t5r6y8n0ab",
    "symbol": "AAPL",
    "asset_id": "asset_9f8e7d6c-5b4a-3210-fedc-ba0987654321",
    "currency": "USD",
    "quantity": "10",
    "average_cost_basis": "178.50",
    "total_cost_basis": "1785.00",
    "current_price": "179.25",
    "market_value": "1792.50",
    "unrealized_pl": "7.50",
    "unrealized_pl_percent": "0.0042",
    "price_source": "ALPACA",
    "price_confidence": "REAL_TIME",
    "price_timestamp": "2025-06-15T14:30:00Z",
    "last_transaction_at": "2025-06-15T14:00:00Z",
    "created_at": "2025-06-15T14:00:00Z",
    "updated_at": "2025-06-15T14:30:00Z"
  }
]
```

---

## 

[​](https://docs.bluumfinance.com/#step-7-—-withdraw-funds)

Step 7 — Withdraw funds

Sell positions first, then withdraw proceeds to the linked bank account:

```
# Sell the AAPL position
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/orders" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "symbol": "AAPL",
    "side": "sell",
    "type": "market",
    "time_in_force": "day",
    "quantity": "10"
  }'

# Withdraw to linked bank account
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/withdrawals" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: wdr-$(uuidgen)" \
  -d '{
    "amount": "1000.00",
    "currency": "USD",
    "method": "ach",
    "description": "Withdrawal to checking account",
    "plaid_options": {
      "item_id": "'"$PLAID_ITEM_ID"'",
      "account_id": "'"$PLAID_ACCOUNT_ID"'"
    }
  }'
```

Withdrawal status progresses: `pending` → `processing` → `submitted` → `completed`.

---

## 

[​](https://docs.bluumfinance.com/#summary)

Summary

| Step | Endpoint | What it does |
| --- | --- | --- |
| 1 | `POST /investors` | Create investment account with identity, contact, disclosures |
| 2 | `POST /documents` | Upload KYC documents (multipart; `account_id={investor_id}`) |
| 3a | `POST /investors/{investor_id}/funding-sources/plaid/link-token` | Get Plaid Link token for bank connection UI |
| 3b | `POST /investors/{investor_id}/funding-sources/connect` | Exchange Plaid public token to finalize connection |
| 4 | `POST /investors/{investor_id}/deposits` | Deposit funds via ACH or manual bank transfer |
| 5 | `POST /investors/{investor_id}/orders` | Place a buy order |
| 6 | `GET /investors/{investor_id}/positions` | View current holdings |
| 7 | `POST /investors/{investor_id}/withdrawals` | Withdraw proceeds to linked bank account |

## 

[​](https://docs.bluumfinance.com/#next-steps)

Next steps

- [Platform Concepts](https://docs.bluumfinance.com/platform/accounts) — Understand the data model in depth
- [Webhooks](https://docs.bluumfinance.com/platform/webhooks) — Set up real-time event notifications
- [Going Live](https://docs.bluumfinance.com/get-started/go-live) — Production readiness checklist

[Welcome to Bluum](https://docs.bluumfinance.com/get-started/welcome) [Quick Start: Wealth Management](https://docs.bluumfinance.com/get-started/quick-start-wealth)

⌘I