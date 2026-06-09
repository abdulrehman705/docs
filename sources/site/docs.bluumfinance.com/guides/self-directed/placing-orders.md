# Source: https://docs.bluumfinance.com/guides/self-directed/placing-orders

This guide covers every order type, common patterns, and how to track order execution.

## 

[​](https://docs.bluumfinance.com/#market-orders)

Market orders

Execute immediately at the current market price.

### 

[​](https://docs.bluumfinance.com/#buy-by-quantity)

Buy by quantity

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/orders" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "symbol": "AAPL",
    "side": "buy",
    "type": "market",
    "time_in_force": "day",
    "quantity": "10"
  }'
```

### 

[​](https://docs.bluumfinance.com/#buy-by-dollar-amount-fractional-shares)

Buy by dollar amount (fractional shares)

Invest exactly $1,000 in GOOGL — Bluum calculates the fractional quantity:

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/orders" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "symbol": "GOOGL",
    "side": "buy",
    "type": "market",
    "time_in_force": "day",
    "notional": "1000.00"
  }'
```

`notional` is only available for `market` orders on `fractionable` assets.

## 

[​](https://docs.bluumfinance.com/#limit-orders)

Limit orders

Execute only at the specified price or better:

```
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/orders" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "symbol": "MSFT",
    "side": "buy",
    "type": "limit",
    "time_in_force": "gtc",
    "quantity": "5",
    "limit_price": "350.00",
    "client_order_id": "my-limit-001"
  }'
```

## 

[​](https://docs.bluumfinance.com/#stop-orders)

Stop orders

Trigger a market order when the price reaches the stop level:

```
# Stop-loss: sell if AAPL drops to $170
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/orders" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "symbol": "AAPL",
    "side": "sell",
    "type": "stop",
    "time_in_force": "gtc",
    "quantity": "10",
    "stop_price": "170.00"
  }'
```

## 

[​](https://docs.bluumfinance.com/#trailing-stop-orders)

Trailing stop orders

The stop price follows the market price by a fixed offset:

```
# Sell if AAPL drops 5% from its peak
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/orders" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "symbol": "AAPL",
    "side": "sell",
    "type": "trailing_stop",
    "time_in_force": "gtc",
    "quantity": "10",
    "trail_percent": "5.0"
  }'
```

## 

[​](https://docs.bluumfinance.com/#tracking-order-status)

Tracking order status

```
# Get a specific order
curl -X GET "$BASE_URL/investors/$INVESTOR_ID/orders/$ORDER_ID" \
  -H "Authorization: Basic $AUTH"

# List all orders
curl -X GET "$BASE_URL/investors/$INVESTOR_ID/orders" \
  -H "Authorization: Basic $AUTH"
```

### 

[​](https://docs.bluumfinance.com/#status-progression)

Status progression

```
accepted → filled
    │         │
    │    partially_filled
    │
 canceled / rejected
```

Use webhooks (`order.*`) for real-time notifications:

```
curl -X POST "$BASE_URL/webhooks" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-app.com/webhooks/bluum",
    "events": ["order.*"]
  }'
```

## 

[​](https://docs.bluumfinance.com/#order-type-comparison)

Order type comparison

| Type | Use case | Price guarantee | Fill guarantee |
| --- | --- | --- | --- |
| `market` | Quick execution | No | High (during market hours) |
| `limit` | Price control | Yes (or better) | No (may not fill) |
| `stop` | Loss protection | No | No (triggers at stop price) |
| `trailing_stop` | Dynamic stop | No | No (adjusts with market) |

## 

[​](https://docs.bluumfinance.com/#common-errors)

Common errors

| Error | Cause | Resolution |
| --- | --- | --- |
| Insufficient funds | Wallet balance too low | Deposit more funds |
| Market closed | Order placed outside trading hours | Use `gtc` or wait for market open |
| Invalid symbol | Asset not found or not tradable | Verify symbol with `/assets/search` |
| Invalid quantity | Zero, negative, or exceeds position size (for sells) | Check quantity and position |

[Deposits & Withdrawals](https://docs.bluumfinance.com/guides/self-directed/deposits-withdrawals) [Tracking Positions](https://docs.bluumfinance.com/guides/self-directed/positions-portfolio)

⌘I