# Source: https://docs.bluumfinance.com/platform/trading

Trading lets your users buy and sell securities through the Bluum API. You submit orders, Bluum routes them to the appropriate exchange via our custodian network, and you receive status updates as orders execute.

## 

[​](https://docs.bluumfinance.com/#order-types)

Order types

| Type | Description | Required fields |
| --- | --- | --- |
| `market` | Execute immediately at current market price | `qty` or `notional` |
| `limit` | Execute only at the specified price or better | `qty`, `limit_price` |
| `stop` | Trigger a market order when price hits stop level | `qty`, `stop_price` |
| `stop_limit` | Trigger a limit order when price hits stop level | `qty`, `stop_price`, `limit_price` |
| `trailing_stop` | Stop price trails market price by a fixed amount or percent | `qty`, `trail_price` or `trail_percent` |

## 

[​](https://docs.bluumfinance.com/#order-sides)

Order sides

| Side | Description |
| --- | --- |
| `buy` | Purchase shares |
| `sell` | Sell shares you own |

## 

[​](https://docs.bluumfinance.com/#quantity-vs-notional)

Quantity vs Notional

You can specify order size two ways:

- **`qty`** — Number of shares (e.g., `"10"` for 10 shares)
- **`notional`** — Dollar amount (e.g., `"1000.00"` to invest exactly $1,000)

Notional orders result in fractional share purchases when the dollar amount doesn’t divide evenly into whole shares. Only available for `market` orders.

## 

[​](https://docs.bluumfinance.com/#time-in-force)

Time in force

| Value | Meaning |
| --- | --- |
| `day` | Valid for the current trading day. Canceled at market close if unfilled. |
| `gtc` | Good ‘til canceled. Remains active until filled or explicitly canceled. |
| `ioc` | Immediate or cancel. Fill what you can immediately, cancel the rest. |
| `fok` | Fill or kill. Fill the entire order immediately, or cancel entirely. |

## 

[​](https://docs.bluumfinance.com/#order-lifecycle)

Order lifecycle

```
accepted → filled
    │         │
    │    partially_filled
    │
 canceled / rejected
```

| Status | Meaning |
| --- | --- |
| `accepted` | Order received and queued for execution |
| `partially_filled` | Some shares executed, remainder still active |
| `filled` | Order fully executed |
| `canceled` | Order canceled (by user or system) |
| `rejected` | Order rejected (insufficient funds, invalid symbol, market closed) |

## 

[​](https://docs.bluumfinance.com/#placing-an-order)

Placing an order

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

[​](https://docs.bluumfinance.com/#response)

Response

```
{
  "id": "ord_x9y8z7a6b5c4d3e2",
  "symbol": "AAPL",
  "quantity": "10",
  "side": "buy",
  "type": "market",
  "time_in_force": "day",
  "status": "pending",
  "filled_quantity": "0",
  "average_price": "0.00",
  "submitted_at": "2025-06-15T14:30:00Z"
}
```

## 

[​](https://docs.bluumfinance.com/#market-hours)

Market hours

US equity markets are open Monday–Friday, 9:30 AM – 4:00 PM Eastern Time, excluding holidays.

- Market orders placed outside market hours queue until the next open
- Limit and stop orders with `gtc` remain active across sessions
- Use the [Market Data](https://docs.bluumfinance.com/platform/assets) endpoints to check market status and calendar

## 

[​](https://docs.bluumfinance.com/#commission-and-fees)

Commission and fees

You can attach a commission to orders:

| Field | Type | Description |
| --- | --- | --- |
| `commission` | string | Commission amount (e.g., `"1.00"`) |
| `commission_type` | string | Calculation method: `notional` (per order, default), `qty` (per share, pro-rated), `bps` (basis points, up to 2 decimals) |

Commission fields are optional. When set, they appear in the order response as well.

## 

[​](https://docs.bluumfinance.com/#client-order-ids)

Client order IDs

Use `client_order_id` to assign your own identifier to an order for reconciliation:

```
{
  "symbol": "MSFT",
  "side": "buy",
  "type": "limit",
  "quantity": "5",
  "limit_price": "350.00",
  "time_in_force": "gtc",
  "client_order_id": "my-order-001",
  "commission": "1.00",
  "commission_type": "notional"
}
```

## 

[​](https://docs.bluumfinance.com/#key-endpoints)

Key endpoints

| Method | Path | Description |
| --- | --- | --- |
| `POST` | `/investors/{investor_id}/orders` | Place an order |
| `GET` | `/investors/{investor_id}/orders` | List orders for an account |
| `GET` | `/trading/orders/{order_id}` | Get order details |

[Funding & Transfers](https://docs.bluumfinance.com/platform/funding) [Assets & Market Data](https://docs.bluumfinance.com/platform/assets)

⌘I