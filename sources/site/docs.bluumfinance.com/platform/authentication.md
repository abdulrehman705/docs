# Source: https://docs.bluumfinance.com/platform/authentication

All Bluum API requests use **HTTP Basic Authentication**. Your API Key is the username and your API Secret is the password.

## 

[​](https://docs.bluumfinance.com/#how-it-works)

How it works

1. Concatenate your API Key and Secret with a colon: `API_KEY:API_SECRET`
2. Base64-encode the result
3. Send it in the `Authorization` header as `Basic <encoded_value>`

cURL

Node.js

Python

```
curl -X GET 'https://test-service.bluumfinance.com/v1/assets' \
  -H 'Authorization: Basic '$(echo -n 'YOUR_API_KEY:YOUR_API_SECRET' | base64)
```

## 

[​](https://docs.bluumfinance.com/#required-headers)

Required headers

| Header | Required | Description |
| --- | --- | --- |
| `Authorization` | Always | `Basic <base64(API_KEY:API_SECRET)>` |
| `Content-Type` | POST/PUT | `application/json` for JSON payloads, `multipart/form-data` for file uploads |
| `Idempotency-Key` | Deposits/Withdrawals | Unique key to prevent duplicate operations on retry |

## 

[​](https://docs.bluumfinance.com/#credentials)

Credentials

You receive separate credentials for each environment:

| Environment | Base URL | How to get |
| --- | --- | --- |
| Sandbox | `https://test-service.bluumfinance.com/v1` | Dashboard → Settings → API Keys |
| Production | `https://service.bluumfinance.com/v1` | Issued after compliance approval |

The API Secret is shown only once when created. Store it immediately in a secret manager. If lost, generate a new key pair.

## 

[​](https://docs.bluumfinance.com/#security-best-practices)

Security best practices

- Store credentials in a secret manager (AWS Secrets Manager, HashiCorp Vault, Doppler) — never in code or config files
- Rotate keys every 90 days
- Scope credential access to service accounts, not individual developers
- Use environment variables to switch between sandbox and production
- Never log or transmit credentials in plaintext

## 

[​](https://docs.bluumfinance.com/#common-authentication-errors)

Common authentication errors

| Error Code | Meaning | Resolution |
| --- | --- | --- |
| `BLUM-401-001` | Missing Authorization header | Include `Authorization: Basic <credentials>` |
| `BLUM-401-002` | Malformed credentials | Verify Base64 encoding of `API_KEY:API_SECRET` |
| `BLUM-401-003` | Invalid API key or secret | Check credentials are correct and not revoked |
| `BLUM-401-004` | API key inactive | Contact support to activate your key |

[Platform Overview](https://docs.bluumfinance.com/platform/overview) [Accounts](https://docs.bluumfinance.com/platform/accounts)

⌘I