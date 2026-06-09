# Source: https://docs.bluumfinance.com/guides/self-directed/account-onboarding

This guide covers the full account onboarding flow — collecting user information, submitting the account, and handling edge cases.

## 

[​](https://docs.bluumfinance.com/#overview)

Overview

Account creation requires four data groups:

1. **Contact** — Email, phone, mailing address
2. **Identity** — Legal name, date of birth, tax ID, citizenship
3. **Disclosures** — Regulatory disclosures (control person, political exposure, etc.)
4. **Agreements** — Account and customer agreements with timestamps and IP address

## 

[​](https://docs.bluumfinance.com/#collecting-data-in-your-ui)

Collecting data in your UI

You don’t need to collect all data on a single screen. A common pattern:

```
Screen 1: Name, email, phone
Screen 2: Address
Screen 3: Date of birth, SSN, citizenship, funding source
Screen 4: Disclosures (yes/no checkboxes)
Screen 5: Agreement review and signature
```

Submit the account once all data is collected.

## 

[​](https://docs.bluumfinance.com/#creating-the-account)

Creating the account

```
curl -X POST "$BASE_URL/accounts" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "account_type": "individual",
    "management_type": "self_directed",
    "contact": {
      "email_address": "user@example.com",
      "phone_number": "+14155551234",
      "street_address": ["456 Oak Avenue"],
      "city": "San Francisco",
      "state": "CA",
      "postal_code": "94102",
      "country": "US"
    },
    "identity": {
      "first_name": "Jane",
      "last_name": "Doe",
      "date_of_birth": "1990-05-15",
      "tax_id": "987-65-4321",
      "tax_id_type": "SSN",
      "country_of_citizenship": "US",
      "country_of_birth": "US",
      "country_of_tax_residence": "US",
      "funding_source": ["employment_income"]
    },
    "disclosures": {
      "is_control_person": false,
      "is_affiliated_exchange_or_finra": false,
      "is_politically_exposed": false,
      "immediate_family_exposed": false
    },
    "agreements": [
      {
        "agreement": "investor_agreement",
        "agreed": true,
        "signed_at": "2025-06-15T10:30:00Z",
        "ip_address": "203.0.113.42"
      },
      {
        "agreement": "investor_agreement",
        "agreed": true,
        "signed_at": "2025-06-15T10:30:00Z",
        "ip_address": "203.0.113.42"
      }
    ]
  }'
```

## 

[​](https://docs.bluumfinance.com/#handling-validation-errors)

Handling validation errors

Common validation errors and how to handle them:

| Error | Cause | Fix |
| --- | --- | --- |
| `BLUM-400-002` Missing field | Required field omitted | Check your request against the [Accounts](https://docs.bluumfinance.com/platform/accounts) schema |
| `BLUM-400-003` Invalid format | Wrong data format | Phone: E.164 (`+1...`), dates: `YYYY-MM-DD`, country: ISO alpha-2 |
| `BLUM-409-001` Duplicate | Account with same email exists | Check for existing account before creating |

## 

[​](https://docs.bluumfinance.com/#after-account-creation)

After account creation

Once the account is `ACTIVE`:

1. **Upload KYC documents** — See [KYC Verification](https://docs.bluumfinance.com/guides/self-directed/kyc-verification)
2. **Link a bank account** — See [Bank Linking](https://docs.bluumfinance.com/guides/self-directed/bank-linking)
3. **Fund the account** — See [Deposits & Withdrawals](https://docs.bluumfinance.com/guides/self-directed/deposits-withdrawals)

## 

[​](https://docs.bluumfinance.com/#choosing-account-type)

Choosing account type

| If you’re building… | `account_type` | `management_type` |
| --- | --- | --- |
| Trading app, invest tab, stock gifting | `individual` | `self_directed` |
| Robo-advisor, financial planner, managed portfolios | `individual` | `advised` |

See [Accounts](https://docs.bluumfinance.com/platform/accounts) for full details on account types and lifecycle.

[Integration Guides](https://docs.bluumfinance.com/guides/overview) [KYC Document Verification](https://docs.bluumfinance.com/guides/self-directed/kyc-verification)

⌘I