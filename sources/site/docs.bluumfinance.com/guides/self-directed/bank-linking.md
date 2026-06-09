# Source: https://docs.bluumfinance.com/guides/self-directed/bank-linking

Bank linking uses Plaid to securely connect the user’s bank account for ACH transfers. This is a frontend + backend flow involving Plaid Link.

## 

[​](https://docs.bluumfinance.com/#flow-overview)

Flow overview

```
Your Frontend                    Your Backend                   Bluum API
     │                                │                            │
     │  User clicks "Link Bank"       │                            │
     │───────────────────────────────►│                            │
     │                                │── POST link-token ────────►│
     │                                │◄── { link_token } ─────────│
     │◄── Open Plaid Link ───────────│                            │
     │                                │                            │
     │  User selects bank & logs in   │                            │
     │                                │                            │
     │── public_token ───────────────►│                            │
     │                                │── POST connect ───────────►│
     │                                │◄── { bank details } ───────│
     │◄── "Bank linked!" ────────────│                            │
```

## 

[​](https://docs.bluumfinance.com/#step-1-create-a-link-token)

Step 1: Create a Link token

Your backend requests a Link token from Bluum:

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/funding-sources/plaid/link-token" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{ "enable_hosted_link": false }'
```

Response:

```
{
  "status": "success",
  "data": {
    "link_token": "link-sandbox-abc123def456",
    "hosted_link_url": null
  }
}
```

## 

[​](https://docs.bluumfinance.com/#step-2-launch-plaid-link-in-your-frontend)

Step 2: Launch Plaid Link in your frontend

Use the Plaid Link SDK with the token:

```
const { open } = usePlaidLink({
  token: linkToken, // from step 1
  onSuccess: (publicToken, metadata) => {
    // Send publicToken to your backend
    exchangeToken(publicToken);
  },
  onExit: (err, metadata) => {
    // User closed Plaid Link
  }
});
```

### 

[​](https://docs.bluumfinance.com/#hosted-link-alternative)

Hosted Link alternative

Set `enable_hosted_link: true` to get a Plaid-hosted URL instead. Redirect the user to `hosted_link_url` — no Plaid SDK needed. Useful for mobile apps or simpler integrations.

## 

[​](https://docs.bluumfinance.com/#step-3-exchange-the-public-token)

Step 3: Exchange the public token

After the user completes Plaid Link, send the `public_token` to Bluum:

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/funding-sources/connect" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{ "type": "plaid", "public_token": "public-sandbox-abc123def456" }'
```

Response:

```
{
  "status": "success",
  "data": {
    "item": {
      "itemId": "item_abc123",
      "institutionName": "Chase Bank",
      "accounts": [
        {
          "accountId": "acc_1234567890",
          "accountName": "Checking Account",
          "accountType": "depository",
          "mask": "0000"
        }
      ]
    }
  }
}
```

Store `itemId` and `accountId` — you’ll need them for deposits and withdrawals.

## 

[​](https://docs.bluumfinance.com/#managing-funding-sources)

Managing funding sources

```
# List linked funding sources
curl -X GET "$BASE_URL/investors/$INVESTOR_ID/funding-sources" \
  -H "Authorization: Basic $AUTH"

# Get a specific funding source
curl -X GET "$BASE_URL/investors/$INVESTOR_ID/funding-sources/$FUNDING_SOURCE_ID" \
  -H "Authorization: Basic $AUTH"
```

## 

[​](https://docs.bluumfinance.com/#multiple-bank-accounts)

Multiple bank accounts

Users can link multiple bank accounts. Each `connect` call adds a new funding source. When making deposits or withdrawals, specify which bank account to use via `plaid_options.item_id` and `plaid_options.account_id`.

## 

[​](https://docs.bluumfinance.com/#next-steps)

Next steps

- [Make a deposit](https://docs.bluumfinance.com/guides/self-directed/deposits-withdrawals)
- [Place an order](https://docs.bluumfinance.com/guides/self-directed/placing-orders)

[KYC Document Verification](https://docs.bluumfinance.com/guides/self-directed/kyc-verification) [Deposits & Withdrawals](https://docs.bluumfinance.com/guides/self-directed/deposits-withdrawals)

⌘I