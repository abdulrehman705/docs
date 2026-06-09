# Source: https://docs.bluumfinance.com/resources/glossary

| Term | Definition |
| --- | --- |
| **Account** | An investment account for one end user. Holds identity, contact, disclosures, and links to wallets, orders, and positions. |
| **ACH** | Automated Clearing House — electronic bank transfer network used for deposits and withdrawals. |
| **Asset** | A tradable financial instrument (stock, ETF) available through Bluum. |
| **Auto-Invest** | A recurring schedule that automatically invests a fixed amount into a portfolio on a set frequency. |
| **BYO Wallet** | Bring Your Own wallet — the partner manages the balance externally and provides it via an endpoint. |
| **Client Order ID** | A partner-assigned identifier for an order, used for reconciliation. |
| **Cost Basis** | The total purchase price of a position (`quantity * average entry price`). |
| **Custodian** | A regulated financial institution that holds securities on behalf of investors. Bluum routes to custodians automatically. |
| **DRIP** | Dividend Reinvestment Plan — automatically reinvests dividends back into the portfolio. |
| **Deposit** | A money movement from a bank account into an investment wallet. |
| **Financial Plan** | A generated strategy that maps the investor’s goals, risk profile, and finances into an actionable investment approach. |
| **Fractional Shares** | Owning less than one full share of a stock or ETF. Enabled via notional orders. |
| **Funding Source** | A linked bank account (via Plaid) used for ACH transfers. |
| **Goal** | A financial objective (retirement, education, home purchase) with a target amount and timeline. |
| **Hold** | A temporary reservation on wallet funds during order execution. Released if the order is canceled, captured if filled. |
| **Hosted Wallet** | Default wallet type where Bluum manages the cash balance directly. |
| **Idempotency Key** | A unique request identifier that prevents duplicate financial operations on retry. |
| **Individual Account** | Account type for wealth management — includes investor profile, risk assessment, and portfolio management. |
| **Investor Profile** | Comprehensive financial picture: demographics, employment, tax, insurance, estate, cash flow, and preferences. |
| **IPS** | Investment Policy Statement — the governing document for a managed portfolio. Defines objectives, constraints, allocation targets, and rebalancing rules. |
| **KYC** | Know Your Customer — identity verification process required before trading. |
| **Life Event** | A significant personal event (marriage, job change, inheritance) that affects the financial plan. |
| **Limit Order** | An order that executes only at the specified price or better. |
| **Market Data** | Real-time and historical pricing, quotes, bars, and market status information. |
| **Market Order** | An order that executes immediately at the current market price. |
| **Notional Order** | An order specified in dollar amount rather than share quantity. Results in fractional shares. |
| **Omnibus Wallet** | Wallet type with an immutable double-entry ledger for full audit trails. |
| **Plaid** | Third-party service for secure bank account linking and ACH transfers. |
| **Portfolio** | A managed collection of holdings governed by an IPS. Supports rebalancing, auto-invest, and DRIP. |
| **Position** | A holding in a specific asset — quantity, cost basis, current value, and P&L. |
| **Rebalancing** | Adjusting portfolio holdings to return to the target asset allocation defined in the IPS. |
| **Risk Assessment** | A questionnaire-based evaluation that determines the investor’s risk tolerance and recommended allocation. |
| **Sandbox** | Development environment with simulated trading and funding. Data resets nightly. |
| **Snapshot** | A point-in-time record of an investor profile, used for compliance and auditing. |
| **Stop Order** | An order that triggers a market order when the price reaches a specified level. Used for loss protection. |
| **Tenant** | The top-level entity representing a partner company. All resources (accounts, orders, etc.) are scoped to a tenant. |
| **Time in Force** | How long an order remains active: `day` (current session), `gtc` (until filled/canceled), `ioc` (immediate or cancel), `fok` (fill or kill). |
| **Self-Directed Account** | An `individual` account with `management_type: "self_directed"` — users decide what to buy and sell. |
| **Trailing Stop** | A stop order where the stop price follows the market price by a fixed offset. |
| **Wallet** | The cash balance container for an investment account. Receives deposits, funds trades, disburses withdrawals. |
| **Webhook** | A registered URL that receives HTTP POST notifications when events occur (order fills, deposit completions, etc.). |
| **Withdrawal** | A money movement from an investment wallet to a bank account. |

[Error Code Reference](https://docs.bluumfinance.com/resources/error-codes) [Getting Help](https://docs.bluumfinance.com/resources/support)

⌘I