# Alpaca API Reference Documentation Skill

## Purpose
Systematically explore, document, and implement Alpaca Trading/Market Data/Broker APIs for trading applications.

## API Categories

### 1. **Trading API** (Individual traders)
**Endpoints:**
- `Accounts` - Get account details, status, configurations
- `Orders` - Create, retrieve, modify, cancel orders (Market, Limit, Stop)
- `Positions` - View, close, manage open positions
- `Watchlists` - Create and manage custom watchlists
- `Portfolio History` - Get P&L and portfolio performance data
- `Account Activities` - Retrieve trading history and transactions

**Key Methods:**
```python
trading_client.get_account()
trading_client.submit_order(order_request)
trading_client.get_all_positions()
trading_client.get_orders()
```

### 2. **Market Data API** (Historical + Real-time)
**Stock Data:**
- Historical bars (OHLCV)
- Latest bars, quotes, trades
- Snapshots
- Condition & exchange codes

**Crypto Data (24/7):**
- Historical bars for BTC/USD, ETH/USD, etc.
- Latest quotes, trades, orderbooks
- Snapshots

**Options Data:**
- Historical bars, quotes, trades
- Option chains
- Latest data

**Other Data:**
- News articles
- Corporate actions
- Fixed income (bonds)
- Forex rates

**Key Methods:**
```python
data_client.get_stock_bars(bars_request)
crypto_stream = CryptoDataStream(api_key, secret_key)
stock_stream = StockDataStream(api_key, secret_key)
```

### 3. **Broker API** (Multi-user platforms)
**For building trading platforms/apps:**
- Account management (create, update, close accounts)
- Manage multiple sub-accounts
- Instant funding
- ACH relationships
- Deposits/Withdrawals
- Compliance management

### 4. **Streaming Endpoints** (Real-time data)
- **WebSocket (Stock/Crypto):** Quotes, trades, bars
- **SSE (Server-Sent Events):** Account events, trade events, funding status

## Systematic Exploration Process

### Step 1: Identify API Category
Determine if you need:
- [ ] Trading API (execute trades)
- [ ] Market Data API (get prices/news)
- [ ] Broker API (manage accounts for others)
- [ ] Streaming (real-time updates)

### Step 2: Find Relevant Endpoints
- Go to https://docs.alpaca.markets/reference/
- Search for your use case (e.g., "orders", "crypto", "positions")
- Note HTTP method (GET, POST, PUT, DELETE)

### Step 3: Understand Response Structure
- Read request parameters
- Review response fields and types
- Check error codes

### Step 4: Implement in Python
```python
from alpaca.trading.client import TradingClient
from alpaca.data.historical.stock import StockHistoricalDataClient
from alpaca.data.live import StockDataStream, CryptoDataStream

# Initialize clients
trading_client = TradingClient(api_key, secret_key, paper=True)
data_client = StockHistoricalDataClient(api_key, secret_key)
```

### Step 5: Handle Common Issues
- **UUID not subscriptable:** Convert to string: `str(uuid_obj)[:8]`
- **String instead of number:** Cast: `float(string_value)`
- **Module not found:** Verify package installed: `uv add alpaca`
- **Connection failed:** Check credentials and endpoint

## Complete API List (from reference)

### Trading Endpoints
| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/v2/accounts` | GET | Get account info |
| `/v2/orders` | POST, GET, DELETE | Manage orders |
| `/v2/positions` | GET, DELETE | Manage positions |
| `/v2/watchlists` | GET, POST, PUT, DELETE | Manage watchlists |
| `/v2/portfolio_history` | GET | Portfolio performance |

### Market Data Endpoints
| Category | Endpoints |
|----------|-----------|
| **Stock** | bars, quotes, trades, snapshots, auctions |
| **Crypto** | bars, quotes, trades, orderbooks, snapshots |
| **Options** | bars, quotes, trades, chains |
| **Other** | news, corporate_actions, logos, screener |

### Streaming Endpoints
| Type | Source | Data |
|------|--------|------|
| **WebSocket** | StockDataStream | Quotes, trades, bars |
| **WebSocket** | CryptoDataStream | Crypto quotes, trades, bars (24/7) |
| **SSE** | Events | Account, trade, funding updates |

## Common Patterns

### Pattern: Fetch Historical Data
```python
from alpaca.data.requests import StockBarsRequest
from alpaca.data.timeframe import TimeFrame

request = StockBarsRequest(
    symbol_or_symbols="AAPL",
    timeframe=TimeFrame.Day,
    start="2024-01-01"
)
bars = data_client.get_stock_bars(request)
df = bars.df
```

### Pattern: Stream Real-Time Data
```python
from alpaca.data.live import CryptoDataStream

stream = CryptoDataStream(api_key, secret_key)

async def handler(data):
    print(f"BTC: ${data.price}")

stream.subscribe_trades(handler, "BTC/USD")
stream.run()  # Blocking
```

### Pattern: Place and Manage Orders
```python
from alpaca.trading.requests import MarketOrderRequest
from alpaca.trading.enums import OrderSide, TimeInForce

# Place order
order = trading_client.submit_order(
    MarketOrderRequest(
        symbol="AAPL",
        qty=1,
        side=OrderSide.BUY,
        time_in_force=TimeInForce.DAY
    )
)

# Get orders
orders = trading_client.get_orders()

# Cancel order
trading_client.cancel_order_by_id(order.id)
```

## Quality Checklist

Before using an API endpoint, verify:
- [ ] Correct HTTP method documented
- [ ] All required parameters provided
- [ ] Response types understood (UUID vs str, float vs str)
- [ ] Error handling in place
- [ ] Rate limits considered
- [ ] Paper trading vs live trading clarity

## Resources
- **API Docs:** https://docs.alpaca.markets/docs
- **API Reference:** https://docs.alpaca.markets/reference/
- **GitHub:** https://github.com/alpacahq/
- **Forum:** https://forum.alpaca.markets/

## Next Steps
1. Choose an API category (Trading/Market Data/Broker)
2. Review endpoints at https://docs.alpaca.markets/reference/
3. Implement using patterns above
4. Test on paper trading first
5. Monitor notebook for type mismatches
