# Source: https://docs.bluumfinance.com/platform/accounts

An **Account** represents an investment account for one end user. It’s the root object that connects to wallets, orders, positions, and (for wealth management) investor profiles and portfolios.

## 

[​](https://docs.bluumfinance.com/#account-types)

Account types

| Type | Use case | Wealth APIs | Auto-creates |
| --- | --- | --- | --- |
| `individual` | Personal investment account — most common type | Depends on `management_type` | Wallet (+ Investor Profile if `advised`) |
| `joint` | Joint investment account shared by two or more individuals | Depends on `management_type` | Wallet (+ Investor Profile if `advised`) |
| `corporate` | Corporate/business investment account | Depends on `management_type` | Wallet (+ Investor Profile if `advised`) |

### 

[​](https://docs.bluumfinance.com/#management-type)

Management type

The `management_type` field controls whether the account is self-directed or professionally managed:

| Management type | Description | Wealth APIs |
| --- | --- | --- |
| `self_directed` | User decides what to buy and sell (default) | No |
| `advised` | Managed portfolios, financial planning, robo-advisory | Yes |

For example, a self-directed personal account uses `account_type: "individual"` with `management_type: "self_directed"` (the default). A wealth-managed account uses `account_type: "individual"` with `management_type: "advised"`.

## 

[​](https://docs.bluumfinance.com/#account-lifecycle)

Account lifecycle

```
Create Account → KYC Verification → Active → Trading
                                      │        (orders, deposits,
                                      │         withdrawals)
                                      ▼
                                   Inactive / Suspended
```

| Status | Meaning |
| --- | --- |
| `ACTIVE` | Account is open and can trade (sandbox accounts are immediately active) |
| `INACTIVE` | Account is deactivated |
| `SUSPENDED` | Account temporarily suspended |

## 

[​](https://docs.bluumfinance.com/#required-fields)

Required fields

### 

[​](https://docs.bluumfinance.com/#contact)

Contact

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `email_address` | string | Yes | User’s email |
| `phone_number` | string | Yes | E.164 format (e.g., `+14155551234`) |
| `street_address` | string\[\] | Yes | Street address lines |
| `city` | string | Yes | City |
| `state` | string | Yes | State/province code |
| `postal_code` | string | Yes | Postal/ZIP code |
| `country` | string | Yes | ISO 3166-1 alpha-2 (e.g., `US`) |

### 

[​](https://docs.bluumfinance.com/#identity)

Identity

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `first_name` | string | Yes | Legal first name |
| `last_name` | string | Yes | Legal last name |
| `date_of_birth` | string | Yes | `YYYY-MM-DD` format |
| `tax_id` | string | Yes | SSN or ITIN |
| `tax_id_type` | string | Yes | `SSN` or `ITIN` |
| `country_of_citizenship` | string | Yes | ISO 3166-1 alpha-2 |
| `country_of_birth` | string | Yes | ISO 3166-1 alpha-2 |
| `country_of_tax_residence` | string | Yes | ISO 3166-1 alpha-2 |
| `funding_source` | string\[\] | Yes | Source of funds (e.g., `employment_income`, `investments`) |

### 

[​](https://docs.bluumfinance.com/#disclosures)

Disclosures

All boolean, all required:

- `is_control_person` — Is the user a control person of a publicly traded company?
- `is_affiliated_exchange_or_finra` — Is the user affiliated with a stock exchange or FINRA?
- `is_politically_exposed` — Is the user a politically exposed person?
- `immediate_family_exposed` — Is an immediate family member politically exposed?

### 

[​](https://docs.bluumfinance.com/#agreements)

Agreements

At least `investor_agreement` and `investor_agreement` must be signed:

| Field | Type | Description |
| --- | --- | --- |
| `agreement` | string | Agreement type |
| `agreed` | boolean | Must be `true` |
| `signed_at` | string | ISO 8601 timestamp |
| `ip_address` | string | IP address of the signer |

### 

[​](https://docs.bluumfinance.com/#additional-account-fields)

Additional account fields

These optional fields configure trading behavior and tax treatment:

| Field | Type | Default | Description |
| --- | --- | --- | --- |
| `tax_advantaged` | boolean | `false` | Whether the account has tax-advantaged status (IRA, ISA, RRSP) |
| `tax_designation` | string | `null` | Jurisdiction-specific designation (e.g., `traditional_ira`, `roth_ira`, `isa`, `rrsp`) |
| `trading_type` | string | `margin` | Trading type: `margin` or `cash` |
| `enabled_assets` | string\[\] | `["us_equity"]` | Asset classes enabled: `us_equity`, `us_option`, `crypto`, `bonds`, `etf`, `mutual_funds` |

## 

[​](https://docs.bluumfinance.com/#relationships)

Relationships

```
Account
  ├── Wallet (cash balance, deposits, withdrawals)
  ├── Orders (buy/sell instructions)
  ├── Positions (current holdings)
  ├── Documents (KYC uploads)
  ├── Funding Sources (linked bank accounts)
  └── [Wealth Management]
      ├── Investor Profile
      ├── Risk Assessments
      ├── Goals & Life Events
      ├── Financial Plan
      ├── Investment Policy Statement
      └── Portfolios
```

## 

[​](https://docs.bluumfinance.com/#key-endpoints)

Key endpoints

| Method | Path | Description |
| --- | --- | --- |
| `POST` | `/accounts` | Create an account |
| `GET` | `/investors/{investor_id}` | Get account details |
| `GET` | `/accounts` | List all accounts |
| `GET` | `/investors/{investor_id}/wallets` | Get account wallets |
| `GET` | `/investors/{investor_id}/transactions` | Get transaction history |

[Authentication](https://docs.bluumfinance.com/platform/authentication) [Compliance & KYC](https://docs.bluumfinance.com/platform/compliance-kyc)

⌘I