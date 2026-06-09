# Source: https://docs.bluumfinance.com/guides/operations/market-data

This guide covers how to use Bluum’s market data APIs to build asset search, price charts, portfolio valuations, and market status indicators.

## 

[​](https://docs.bluumfinance.com/#asset-discovery)

Asset discovery

### 

[​](https://docs.bluumfinance.com/#search-by-name-or-symbol)

Search by name or symbol

```
curl -X GET "$BASE_URL/assets/search?q=tesla" \
  -H "Authorization: Basic $AUTH"
```

Returns matching assets with symbol, name, exchange, and tradability.

### 

[​](https://docs.bluumfinance.com/#list-with-filters)

List with filters

```
# All tradable US equities
curl -X GET "$BASE_URL/assets?asset_class=us_equity&tradable=true" \
  -H "Authorization: Basic $AUTH"
```

### 

[​](https://docs.bluumfinance.com/#get-asset-details)

Get asset details

```
curl -X GET "$BASE_URL/assets/TSLA" \
  -H "Authorization: Basic $AUTH"
```

## 

[​](https://docs.bluumfinance.com/#real-time-quotes)

Real-time quotes

Get the latest bid/ask/last price:

```
curl -X GET "$BASE_URL/market-data/assets/AAPL/quote" \
  -H "Authorization: Basic $AUTH"
```

Use quotes to:

- Display current prices in your UI
- Calculate portfolio market value
- Show pre-trade price estimates

## 

[​](https://docs.bluumfinance.com/#historical-bars-ohlcv)

Historical bars (OHLCV)

Get open, high, low, close, and volume data:

```
curl -X GET "$BASE_URL/market-data/assets/AAPL/bars?timeframe=1Day&start=2025-01-01&end=2025-06-15" \
  -H "Authorization: Basic $AUTH"
```

### 

[​](https://docs.bluumfinance.com/#timeframes)

Timeframes

| Value | Description |
| --- | --- |
| `1Min` | 1-minute bars (intraday) |
| `5Min` | 5-minute bars (intraday) |
| `15Min` | 15-minute bars (intraday) |
| `1Hour` | Hourly bars |
| `1Day` | Daily bars |
| `1Week` | Weekly bars |
| `1Month` | Monthly bars |

## 

[​](https://docs.bluumfinance.com/#chart-data)

Chart data

Get chart-ready data with a simplified API:

```
# 1 month of daily data
curl -X GET "$BASE_URL/assets/chart?symbol=AAPL&timeframe=1Day&period=1M" \
  -H "Authorization: Basic $AUTH"

# 1 year of weekly data
curl -X GET "$BASE_URL/assets/chart?symbol=AAPL&timeframe=1Week&period=1Y" \
  -H "Authorization: Basic $AUTH"
```

## 

[​](https://docs.bluumfinance.com/#market-snapshots)

Market snapshots

Get a complete picture for an asset — quote, latest bar, and previous close:

```
curl -X GET "$BASE_URL/market-data/assets/AAPL/snapshot" \
  -H "Authorization: Basic $AUTH"
```

Snapshots are useful for building asset detail pages that show current price, daily change, and trading volume in a single request.

## 

[​](https://docs.bluumfinance.com/#market-status)

Market status

### 

[​](https://docs.bluumfinance.com/#check-if-a-market-is-open)

Check if a market is open

```
curl -X GET "$BASE_URL/market-data/markets/XNYS/status" \
  -H "Authorization: Basic $AUTH"
```

Use this to:

- Show “Market Open” / “Market Closed” indicators
- Disable order placement outside market hours (for `day` orders)
- Display countdown to market open/close

### 

[​](https://docs.bluumfinance.com/#trading-calendar)

Trading calendar

```
curl -X GET "$BASE_URL/market-data/markets/XNYS/calendar?start=2025-06-01&end=2025-06-30" \
  -H "Authorization: Basic $AUTH"
```

Returns open/close times and holidays for the specified period.

### 

[​](https://docs.bluumfinance.com/#market-codes)

Market codes

| Code | Market |
| --- | --- |
| `XNYS` | New York Stock Exchange (NYSE) |
| `XNAS` | NASDAQ |

## 

[​](https://docs.bluumfinance.com/#building-a-stock-detail-page)

Building a stock detail page

A typical asset detail page combines multiple market data calls:

```
const [quote, bars, snapshot] = await Promise.all([
  fetch(`${baseUrl}/market-data/assets/${symbol}/quote`, { headers }),
  fetch(`${baseUrl}/market-data/assets/${symbol}/bars?timeframe=1Day&start=${oneYearAgo}&end=${today}`, { headers }),
  fetch(`${baseUrl}/market-data/assets/${symbol}/snapshot`, { headers })
]);
```

Display: current price, daily change, price chart, volume, and 52-week range.

[Setting Up Webhooks](https://docs.bluumfinance.com/guides/operations/webhook-setup) [Testing in Sandbox](https://docs.bluumfinance.com/guides/operations/testing-and-sandbox)

⌘I