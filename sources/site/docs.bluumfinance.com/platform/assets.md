# Source: https://docs.bluumfinance.com/platform/assets

**Assets** are the tradable instruments available through Bluum — stocks, ETFs, and other securities. **Market Data** provides real-time and historical pricing, market status, and trading calendars.

## 

[​](https://docs.bluumfinance.com/#asset-classes)

Asset classes

| Class | Description | Markets |
| --- | --- | --- |
| `us_equity` | US-listed stocks and ETFs | XNAS (NASDAQ), XNYS (NYSE) |

## 

[​](https://docs.bluumfinance.com/#searching-assets)

Searching assets

Find assets by symbol, name, or filters:

```
# Search by symbol or name
curl -X GET "$BASE_URL/assets/search?q=apple" \
  -H "Authorization: Basic $AUTH"

# List tradable US equities
curl -X GET "$BASE_URL/assets?asset_class=us_equity&tradable=true" \
  -H "Authorization: Basic $AUTH"

# Get a specific asset
curl -X GET "$BASE_URL/assets/AAPL" \
  -H "Authorization: Basic $AUTH"
```

### 

[​](https://docs.bluumfinance.com/#asset-object)

Asset object

```
{
  "id": "6c5b2403-24a9-4b55-a3dd-5cb1e4b50da6",
  "symbol": "AAPL",
  "name": "Apple Inc.",
  "class": "us_equity",
  "exchange": "NASDAQ",
  "status": "active",
  "tradable": true,
  "fractionable": true
}
```

Key fields:

- `tradable` — Whether the asset can be traded through Bluum
- `fractionable` — Whether fractional shares are supported (enables notional orders)

## 

[​](https://docs.bluumfinance.com/#market-data)

Market data

### 

[​](https://docs.bluumfinance.com/#quotes)

Quotes

Get the latest quote for an asset:

```
curl -X GET "$BASE_URL/market-data/assets/AAPL/quote" \
  -H "Authorization: Basic $AUTH"
```

### 

[​](https://docs.bluumfinance.com/#historical-bars-ohlcv)

Historical bars (OHLCV)

Get historical price data:

```
curl -X GET "$BASE_URL/market-data/assets/AAPL/bars?timeframe=1Day&start=2025-01-01&end=2025-06-15" \
  -H "Authorization: Basic $AUTH"
```

### 

[​](https://docs.bluumfinance.com/#snapshots)

Snapshots

Get a complete market snapshot for an asset (quote + latest bar + previous close):

```
curl -X GET "$BASE_URL/market-data/assets/AAPL/snapshot" \
  -H "Authorization: Basic $AUTH"
```

### 

[​](https://docs.bluumfinance.com/#charts)

Charts

Get chart-ready data for an asset:

```
curl -X GET "$BASE_URL/assets/chart?symbol=AAPL&timeframe=1Day&period=1M" \
  -H "Authorization: Basic $AUTH"
```

## 

[​](https://docs.bluumfinance.com/#market-status-and-calendar)

Market status and calendar

Check if a market is currently open and get the trading calendar:

```
# List all markets
curl -X GET "$BASE_URL/market-data/markets" \
  -H "Authorization: Basic $AUTH"

# Check if NYSE is open
curl -X GET "$BASE_URL/market-data/markets/XNYS/status" \
  -H "Authorization: Basic $AUTH"

# Get trading calendar (open/close times, holidays)
curl -X GET "$BASE_URL/market-data/markets/XNYS/calendar?start=2025-06-01&end=2025-06-30" \
  -H "Authorization: Basic $AUTH"
```

## 

[​](https://docs.bluumfinance.com/#key-endpoints)

Key endpoints

| Method | Path | Description |
| --- | --- | --- |
| `GET` | `/assets/search` | Search assets by name or symbol |
| `GET` | `/assets` | List assets with filters |
| `GET` | `/assets/{symbol}` | Get asset details |
| `GET` | `/assets/chart` | Get chart data |
| `GET` | `/market-data/assets/{symbol}/quote` | Latest quote |
| `GET` | `/market-data/assets/{symbol}/bars` | Historical OHLCV bars |
| `GET` | `/market-data/assets/{symbol}/snapshot` | Market snapshot |
| `GET` | `/market-data/markets` | List markets |
| `GET` | `/market-data/markets/{code}/status` | Market open/closed status |
| `GET` | `/market-data/markets/{code}/calendar` | Trading calendar |

[Trading](https://docs.bluumfinance.com/platform/trading) [Positions](https://docs.bluumfinance.com/platform/positions)

⌘I