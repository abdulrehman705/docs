# Source: https://docs.bluumfinance.com/api-reference/introduction

This section contains the auto-generated reference for every Bluum API endpoint — request parameters, response schemas, and interactive examples.

## 

[​](https://docs.bluumfinance.com/#base-urls)

Base URLs

| Environment | URL |
| --- | --- |
| **Sandbox** | `https://test-service.bluumfinance.com/v1` |
| **Production** | `https://service.bluumfinance.com/v1` |

## 

[​](https://docs.bluumfinance.com/#authentication)

Authentication

HTTP Basic Auth with your API Key (username) and API Secret (password). See [Authentication](https://docs.bluumfinance.com/platform/authentication) for details.

## 

[​](https://docs.bluumfinance.com/#endpoint-groups)

Endpoint groups

| Group | Description |
| --- | --- |
| **Accounts** | Create, read, and manage investment accounts |
| **Documents** | Upload and track KYC verification documents |
| **Trading** | Place and manage orders |
| **Positions** | View account holdings and P&L |
| **Assets** | Search and list tradable instruments |
| **Funding Sources** | Link bank accounts via Plaid |
| **Transfers** | Deposits and withdrawals |
| **Market Data** | Quotes, bars, snapshots, market status |
| **Webhooks** | Event notification management |
| **Wealth Management** | Profiles, risk, goals, portfolios, insights |

## 

[​](https://docs.bluumfinance.com/#conventions)

Conventions

- **Decimal values** are strings to preserve precision (e.g., `"178.50"`)
- **Dates** use ISO 8601 format (`2025-06-15T14:30:00Z`)
- **IDs** are UUID v4 format
- **Errors** follow the `BLUM-{STATUS}-{SEQ}` pattern — see [Error Handling](https://docs.bluumfinance.com/platform/errors)
- **Rate limits** — Sandbox: 10 req/s, Production: 25 req/s

## 

[​](https://docs.bluumfinance.com/#interactive-playground)

Interactive playground

Use the playground on each endpoint page to make live requests against the sandbox. Enter your API Key and Secret in the authentication fields.

## 

[​](https://docs.bluumfinance.com/#additional-resources)

Additional resources

- [Postman Collection](https://docs.bluumfinance.com/resources/postman) — Pre-built requests for every endpoint
- [SDKs](https://docs.bluumfinance.com/resources/sdks) — Client libraries and code generation
- [Error Code Reference](https://docs.bluumfinance.com/resources/error-codes) — Complete error catalog

⌘I