# Source: https://docs.bluumfinance.com/guides/operations/testing-and-sandbox

The sandbox simulates the full Bluum environment — accounts, trading, funding, and webhooks. This guide covers how to test each part of your integration systematically.

## 

[​](https://docs.bluumfinance.com/#test-scenarios-by-domain)

Test scenarios by domain

### 

[​](https://docs.bluumfinance.com/#account-creation)

Account creation

| Scenario | How to test |
| --- | --- |
| Successful creation | Submit valid account data → verify `ACTIVE` status |
| Validation failure | Omit required fields → verify error response |
| Duplicate account | Create two accounts with same email → verify `409` conflict |

### 

[​](https://docs.bluumfinance.com/#kyc-verification)

KYC verification

| Scenario | How to test |
| --- | --- |
| Approval | Upload any valid image → auto-approved in sandbox |
| Rejection | Use test rejection values (see dashboard) |
| Missing document | Attempt operations before KYC → verify error |

### 

[​](https://docs.bluumfinance.com/#deposits)

Deposits

| Scenario | How to test |
| --- | --- |
| ACH success | Create deposit with valid Plaid options → transitions to `completed` |
| Manual transfer | Create manual deposit → verify bank details in response |
| Insufficient funds | Create withdrawal exceeding balance → verify `422` error |
| Idempotency | Submit same deposit twice with same key → verify no duplicate |
| Idempotency conflict | Submit different body with same key → verify error |

### 

[​](https://docs.bluumfinance.com/#trading)

Trading

| Scenario | How to test |
| --- | --- |
| Market buy | Place market order during hours → verify `filled` status |
| Limit order | Place limit below market → remains `accepted` |
| Fractional buy | Use `notional` for dollar-based purchase |
| Insufficient funds | Place order exceeding wallet balance |
| Invalid symbol | Use non-existent symbol → verify error |

### 

[​](https://docs.bluumfinance.com/#webhooks)

Webhooks

| Scenario | How to test |
| --- | --- |
| Event delivery | Register webhook, place order → verify event received |
| Duplicate delivery | Process same event twice → verify idempotent handling |
| Failure retry | Return 500 from webhook → verify Bluum retries |

## 

[​](https://docs.bluumfinance.com/#sandbox-specific-behaviors)

Sandbox-specific behaviors

### 

[​](https://docs.bluumfinance.com/#timing)

Timing

- Market orders fill instantly (no execution delay)
- ACH deposits complete in seconds (not days)
- KYC documents auto-approve immediately

### 

[​](https://docs.bluumfinance.com/#data-reset)

Data reset

- Sandbox data resets nightly at midnight UTC
- All accounts, orders, and deposits are cleared
- Webhook registrations persist across resets

### 

[​](https://docs.bluumfinance.com/#plaid-sandbox)

Plaid sandbox

- Use Plaid sandbox credentials (provided in your dashboard)
- Test bank: “First Platypus Bank” with credentials `user_good` / `pass_good`

## 

[​](https://docs.bluumfinance.com/#integration-test-checklist)

Integration test checklist

Run through this checklist before going live:

- [ ] Account creation with all required fields
- [ ] Account creation with missing fields (error handling)
- [ ] KYC document upload and status check
- [ ] Plaid Link token creation
- [ ] Plaid public token exchange
- [ ] ACH deposit with idempotency key
- [ ] Manual bank transfer deposit
- [ ] Deposit status tracking (polling or webhook)
- [ ] Market buy order
- [ ] Limit buy order
- [ ] Position query after fill
- [ ] Sell order
- [ ] Withdrawal with idempotency key
- [ ] Withdrawal cancellation
- [ ] Webhook registration and event receipt
- [ ] Error handling for all 4xx/5xx responses
- [ ] Rate limit handling (429 with backoff)

## 

[​](https://docs.bluumfinance.com/#automated-testing-tips)

Automated testing tips

```
// Use environment variables for easy switching
const config = {
  baseUrl: process.env.MARKET_BASE_URL || 'https://dev-tapp-api.tappengine.com/v1',
  apiKey: process.env.BLUUM_API_KEY,
  apiSecret: process.env.BLUUM_API_SECRET
};

// Generate unique idempotency keys per test run
const idempotencyKey = `test-${Date.now()}-${Math.random().toString(36).slice(2)}`;

// Clean up: note that sandbox resets nightly, but you may want to
// cancel pending orders and withdrawals between test runs
```

## 

[​](https://docs.bluumfinance.com/#next-steps)

Next steps

When all tests pass, follow the [Going Live](https://docs.bluumfinance.com/get-started/go-live) checklist to move to production.

[Working with Market Data](https://docs.bluumfinance.com/guides/operations/market-data)

⌘I