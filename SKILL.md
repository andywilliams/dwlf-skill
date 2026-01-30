---
name: dwlf
description: >
  Interact with DWLF (dwlf.co.uk), a market analysis platform for crypto and stocks.
  Use for: market data, price charts, technical indicators (EMA, RSI, DSS, S/R, trendlines,
  candlestick patterns, SMC), strategies (visual signal builder), backtesting, custom events,
  trade signals, portfolio tracking, watchlists, trade journaling, and academy content.
  Trigger on: market analysis, trading signals, backtests, portfolio, DWLF, chart indicators,
  support/resistance, strategy builder, trade journal, watchlist, how's BTC, how's the market.
metadata:
  clawdbot:
    emoji: "📊"
    requires:
      bins: ["curl", "jq"]
---

# DWLF — Market Analysis Platform

API base: `https://api.dwlf.co.uk/v2`

## Auth

Use API key auth. Check `TOOLS.md` for the key. Header:
```
Authorization: ApiKey dwlf_sk_...
```

Helper script: `scripts/dwlf-api.sh`

## Quick Start

```bash
# Generic GET request
./scripts/dwlf-api.sh GET /market-data/BTC-USD

# With query params
./scripts/dwlf-api.sh GET "/events?symbol=BTC-USD&limit=10"

# POST request
./scripts/dwlf-api.sh POST /visual-backtests '{"strategyId":"...","symbol":"BTC-USD"}'
```

## Symbol Format

- Crypto: `BTC-USD`, `ETH-USD`, `SOL-USD` (always with `-USD` suffix)
- Stocks/ETFs: `TSLA`, `NVDA`, `META`, `MARA`, `RIOT`
- Forex: `GBP-USD`, `EUR-USD`

If user says "BTC" → use `BTC-USD`. If "TSLA" → use `TSLA`.

## Core Endpoints

### Market Data
| Method | Path | Description |
|--------|------|-------------|
| GET | `/market-data/{symbol}?interval=1d&limit=50` | OHLCV candles |
| GET | `/market-data/symbols` | List all tracked symbols |
| GET | `/support-resistance/{symbol}` | S/R levels with scores |
| GET | `/chart-indicators/{symbol}?interval=1d` | All indicators (RSI, EMA, MACD, etc.) |
| GET | `/trendlines/{symbol}` | Auto-detected trendlines |
| GET | `/events?symbol={symbol}&limit=20` | System events (breakouts) |
| GET | `/events?type=custom_event&scope=user&symbol={symbol}&days=30` | User's custom events (wcl, dss, reversals etc.) |

### Strategies & Signals
| Method | Path | Description |
|--------|------|-------------|
| GET | `/visual-strategies` | List user's strategies |
| GET | `/visual-strategies/{id}` | Strategy details |
| POST | `/visual-strategies` | Create strategy |
| PUT | `/visual-strategies/{id}` | Update strategy |
| GET | `/user/trade-signals/active` | Active trade signals |
| GET | `/user/trade-signals/recent?limit=20` | Recent signals |
| GET | `/user/trade-signals/stats` | Signal performance stats |
| GET | `/user/trade-signals/symbol/{symbol}` | Signals for a symbol |

### Backtesting
| Method | Path | Description |
|--------|------|-------------|
| POST | `/visual-backtests` | Trigger backtest (async) |
| GET | `/visual-backtests/{id}` | Get backtest results |

Backtests are async — POST triggers, then poll GET until complete.

### Portfolio & Trades
| Method | Path | Description |
|--------|------|-------------|
| GET | `/portfolios` | List portfolios |
| GET | `/portfolios/{id}` | Portfolio details + holdings |
| GET | `/trades?status=open` | List trades |
| POST | `/trades` | Log a new trade |
| PUT | `/trades/{id}` | Update trade |
| GET | `/trade-plans` | List trade plans |

### Watchlist
| Method | Path | Description |
|--------|------|-------------|
| GET | `/watchlist` | Get watchlist |
| POST | `/watchlist` | Add symbol (`{"symbol":"BTC-USD"}`) |
| DELETE | `/watchlist/{symbol}` | Remove symbol |

### Custom Events
| Method | Path | Description |
|--------|------|-------------|
| GET | `/custom-events` | List custom events |
| POST | `/custom-events` | Create custom event |
| GET | `/custom-events/{id}` | Event details |

### Evaluations
| Method | Path | Description |
|--------|------|-------------|
| POST | `/evaluations` | Trigger evaluation run |
| GET | `/evaluations/{id}` | Get evaluation results |

## Response Formatting

When presenting data to users:

**Market overview:** Show price, % change, key S/R levels, and any recent events.

**Signals:** Show symbol, direction, entry, stop loss, confidence score, strategy name.

**S/R levels:** Sort by score (strongest first), show level and touch count.

**Backtests:** Show trade count, win rate, total return, Sharpe ratio, best/worst trades.

## Available Indicators

EMA (multiple periods), SMA, RSI, MACD, Bollinger Bands, DSS (Double Smoothed Stochastic),
Stochastic RSI, ATR, ADX, OBV, Volume Profile, Ichimoku Cloud, Fibonacci Retracement,
Support/Resistance, Trendlines, Candlestick Patterns, SMC (Order Blocks, FVGs, BOS/ChoCH).

## Academy

DWLF Academy is a CDN-hosted collection of educational content (15 tracks, 60+ lessons) covering indicators, events, strategies, charting, and more. No auth required.

Use academy tools to read lesson content and understand DWLF concepts:
- `dwlf_list_academy_tracks` — browse all tracks and lessons
- `dwlf_search_academy` — search by keyword
- `dwlf_get_academy_lesson` — read a specific lesson (markdown)

When a user asks "how does X work in DWLF?" or "what is DSS?", check the academy first — it likely has a lesson explaining it.

## Detailed Reference

- **API endpoints** (params, response shapes): read `references/api-endpoints.md`
- **Strategy builder** (node types, edge wiring, examples): read `references/strategy-builder.md`
