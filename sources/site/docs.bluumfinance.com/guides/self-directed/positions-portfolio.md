# Source: https://docs.bluumfinance.com/guides/self-directed/positions-portfolio

After orders fill, positions appear in the account. This guide covers how to query, interpret, and display position data.

## 

[​](https://docs.bluumfinance.com/#listing-positions)

Listing positions

```
curl -X GET "$BASE_URL/investors/$INVESTOR_ID/positions" \
  -H "Authorization: Basic $AUTH"
```

```
[
  {
    "id": "pos_a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "investor_id": "inv_01j9x8m2k7qpzwv3t5r6y8n0ab",
    "symbol": "AAPL",
    "asset_id": "asset_9f8e7d6c-5b4a-3210-fedc-ba0987654321",
    "currency": "USD",
    "quantity": "10",
    "average_cost_basis": "178.50",
    "total_cost_basis": "1785.00",
    "current_price": "182.30",
    "market_value": "1823.00",
    "unrealized_pl": "38.00",
    "unrealized_pl_percent": "0.0213",
    "price_source": "ALPACA",
    "price_confidence": "REAL_TIME",
    "price_timestamp": "2025-06-15T14:30:00Z",
    "last_transaction_at": "2025-06-15T14:00:00Z",
    "created_at": "2025-06-15T14:00:00Z",
    "updated_at": "2025-06-15T14:30:00Z"
  },
  {
    "id": "pos_b2c3d4e5-f6a7-8901-bcde-f12345678901",
    "investor_id": "inv_01j9x8m2k7qpzwv3t5r6y8n0ab",
    "symbol": "GOOGL",
    "asset_id": "asset_8e7d6c5b-4a32-10fe-dcba-098765432100",
    "currency": "USD",
    "quantity": "5.42",
    "average_cost_basis": "184.50",
    "total_cost_basis": "999.99",
    "current_price": "186.10",
    "market_value": "1008.66",
    "unrealized_pl": "8.67",
    "unrealized_pl_percent": "0.0087",
    "price_source": "ALPACA",
    "price_confidence": "REAL_TIME",
    "price_timestamp": "2025-06-15T14:30:00Z",
    "last_transaction_at": "2025-06-15T13:45:00Z",
    "created_at": "2025-06-15T13:45:00Z",
    "updated_at": "2025-06-15T14:30:00Z"
  }
]
```

## 

[​](https://docs.bluumfinance.com/#position-detail)

Position detail

```
curl -X GET "$BASE_URL/investors/$INVESTOR_ID/positions/$POSITION_ID" \
  -H "Authorization: Basic $AUTH"
```

## 

[​](https://docs.bluumfinance.com/#understanding-p&l-fields)

Understanding P&L fields

| Field | Calculation | Use for |
| --- | --- | --- |
| `total_cost_basis` | `quantity * average_cost_basis` | What the user paid |
| `market_value` | `quantity * current_price` | What it’s worth now |
| `unrealized_pl` | `market_value - total_cost_basis` | Dollar gain/loss |
| `unrealized_pl_percent` | `unrealized_pl / total_cost_basis` | Percentage gain/loss |

**Unrealized** means the gain/loss is on paper — the user hasn’t sold yet. Once they sell, it becomes **realized** P&L.

## 

[​](https://docs.bluumfinance.com/#displaying-positions-in-your-ui)

Displaying positions in your UI

Common patterns for showing positions to users:

### 

[​](https://docs.bluumfinance.com/#portfolio-summary)

Portfolio summary

- Total market value (sum of all `market_value`)
- Total unrealized P&L (sum of all `unrealized_pl`)
- Overall percentage return

### 

[​](https://docs.bluumfinance.com/#per-position-row)

Per-position row

- Symbol and name
- Quantity held
- Current price
- Market value
- Unrealized P&L (with color: green for positive, red for negative)
- Percentage change

### 

[​](https://docs.bluumfinance.com/#real-time-updates)

Real-time updates

Positions update as market prices change. To keep your UI current:

- **Polling** — Query positions periodically (e.g., every 30 seconds during market hours)
- **Webhooks** — Subscribe to `order.filled` events to know when new positions are created or existing ones change

## 

[​](https://docs.bluumfinance.com/#when-positions-update)

When positions update

| Event | Effect |
| --- | --- |
| Buy order fills | New position created, or existing position increased |
| Sell order fills | Position decreased or removed (if fully sold) |
| Price change | `current_price`, `market_value`, `unrealized_pl` updated |
| Stock split | `quantity` and `average_cost_basis` adjusted proportionally |

## 

[​](https://docs.bluumfinance.com/#empty-positions)

Empty positions

When a user sells their entire holding in an asset, the position is removed from the list. A `GET /positions` response only includes assets the user currently holds.

[Placing Orders](https://docs.bluumfinance.com/guides/self-directed/placing-orders) [Setting Up Webhooks](https://docs.bluumfinance.com/guides/operations/webhook-setup)

⌘I