# Source: https://docs.bluumfinance.com/platform/positions

A **Position** represents the user’s holding in a specific asset — the quantity held, average cost basis, current market value, and unrealized profit or loss. Positions are created automatically when orders fill and updated in real-time as market prices change.

## 

[​](https://docs.bluumfinance.com/#position-object)

Position object

```
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
}
```

| Field | Description |
| --- | --- |
| `id` | Unique position identifier |
| `investor_id` | Investor that holds this position |
| `symbol` | Ticker symbol |
| `asset_id` | Unique asset identifier |
| `currency` | ISO 4217 currency code |
| `quantity` | Number of shares held (can be fractional) |
| `average_cost_basis` | Volume-weighted average purchase price |
| `total_cost_basis` | `quantity * average_cost_basis` |
| `current_price` | Latest market price |
| `market_value` | `quantity * current_price` |
| `unrealized_pl` | `market_value - total_cost_basis` |
| `unrealized_pl_percent` | Unrealized P&L as a percentage |
| `price_source` | Where the price came from (`ALPACA`, `NAYA`, `DATABASE`, `UNAVAILABLE`) |
| `price_confidence` | Freshness of the price (`REAL_TIME`, `DELAYED`, `END_OF_DAY`, `STALE`, `UNAVAILABLE`) |
| `price_timestamp` | When the price was last updated |
| `last_transaction_at` | When the position was last affected by a transaction |
| `created_at` | When the position was created |
| `updated_at` | When the position was last updated |

## 

[​](https://docs.bluumfinance.com/#querying-positions)

Querying positions

```
# All positions for an account
curl -X GET "$BASE_URL/investors/$INVESTOR_ID/positions" \
  -H "Authorization: Basic $AUTH"

# Specific position
curl -X GET "$BASE_URL/investors/$INVESTOR_ID/positions/$POSITION_ID" \
  -H "Authorization: Basic $AUTH"
```

## 

[​](https://docs.bluumfinance.com/#how-positions-update)

How positions update

| Event | Effect on position |
| --- | --- |
| Buy order fills | Creates or increases position; recalculates `average_cost_basis` |
| Sell order fills | Decreases position; removes if fully sold |
| Market price changes | Updates `current_price`, `market_value`, `unrealized_pl` |
| Stock split | Adjusts `quantity` and `average_cost_basis` proportionally |

## 

[​](https://docs.bluumfinance.com/#key-endpoints)

Key endpoints

| Method | Path | Description |
| --- | --- | --- |
| `GET` | `/investors/{investor_id}/positions` | List all positions |
| `GET` | `/investors/{investor_id}/positions/{position_id}` | Get position details |

[Assets & Market Data](https://docs.bluumfinance.com/platform/assets) [Webhooks](https://docs.bluumfinance.com/platform/webhooks)

⌘I