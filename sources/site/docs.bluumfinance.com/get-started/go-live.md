# Source: https://docs.bluumfinance.com/get-started/go-live

This page covers everything you need to move from sandbox to production.

## 

[​](https://docs.bluumfinance.com/#production-readiness-checklist)

Production readiness checklist

Before requesting production access, confirm you’ve completed each item:

1

Integration tested in sandbox

Your integration handles the full lifecycle you plan to support in production:

- Account creation and KYC verification
- Bank account linking and funding
- Order placement, status tracking, and fills
- Withdrawal processing
- Error handling for all failure scenarios
- Webhook event processing (if using webhooks)

2

Idempotency keys implemented

All deposit and withdrawal requests include an `Idempotency-Key` header to prevent duplicate transfers on retry.

3

Error handling is robust

Your integration handles:

- `401` authentication failures (credential rotation, expiry)
- `429` rate limit responses (exponential backoff with jitter)
- `400` validation errors (surface to users or log for debugging)
- `500` server errors (retry with backoff, alert on repeated failures)
- Network timeouts (retry with idempotency key)

See [Error Handling](https://docs.bluumfinance.com/platform/errors) for the complete error model.

4

Webhook endpoints secured

If you use webhooks:

- Endpoint is HTTPS only
- Verify webhook signatures before processing
- Respond with `2xx` within 30 seconds
- Handle duplicate deliveries idempotently
- Alert on delivery failures

See [Webhooks](https://docs.bluumfinance.com/platform/webhooks) for implementation details.

5

Credentials stored securely

- API keys and secrets are in a secret manager (not code, not config files)
- Credentials are scoped to service accounts, not individual developers
- You have a key rotation process ready

6

Compliance requirements met

Contact your Bluum account manager to confirm:

- Your KYC document collection meets regulatory requirements
- Required disclosures and agreements are presented to end users
- Your record-keeping meets applicable regulations

## 

[​](https://docs.bluumfinance.com/#requesting-production-access)

Requesting production access

1. Email `support@bluum.finance` with:
 - Your company name and dashboard account email
 - A description of your product and integration model (Self-Directed or Wealth Management)
 - Confirmation that you’ve completed the readiness checklist above
2. Bluum reviews your sandbox integration and compliance setup
3. Upon approval, you receive production API credentials

## 

[​](https://docs.bluumfinance.com/#deploying-to-production)

Deploying to production

1

Update environment configuration

Switch your base URL and credentials to production values:

```
export MARKET_BASE_URL="https://api.tappengine.com/v1"
export BLUUM_API_KEY="your_production_key"
export BLUUM_API_SECRET="your_production_secret"
```

2

Smoke test

Verify connectivity with a read-only request:

```
curl -X GET "$MARKET_BASE_URL/assets?asset_class=us_equity&tradable=true" \
  -H "Authorization: Basic $(echo -n "$BLUUM_API_KEY:$BLUUM_API_SECRET" | base64)"
```

Confirm you receive a `200` response. If you see `401`, verify both key and secret are updated.

3

Monitor initial operations

Watch the first few production operations closely:

- First account creation and KYC flow
- First deposit and order
- Webhook delivery to your production endpoint

Log `X-Request-Id` headers from every response for troubleshooting.

## 

[​](https://docs.bluumfinance.com/#credential-rotation)

Credential rotation

Rotate API keys every 90 days:

1. Generate a new key pair in the dashboard
2. Deploy the new credentials to your production environment
3. Verify connectivity with the new credentials
4. Revoke the old key pair in the dashboard

Overlap old and new keys during rotation. Generate the new key, deploy it, verify it works, then revoke the old one. Never revoke before verifying the new key.

## 

[​](https://docs.bluumfinance.com/#observability)

Observability

### 

[​](https://docs.bluumfinance.com/#request-logging)

Request logging

Log every API request and response, including:

- HTTP method and path
- Response status code
- `X-Request-Id` header (include in support tickets)
- Response time

### 

[​](https://docs.bluumfinance.com/#monitoring)

Monitoring

Set up alerts for:

- Elevated error rates (5xx responses)
- Rate limit hits (429 responses)
- Orders stuck in `accepted` status for longer than expected
- Webhook delivery failures

## 

[​](https://docs.bluumfinance.com/#support)

Support

| Channel | Use for |
| --- | --- |
| `support@bluum.finance` | Credentials, access, compliance, general questions |
| `ope@bluumfinance.com` | Integration help, technical questions |
| Emergency trading desk: `+1 (415) 555-0112` | Urgent production issues during market hours (24/5) |

[Sandbox Environment](https://docs.bluumfinance.com/get-started/sandbox)

⌘I