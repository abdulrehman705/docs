# Source: https://docs.bluumfinance.com/get-started/sandbox

The sandbox is a fully functional replica of the Bluum production environment. It simulates order execution, deposit processing, and KYC verification — without touching real money or securities.

## 

[​](https://docs.bluumfinance.com/#sandbox-vs-production)

Sandbox vs Production

| | Sandbox | Production |
| --- | --- | --- |
| **Base URL** | `https://test-service.bluumfinance.com/v1` | `https://service.bluumfinance.com/v1` |
| **Data persistence** | Resets nightly at midnight UTC | Permanent |
| **Order execution** | Simulated fills (instant for market orders) | Real exchange execution |
| **Deposits** | Simulated ACH processing | Real bank transfers |
| **KYC verification** | Auto-approved in most cases | Real identity verification |
| **Rate limit** | 10 requests/second | 25 requests/second |
| **Access** | Immediate after signup | Requires compliance approval |

## 

[​](https://docs.bluumfinance.com/#getting-credentials)

Getting credentials

1

Create a dashboard account

Visit the [Bluum Finance dashboard](https://dashboard.bluumfinance.com/login) and sign up with your email and company information.

2

Generate API keys

Navigate to **Settings > API Keys** in the dashboard. Click **Create API Key** and copy both the API Key and API Secret immediately — the secret is only shown once.

Store your API secret securely. If you lose it, you’ll need to generate a new key pair.

3

Test your credentials

```
curl -X GET 'https://test-service.bluumfinance.com/v1/assets?asset_class=us_equity&tradable=true' \
  -H 'Authorization: Basic '$(echo -n 'YOUR_API_KEY:YOUR_API_SECRET' | base64)
```

A successful response returns a JSON array of tradable assets.

## 

[​](https://docs.bluumfinance.com/#setting-up-postman)

Setting up Postman

The Bluum Postman collection includes pre-configured requests for every endpoint.

1. Import the collection from our [Postman documentation](https://documenter.getpostman.com/view/40898431/2sBXVo97hh)
2. Set environment variables:
 - `baseUrl` = `https://test-service.bluumfinance.com/v1`
 - `apiKey` = your sandbox API key
 - `apiSecret` = your sandbox API secret
3. The collection handles Base64 encoding automatically

See [Postman Collection](https://docs.bluumfinance.com/resources/postman) for detailed setup instructions.

## 

[​](https://docs.bluumfinance.com/#sandbox-behaviors)

Sandbox behaviors

### 

[​](https://docs.bluumfinance.com/#order-execution)

Order execution

Market orders fill instantly at the current simulated price. Limit orders fill when the simulated price crosses the limit. Orders placed outside market hours queue until the next simulated open.

### 

[​](https://docs.bluumfinance.com/#deposits-and-withdrawals)

Deposits and withdrawals

ACH deposits via Plaid transition through `pending` → `processing` → `completed` within seconds. Manual bank transfer deposits remain in `pending` until you explicitly trigger completion (useful for testing status transitions).

### 

[​](https://docs.bluumfinance.com/#kyc-verification)

KYC verification

Document uploads are auto-approved in sandbox. To test rejection scenarios, use specific test values documented in your dashboard.

### 

[​](https://docs.bluumfinance.com/#webhooks)

Webhooks

Webhook events fire in sandbox just like production. Register a webhook endpoint to receive real-time notifications during development. Tools like [webhook.site](https://webhook.site) or [ngrok](https://ngrok.com) are useful for local testing.

## 

[​](https://docs.bluumfinance.com/#environment-variable-setup)

Environment variable setup

Use environment variables to switch between sandbox and production without code changes:

```
# Sandbox
export BLUUM_BASE_URL="https://test-service.bluumfinance.com/v1"
export BLUUM_API_KEY="your_sandbox_key"
export BLUUM_API_SECRET="your_sandbox_secret"
```

```
const BASE_URL = process.env.BLUUM_BASE_URL;
const credentials = Buffer.from(
  `${process.env.BLUUM_API_KEY}:${process.env.BLUUM_API_SECRET}`
).toString('base64');
```

Never hardcode credentials. Use a secret manager (AWS Secrets Manager, HashiCorp Vault, Doppler) or environment variables.

## 

[​](https://docs.bluumfinance.com/#next-steps)

Next steps

- [Quick Start: Self-Directed Investing](https://docs.bluumfinance.com/get-started/quick-start-self-directed) — Make your first trade
- [Wealth Management](https://docs.bluumfinance.com/wealth) — Book a demo to learn about managed portfolios
- [Going Live](https://docs.bluumfinance.com/get-started/go-live) — Production readiness checklist

[Quick Start: Wealth Management](https://docs.bluumfinance.com/get-started/quick-start-wealth) [Going Live](https://docs.bluumfinance.com/get-started/go-live)

⌘I