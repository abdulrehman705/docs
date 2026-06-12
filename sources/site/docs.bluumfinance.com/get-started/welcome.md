# Source: https://docs.bluumfinance.com/get-started/welcome

Bluum Finance is an investment infrastructure API for fintech companies. We sit between your application and the financial system — handling account onboarding, identity verification, custodian routing, order execution, wallet management, and regulatory compliance. You build the user experience. Bluum provides the rails.

## 

[​](https://docs.bluumfinance.com/#how-it-works)

How it works

```
┌─────────────┐      ┌─────────────────┐      ┌──────────────────┐
│  Your App   │ ───► │   Bluum API     │ ───► │  Financial System │
│  (Frontend) │ ◄─── │  (Orchestrator) │ ◄─── │  (Custodians,    │
└─────────────┘      └─────────────────┘      │   Banks, KYC)    │
                                               └──────────────────┘
```

Your application makes REST calls to the Bluum API. Bluum orchestrates the downstream operations — creating brokerage accounts with custodians, routing orders to exchanges, initiating ACH transfers, running identity verification — and returns clean, normalized responses. Webhook events notify you in real-time when asynchronous operations complete (order fills, deposit arrivals, KYC decisions).

## 

[​](https://docs.bluumfinance.com/#two-integration-models)

Two integration models

Bluum supports two ways to embed investing, depending on your product:

| | Self-Directed Investing | Wealth Management |
| --- | --- | --- |
| **What it is** | Your users decide what to buy and sell | Bluum’s engine manages portfolios on behalf of your users |
| **Account type** | `individual` with `management_type: "self_directed"` | `individual` with `management_type: "advised"` |
| **Who places trades** | End user (through your UI) | Strategy engine (via portfolios and auto-invest) |
| **Wealth APIs needed** | No | Yes (profiles, risk, goals, portfolios) |
| **Typical products** | Trading apps, invest tabs, stock gifting, round-ups | Robo-advisors, financial planner apps, employee benefits |
| **Time to first trade** | ~30 min in sandbox | ~1 hour in sandbox |

Both models share the same foundation: accounts, compliance, wallets, funding, and market data. Wealth Management adds a layer of financial planning, risk assessment, and portfolio automation on top.

## 

[​](https://docs.bluumfinance.com/#core-objects)

Core objects

These are the building blocks you’ll work with across both integration models:

| Object | What it represents |
| --- | --- |
| **Account** | An investment account for one end user. Holds identity, contact info, disclosures, and agreements. |
| **Wallet** | The cash balance for an account. Receives deposits, funds trades, and disburses withdrawals. |
| **Deposit / Withdrawal** | A money movement into or out of a wallet. Supports ACH (via Plaid) and manual bank transfer. |
| **Funding Source** | A linked bank account (via Plaid) used for ACH deposits and withdrawals. |
| **Order** | A buy or sell instruction. Supports market, limit, stop, and trailing stop types. |
| **Position** | A holding in a specific asset — quantity, cost basis, current value, and P&L. |
| **Asset** | A tradable instrument (stock, ETF). Searchable by symbol, name, or asset class. |
| **Webhook** | A registered URL that receives event notifications (order filled, deposit completed, etc.). |

Wealth Management adds:

| Object | What it represents |
| --- | --- |
| **Investor Profile** | Comprehensive financial picture — demographics, employment, tax, insurance, estate planning, preferences. |
| **Risk Assessment** | A questionnaire-based risk tolerance score that drives portfolio construction. |
| **Goal** | A financial objective (retirement, home purchase) with target amount and timeline. |
| **Financial Plan** | A generated plan that maps goals to investment strategies. |
| **Portfolio** | A managed collection of holdings governed by an Investment Policy Statement. |
| **Auto-Invest** | A recurring schedule that automatically invests into a portfolio. |

## 

[​](https://docs.bluumfinance.com/#authentication)

Authentication

All API requests use **HTTP Basic Authentication**. Your API Key is the username, your API Secret is the password.

```
curl -X GET 'https://dev-tapp-api.tappengine.com/v1/assets?asset_class=us_equity' \
  -H 'Authorization: Basic '$(echo -n 'YOUR_API_KEY:YOUR_API_SECRET' | base64)
```

You’ll get separate credentials for sandbox and production. See [Authentication](https://docs.bluumfinance.com/platform/authentication) for details.

## 

[​](https://docs.bluumfinance.com/#environments)

Environments

| Environment | Base URL | Purpose |
| --- | --- | --- |
| **Sandbox** | `https://dev-tapp-api.tappengine.com/v1` | Development and testing. Data resets nightly. Simulated order fills. |
| **Production** | `https://api.tappengine.com/v1` | Live operations. Real money and securities. Requires compliance approval. |

Start in sandbox — no approval needed. See [Sandbox Environment](https://docs.bluumfinance.com/get-started/sandbox) for setup details.

## 

[​](https://docs.bluumfinance.com/#next-steps)

Next steps

## Quick Start: Self-Directed

Create an account, fund it, and place your first trade in under 30 minutes.

## Wealth Management

Offer managed portfolios, financial planning, and automated investing. [Book a demo](https://docs.bluumfinance.com/wealth) to get started.

[Quick Start: Self-Directed Investing](https://docs.bluumfinance.com/get-started/quick-start-self-directed)

⌘I