# Source: https://docs.bluumfinance.com/platform/wallets

A **Wallet** holds the cash balance for an investment account. Deposits flow into the wallet, trades debit from it, and withdrawals send funds back to the user’s bank account. Every account gets USD, NGN, and KES wallets automatically when created.

## 

[​](https://docs.bluumfinance.com/#wallet-types)

Wallet types

Bluum supports three wallet types, depending on your integration model:

| Type | Balance source | Best for |
| --- | --- | --- |
| **Hosted** | Bluum manages the balance directly | Most integrations — Bluum tracks all cash |
| **BYO (Bring Your Own)** | Your system provides the balance via an endpoint | Partners who manage their own ledger |
| **Omnibus** | Computed from an immutable double-entry ledger | Partners needing full audit trail |

Most integrations use the **Hosted** wallet type. BYO and Omnibus are available for partners with specific operational requirements.

## 

[​](https://docs.bluumfinance.com/#how-money-flows)

How money flows

```
Bank Account ──deposit──► Wallet ──buy order──► Positions
                            ▲                      │
                            │                      │
                        sell order ◄───────────────┘
                            │
                     ──withdrawal──► Bank Account
```

1. **Deposit** — Money enters the wallet from a linked bank account
2. **Buy order** — Wallet balance decreases; a hold is placed during execution
3. **Sell order** — Proceeds return to the wallet after the order fills
4. **Withdrawal** — Money leaves the wallet to the user’s bank account

## 

[​](https://docs.bluumfinance.com/#wallet-holds)

Wallet holds

When an order is placed, Bluum creates a **hold** on the wallet to reserve funds for the trade. This prevents the user from spending the same money twice.

| Hold status | Meaning |
| --- | --- |
| `ACTIVE` | Funds reserved, order in progress |
| `RELEASED` | Order canceled, funds returned to available balance |
| `CAPTURED` | Order filled, funds debited |
| `EXPIRED` | Hold expired (order timed out) |

## 

[​](https://docs.bluumfinance.com/#balance-fields)

Balance fields

| Field | Description |
| --- | --- |
| `balance` | Total wallet balance (available + held) |
| `available_balance` | Balance available for new trades or withdrawals |
| `reserved_balance` | Balance reserved by active holds |

```
balance = available_balance + reserved_balance
```

## 

[​](https://docs.bluumfinance.com/#transactions)

Transactions

Every wallet operation creates a transaction record:

| Transaction type | Description |
| --- | --- |
| `deposit` | Funds received from bank account |
| `withdrawal` | Funds sent to bank account |
| `buy` | Funds debited for order execution |
| `sell` | Proceeds credited from order fill |
| `fee` | Trading commission or platform fee |

```
# Get wallet details
curl -X GET "$BASE_URL/investors/$INVESTOR_ID/wallets" \
  -H "Authorization: Basic $AUTH"

# Get transaction history
curl -X GET "$BASE_URL/investors/$INVESTOR_ID/transactions" \
  -H "Authorization: Basic $AUTH"
```

## 

[​](https://docs.bluumfinance.com/#key-endpoints)

Key endpoints

| Method | Path | Description |
| --- | --- | --- |
| `GET` | `/investors/{investor_id}/wallets` | Get wallet details and balance |
| `GET` | `/investors/{investor_id}/transactions` | List wallet transactions |

[Compliance & KYC](https://docs.bluumfinance.com/platform/compliance-kyc) [Funding & Transfers](https://docs.bluumfinance.com/platform/funding)

⌘I