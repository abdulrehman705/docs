# Source: https://docs.bluumfinance.com/resources/postman

The Bluum Postman collection provides ready-to-run requests for every API endpoint, with pre-configured authentication and example payloads.

## 

[​](https://docs.bluumfinance.com/#quick-setup)

Quick setup

1

Import the collection

Open the [Bluum Postman Collection](https://documenter.getpostman.com/view/40898431/2sBXVo97hh) and click **Run in Postman** to import it.

2

Set environment variables

Create a Postman environment with these variables:

| Variable | Value |
| --- | --- |
| `baseUrl` | `https://dev-tapp-api.tappengine.com/v1` |
| `apiKey` | Your sandbox API key |
| `apiSecret` | Your sandbox API secret |

3

Run your first request

Open the **Assets > List Assets** request and click **Send**. You should see a list of tradable assets.

## 

[​](https://docs.bluumfinance.com/#collection-contents)

Collection contents

The collection is organized by API domain. Folders are derived from the OpenAPI `tags`, so this list stays in lockstep with the spec.

| Folder | Requests |
| --- | --- |
| **Investors** | Create, list, get, close investors; wallets, transactions, portfolio chart, trusted contact |
| **Compliance** | Submit a check result, restart a workflow, poll workflow status |
| **Funding Sources** | List/connect/disconnect funding sources, Plaid Link token, PATCH wire-payout details |
| **Transfers** | Create, list, get, and cancel deposits and withdrawals; download wire-details PDF |
| **Trading** | Order estimates, place/list/get/cancel orders |
| **Positions** | List positions; get position detail |
| **Assets** | Search, list, get-by-symbol, historical chart bars |
| **Markets** | Reference data: status, calendar, halts, sessions |
| **Market Data** | Quotes, snapshots, bars, search (paywalled) |
| **Document Management** | Upload, list, get, download, delete documents |
| **Webhooks** | Register, list, update, delete webhooks; list event types |
| **Investor Profile / Dependents / Risk Assessment / Goals / Life Events / External Accounts / Financial Plan / Investment Policy** | Wealth platform onboarding and planning |
| **Portfolio Management / Auto-Invest / Insights & Recommendations / Reports** | Wealth platform portfolio management |

Total: 23 folders, 124 requests.

## 

[​](https://docs.bluumfinance.com/#collection-variables)

Collection variables

The collection uses variables for resource IDs. After creating a resource (e.g., an account), update the corresponding variable so subsequent requests reference it:

| Variable | Set after |
| --- | --- |
| `investor_id` | Creating an investor (prefixed `inv_…`) |
| `document_id` | Uploading a document (prefixed `doc_…`) |
| `order_id` | Placing an order (prefixed `ord_…`) |
| `deposit_id` | Creating a deposit (prefixed `dep_…`) |
| `withdrawal_id` | Creating a withdrawal (prefixed `wdr_…`) |
| `id` | Funding source (prefixed `fs_…`) — used by PATCH / DELETE funding-source |
| `webhook_id` | Registering a webhook |
| `portfolio_id` | Creating a portfolio |

## 

[​](https://docs.bluumfinance.com/#switching-environments)

Switching environments

To switch from sandbox to production:

1. Create a new Postman environment
2. Set `baseUrl` to `https://api.tappengine.com/v1`
3. Use your production API key and secret
4. Select the production environment before sending requests

[SDKs & Libraries](https://docs.bluumfinance.com/resources/sdks) [AI-Assisted Development](https://docs.bluumfinance.com/resources/ai-tools)

⌘I