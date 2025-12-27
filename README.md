# GEX Analytics Backend 🚀

DEMO LINK: http://gex.kctradings.com/

> Professional-grade Options Flow & Gamma Exposure Analysis Engine for Intraday Trading

[![Python](https://img.shields.io/badge/Python-3.11+-blue.svg)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-Latest-green.svg)](https://fastapi.tiangolo.com/)
[![License](https://img.shields.io/badge/License-Private-red.svg)]()

## 📋 Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Technical Architecture](#technical-architecture)
- [System Design](#system-design)
- [API Design Philosophy](#api-design-philosophy)
- [Performance Optimizations](#performance-optimizations)
- [Data Flow](#data-flow)
- [Market Support](#market-support)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Key Implementations](#key-implementations)
- [Security & Best Practices](#security--best-practices)

---

## 🎯 Overview

A high-performance **real-time options analytics engine** that calculates **Gamma Exposure (GEX)**, **Delta Exposure (DEX)**, and **options flow** for intraday trading decisions. Built for professional traders who need **sub-second analysis** across multiple markets.

### What is GEX?

**Gamma Exposure** measures the hedging activity that market makers must perform, creating **support and resistance zones**:
- **Positive GEX (Calls)** → Market makers sell into rallies → **Resistance**
- **Negative GEX (Puts)** → Market makers buy into dips → **Support**
- **Zero-Gamma Flip** → Critical level where market behavior changes

This system processes real-time option chains, calculates Greeks using Black-Scholes, and aggregates exposure to identify key price levels for intraday trading.

---

## ✨ Key Features

### 🔥 Core Analytics
- **Real-time GEX Calculation** - Intraday gamma exposure across all strikes
- **Multi-Expiry Support** - 0DTE, Weekly, Monthly options analysis
- **Greeks Engine** - Custom Black-Scholes implementation for IV, Delta, Gamma, Theta, Vega
- **Strike-Level Analysis** - GEX, DEX, OI, Volume, IV for each strike
- **Regime Detection** - Positive Gamma, Negative Gamma, and transition states
- **Key Level Identification** - Max Gamma Strike, Zero-Gamma Flip, Call/Put Walls

### 🌐 Market Coverage
- **US Markets** - SPY, QQQ, AAPL, NVDA, TSLA, etc. (via Alpaca)
- **Indian Markets** - NIFTY, BANKNIFTY, FINNIFTY, stocks (via Dhan)
- **Multi-Broker Support** - Unified connector architecture for broker APIs
- **Cross-Market Analytics** - Normalized data models work across all markets

### ⚡ Performance Features
- **Connection Pooling** - Shared HTTP clients reduce latency by 60%
- **Intelligent Caching** - Redis/Memory cache for frequently accessed data
- **Async Architecture** - Non-blocking I/O for concurrent requests
- **Optimized Calculations** - Vectorized operations for Greeks computation
- **AI-Optimized Endpoints** - Condensed responses for LLM consumption

### 📊 Advanced Analytics
- **OHLC Integration** - Recent price action analysis (5min bars, 1hr lookback)
- **Volume Analysis** - Unusual volume detection and trends
- **IV Skew Analysis** - Put/Call IV comparison and market sentiment
- **Flow Signals** - Net OI changes, PCR, and positioning sentiment
- **Timestamp Tracking** - Per-strike quote timestamps to detect data delays

---

## 🏗️ Technical Architecture

### System Design Principles

```
┌─────────────────────────────────────────────────────────────────┐
│                        FastAPI Application                      │
│                     (Async/Await Architecture)                  │
└─────────────────────────────────────────────────────────────────┘
                                │
                ┌───────────────┼───────────────┐
                │               │               │
        ┌───────▼──────┐ ┌─────▼─────┐ ┌──────▼──────┐
        │   REST API   │ │  WebSocket │ │  Scheduler  │
        │   Endpoints  │ │   (Future) │ │   Engine    │
        └───────┬──────┘ └───────────┘ └──────┬──────┘
                │                              │
        ┌───────▼──────────────────────────────▼──────┐
        │         Connector Layer (Unified)           │
        │   ┌──────────┐           ┌──────────┐      │
        │   │  Alpaca  │           │   Dhan   │      │
        │   │ Connector│           │ Connector│      │
        │   └──────────┘           └──────────┘      │
        └──────────────────────────────────────────────┘
                                │
        ┌───────────────────────▼───────────────────────┐
        │           GEX Calculation Engine              │
        │  ┌──────────────┐    ┌──────────────┐        │
        │  │   Greeks     │    │  Exposure    │        │
        │  │  Calculator  │───▶│  Aggregator  │        │
        │  └──────────────┘    └──────────────┘        │
        └───────────────────────────────────────────────┘
                                │
        ┌───────────────────────▼───────────────────────┐
        │              Response Layer                   │
        │   • Standard API (Full detail)                │
        │   • AI-Optimized (Condensed for LLMs)         │
        │   • Quick Levels (Ultra-fast)                 │
        └───────────────────────────────────────────────┘
```

### Architectural Highlights

1. **Adapter Pattern** - Unified data models abstract broker differences
2. **Strategy Pattern** - Market-specific calculation strategies (US vs India)
3. **Connection Pooling** - Shared HTTP clients across request lifecycle
4. **Dependency Injection** - FastAPI's DI for connector management
5. **Repository Pattern** - Database abstraction layer (future caching)

---

## 🔄 Data Flow

### Request Lifecycle

```
User Request → API Endpoint → Connector Selection → Broker API Call
      ↓
Option Chain Fetch → Data Normalization → Unified OptionChain Model
      ↓
Greeks Calculation (Black-Scholes) → EnrichedOptionQuote[]
      ↓
GEX Aggregation by Strike → StrikeData{}
      ↓
Regime Detection + Level Identification → GEXSummary
      ↓
Response Formatting (Standard/AI/Quick) → JSON Response
```

### Data Models Hierarchy

```
OptionChain                    (Raw broker data normalized)
    │
    ├── UnderlyingQuote       (Spot price, bid/ask)
    │       └── timestamp     (Broker quote time)
    │
    └── OptionQuote[]         (All options in chain)
            │
            ├── OptionContract (Strike, Expiry, Type)
            ├── Price Data     (Bid, Ask, Last, Volume, OI)
            └── timestamp      (Per-strike quote time)
                    ↓
        Greeks Calculation (Black-Scholes Model)
                    ↓
EnrichedOptionQuote[]         (Quotes + Greeks + Exposures)
    │
    ├── GreeksData            (IV, Delta, Gamma, Theta, Vega)
    ├── GEX                   (Gamma Exposure)
    └── DEX                   (Delta Exposure)
                    ↓
        Strike Aggregation
                    ↓
StrikeData{}                  (Aggregated by strike price)
    │
    ├── Call/Put GEX
    ├── Call/Put OI
    ├── Call/Put IV
    └── Volume
                    ↓
        Analysis & Classification
                    ↓
GEXSummary                    (Final analytics output)
    │
    ├── Key Levels            (Max Gamma, Flip, Walls)
    ├── Regime                (Pos/Neg Gamma)
    ├── Top Support/Resistance
    └── Flow Signals
```

---

## 🎨 API Design Philosophy

### Endpoint Categories

#### 1. **Standard Endpoints** (`/api/gex/...`)
- **Purpose**: Full-detail analysis for dashboards
- **Response Size**: 50-200KB (all strikes, all data)
- **Use Case**: Web applications, detailed analysis

#### 2. **AI-Optimized Endpoints** (`/api/ai/...`)
- **Purpose**: LLM-ready condensed analysis
- **Response Size**: 5-15KB (±N strikes around spot)
- **Use Case**: AI agents, chatbots, automated trading signals
- **Features**:
  - Configurable strike range (±10 default)
  - Optional OHLC data inclusion
  - Optional Vanna exposure (future)
  - Per-strike timestamps for delay detection

#### 3. **Quick Endpoints** (`/api/ai/quick-levels/...`)
- **Purpose**: Ultra-fast key levels only
- **Response Size**: <1KB
- **Use Case**: High-frequency polling, mobile apps

### Timestamp Architecture

The API provides **3 levels of timestamps** to detect data freshness:

```json
{
  "timestamp": "2025-12-24T14:30:00Z",           // Processing timestamp
  "broker_timestamp": "2025-12-24T14:29:55Z",    // When chain fetched
  "strikes": [
    {
      "strike": 580.0,
      "quote_timestamp": "2025-12-24T14:29:50Z" // Per-strike quote time
    }
  ]
}
```

**Why 3 timestamps?**
- `timestamp` - Know when calculations were performed
- `broker_timestamp` - Know when broker data was fetched
- `quote_timestamp` - Detect stale/delayed strikes (critical for illiquid options)

---

## ⚡ Performance Optimizations

### 1. Connection Pooling
**Problem**: Each request created new HTTP clients → 500ms overhead
**Solution**: Shared `httpx.AsyncClient` in `app.state`
**Result**: ~60% latency reduction

```python
# Shared across all requests in app lifecycle
app.state.alpaca_client = httpx.AsyncClient()
app.state.alpaca_trading_client = httpx.AsyncClient()
```

### 2. Async Architecture
- **FastAPI + Async/Await** throughout the stack
- **Non-blocking I/O** for broker API calls
- **Concurrent requests** supported out of the box

### 3. Intelligent Filtering
- **Strike range filtering** (±20% of spot) reduces calculation load
- **DTE filtering** (max 7 days for US, 30 for India) focuses on relevant expirations
- **Validity checks** remove illiquid options before calculations

### 4. Calculation Optimizations
- **Cached Greeks** (when quote unchanged)
- **Vectorized operations** for bulk calculations
- **Early exit conditions** in regime detection

### 5. Response Compression
- **Gzip compression** for API responses
- **Field selection** in AI endpoints (only essential data)
- **Rounding** to 2-3 decimals reduces payload size

---

## 🌍 Market Support

### US Markets (Alpaca API)

| Feature | Implementation |
|---------|---------------|
| **Symbols** | SPY, QQQ, AAPL, NVDA, TSLA, AMD, MSFT, etc. |
| **Expirations** | 0DTE, Weekly, Monthly (max 7 DTE default) |
| **Data** | Real-time quotes, Greeks, OHLC (5min bars) |
| **Contract Multiplier** | 100 (standard US options) |
| **Risk-Free Rate** | 5.0% (configurable) |

### Indian Markets (Dhan API)

| Feature | Implementation |
|---------|---------------|
| **Indices** | NIFTY, BANKNIFTY, FINNIFTY, MIDCPNIFTY |
| **Stocks** | RELIANCE, TCS, INFY, HDFCBANK, etc. |
| **Expirations** | Weekly, Monthly (max 30 DTE default) |
| **Data** | Real-time quotes, Greeks, OHLC (5min bars) |
| **Contract Multiplier** | Index-specific (NIFTY: 50, BANKNIFTY: 15, etc.) |
| **Risk-Free Rate** | 6.5% (India-specific) |

### Unified Connector Architecture

**All brokers implement the same interface**:
```python
async def get_quote(symbol: str) -> UnderlyingQuote
async def get_option_chain(symbol: str) -> OptionChain
async def get_bars(symbol: str, timeframe: str, limit: int) -> List[Bar]
```

**Benefits**:
- Add new brokers without changing calculation logic
- Swap brokers transparently
- Test with mock connectors
- Unified error handling

---

## 🛠️ Technology Stack

### Backend Framework
- **FastAPI** - Modern async Python web framework
- **Uvicorn** - ASGI server (production-ready)
- **Pydantic** - Data validation and serialization

### Data Processing
- **NumPy** - Vectorized calculations for Greeks
- **SciPy** - Statistical functions (normal distribution for Black-Scholes)
- **Pandas** - Time series analysis (OHLC data)

### External APIs
- **Alpaca Markets API** - US market data
- **Dhan API** - Indian market data
- **httpx** - Async HTTP client with connection pooling

### Database & Caching
- **SQLAlchemy** - ORM for metadata storage
- **Redis** (future) - High-speed caching layer
- **PostgreSQL** (future) - Historical data storage

### Development Tools
- **Loguru** - Advanced logging with rotation
- **pytest** - Testing framework
- **Black** - Code formatting
- **mypy** - Static type checking

---

## 📁 Project Structure

```
gex_analytics_backend/
│
├── api/                          # API layer
│   ├── main.py                   # FastAPI app initialization
│   ├── routes.py                 # Standard GEX endpoints
│   ├── ai_routes.py              # AI-optimized endpoints
│   ├── analytics_routes.py       # Advanced analytics endpoints
│   └── complete_routes.py        # Full-featured endpoints
│
├── connectors/                   # Broker abstraction layer
│   ├── base.py                   # Base connector interface
│   ├── models.py                 # Unified data models
│   ├── alpaca.py                 # Alpaca implementation
│   ├── dhan.py                   # Dhan implementation
│   └── __init__.py               # Connector factory
│
├── engine/                       # Core calculation engine
│   ├── greeks.py                 # Black-Scholes Greeks calculator
│   ├── gex.py                    # Standard GEX calculator
│   ├── gex_intraday.py          # Intraday-optimized GEX
│   └── flow.py                   # Options flow analysis
│
├── database/                     # Data persistence
│   ├── models.py                 # SQLAlchemy models
│   ├── repositories.py           # Data access layer
│   └── __init__.py
│
├── scheduler/                    # Background tasks
│   ├── __init__.py              # Scheduler setup
│   └── tasks.py                 # Periodic jobs (future)
│
├── auth/                        # Authentication (future)
│   ├── models.py                # User models
│   └── jwt.py                   # Token handling
│
├── utils/                       # Shared utilities
│   ├── logging.py               # Logging configuration
│   ├── validators.py            # Input validation
│   └── helpers.py               # Common functions
│
├── config/                      # Configuration
│   └── settings.py              # Environment-based settings
│
├── tests/                       # Test suite
│   ├── test_greeks.py
│   ├── test_gex.py
│   └── test_connectors.py
│
├── scripts/                     # Utility scripts
│   └── setup_db.py             # Database initialization
│
├── logs/                        # Application logs
│   └── gex.log
│
├── main.py                      # Entry point
├── requirements.txt             # Python dependencies
├── .env.example                 # Environment template
└── README.md                    # This file
```

---

## 🔑 Key Implementations

### 1. Black-Scholes Greeks Calculator

Custom implementation of the **Black-Scholes option pricing model**:

**Features**:
- **Implied Volatility** - Newton-Raphson solver with Brent's method fallback
- **Delta** - First derivative w.r.t. spot price
- **Gamma** - Second derivative (acceleration of Delta)
- **Theta** - Time decay per day
- **Vega** - Sensitivity to volatility changes

**Optimizations**:
- Cached normal distribution lookups
- Early exit for OTM options with wide spreads
- Vectorized calculations for bulk processing

### 2. Intraday GEX Calculator

Optimized for **0DTE and weekly options** trading:

**Filters**:
- **DTE Filter** - Focus on near-term expiries (7 days default)
- **Strike Range** - ±20% of spot (configurable)
- **Liquidity Filter** - Remove options with spreads >50% of mid
- **Volume/OI Filter** - Require minimum activity

**Calculations**:
- **Per-Strike GEX** - Aggregated across calls/puts
- **Total GEX** - Sum across all strikes
- **Max Gamma Strike** - Highest absolute gamma concentration
- **Zero-Gamma Flip** - Where net GEX crosses zero
- **Call/Put Walls** - Top 5 resistance/support levels

**Regime Detection**:
```
Positive Gamma   → Spot > Max Gamma → Market dampening
Negative Gamma   → Spot < Max Gamma → Market amplification
Neutral          → Low total GEX     → Unpredictable
```

### 3. Multi-Market Connector System

**Challenge**: Different brokers have different data formats

**Solution**: Unified `OptionChain` model + broker-specific adapters

**Example - Dhan to Unified**:
```python
# Dhan returns: {"CE": [...], "PE": [...]}
# Convert to: OptionQuote[] with normalized fields

for option in dhan_data["CE"]:
    quote = OptionQuote(
        contract=OptionContract(
            symbol=option["symbol"],
            strike=option["strike"],
            expiry=parse_date(option["expiry"]),
            option_type=OptionType.CALL
        ),
        bid=option["bid"],
        ask=option["ask"],
        open_interest=option["oi"],
        ...
    )
```

**Result**: Calculation engine doesn't know/care about broker differences

### 4. AI-Optimized Response Design

**Problem**: Standard GEX response is 100KB+ (all strikes, all data)
**Solution**: Condensed response focused on trading zone

**Optimizations**:
- Return only ±N strikes around spot (configurable)
- Pre-calculated sentiment signals (bearish/neutral/bullish)
- Rounded numbers to 2-3 decimals
- Optional OHLC data (only if requested)
- Per-strike timestamps for delay detection

**Result**: 5-15KB responses perfect for LLM context windows

### 5. Timestamp Tracking System

**Innovation**: 3-level timestamp hierarchy

1. **Processing Timestamp** - When GEX was calculated
2. **Broker Timestamp** - When option chain was fetched
3. **Quote Timestamp** - When each strike's quote was captured

**Use Case**: Detect data delays
```python
# Example: Detecting stale data
for strike in response["strikes"]:
    age = now - parse(strike["quote_timestamp"])
    if age > 60:  # seconds
        warn("Stale data detected for strike", strike)
```

---

## 🔒 Security & Best Practices

### API Security
- **IP Whitelisting** - Restrict Swagger UI access to allowed IPs
- **Rate Limiting** - Prevent abuse (configurable per endpoint)
- **Input Validation** - Pydantic models validate all inputs
- **Error Handling** - Never expose internal errors to clients

### Code Quality
- **Type Hints** - 100% type coverage for static analysis
- **Docstrings** - Comprehensive documentation for all public APIs
- **Logging** - Structured logging with rotation (Loguru)
- **Error Boundaries** - Graceful degradation on broker failures

### Configuration Management
- **Environment Variables** - Secrets never in code
- **Settings Validation** - Pydantic settings with defaults
- **Multi-Environment Support** - Dev, Staging, Production configs

### Performance Monitoring
- **Request Timing** - Log processing time for each endpoint
- **Error Tracking** - Detailed error logs with context
- **Health Checks** - Endpoints for monitoring systems

---

## 📊 Example Use Cases

### 1. Intraday SPY Trading
```
GET /api/ai/gex-summary/SPY?market=US&strike_count=10&include_ohlc=true

Response shows:
• Spot: $580.50
• Max Gamma Strike: $580 (resistance)
• Zero-Gamma Flip: $575 (critical level)
• Regime: Positive Gamma (expect consolidation)
• Recent 1hr trend: +0.8% bullish momentum
• Top resistance: $585 (call wall), $590 (secondary)
• Top support: $575 (put wall), $570 (secondary)

Trading Decision:
• Stay long above $575 (flip level)
• Take profits at $585 (call wall)
• Cut losses below $575 (regime change to negative gamma)
```

### 2. NIFTY Weekly Expiry
```
GET /api/ai/gex-summary/NIFTY?market=IN&strike_count=15

Response shows:
• Spot: 23,450
• Max Gamma Strike: 23,500 (heavy gamma)
• Regime: Negative Gamma (amplified moves expected)
• PCR: 1.65 (bearish positioning)
• IV Skew: Puts expensive (+3.2% vs calls)

Trading Decision:
• Expect volatility (negative gamma regime)
• Watch for breakdown below 23,400 (put wall)
• High PCR + expensive puts = potential short squeeze setup
```

### 3. Quick Level Monitoring
```
GET /api/ai/quick-levels/QQQ?market=US

Ultra-fast response (<100ms):
• Spot: $525.50
• Max Gamma: $525
• Flip: $520
• Regime: Positive Gamma
• PCR: 1.15

Use Case: High-frequency monitoring script checks every 30 seconds
```

---

## 🚀 Performance Metrics

| Metric | Value |
|--------|-------|
| **Response Time (Standard)** | 300-500ms |
| **Response Time (AI-Optimized)** | 150-300ms |
| **Response Time (Quick Levels)** | 50-100ms |
| **Concurrent Requests** | 100+ (async architecture) |
| **Uptime Target** | 99.9% |
| **Data Freshness** | <1 second (broker dependent) |

---

## 🎓 Learning Outcomes

Building this project demonstrates expertise in:

### Software Engineering
- ✅ **RESTful API Design** - Clean, versioned, well-documented APIs
- ✅ **Async Programming** - High-performance async/await patterns
- ✅ **Design Patterns** - Adapter, Strategy, Repository, Factory
- ✅ **SOLID Principles** - Modular, testable, maintainable code
- ✅ **Error Handling** - Graceful degradation and recovery

### Financial Engineering
- ✅ **Options Pricing** - Black-Scholes model implementation
- ✅ **Greeks Calculation** - Delta, Gamma, Theta, Vega, IV solving
- ✅ **Market Microstructure** - Understanding dealer hedging behavior
- ✅ **Risk Metrics** - Exposure calculations and regime analysis

### System Design
- ✅ **Scalability** - Connection pooling, caching, async I/O
- ✅ **Multi-Market Support** - Unified abstractions across brokers
- ✅ **Performance Optimization** - Sub-second response times
- ✅ **API Versioning** - Backward-compatible evolution

### DevOps & Operations
- ✅ **Logging & Monitoring** - Production-ready observability
- ✅ **Configuration Management** - Environment-based configs
- ✅ **Deployment** - Uvicorn with multiple workers
- ✅ **Security** - IP whitelisting, input validation, rate limiting

---

## 📈 Future Enhancements

### Planned Features
- [ ] **WebSocket Streaming** - Real-time GEX updates every second
- [ ] **Historical Analysis** - Store and analyze GEX evolution
- [ ] **Vanna Exposure** - Second-order Greek (Gamma sensitivity to IV)
- [ ] **Charm/Vomma** - Third-order Greeks for advanced analysis
- [ ] **Machine Learning** - Predict regime changes using historical patterns
- [ ] **Multi-Symbol Correlation** - SPY vs QQQ vs IWM GEX comparison
- [ ] **Alert System** - Notifications on regime changes or level breaks
- [ ] **Backtesting Engine** - Test GEX strategies on historical data

### Technical Improvements
- [ ] **Redis Caching** - Sub-10ms response times for cached data
- [ ] **PostgreSQL Integration** - Store tick-by-tick GEX history
- [ ] **GraphQL API** - Flexible querying for complex use cases
- [ ] **Prometheus Metrics** - Detailed performance monitoring
- [ ] **Docker Containerization** - Easy deployment anywhere
- [ ] **Kubernetes Orchestration** - Auto-scaling based on load
- [ ] **CI/CD Pipeline** - Automated testing and deployment

---

## 🤝 Why This Project Matters

### For Traders
- **Data-Driven Decisions** - Replace gut feeling with quantitative analysis
- **Intraday Edge** - Real-time GEX provides actionable levels
- **Multi-Market Coverage** - Trade US and Indian markets with same tools

### For Developers
- **Clean Architecture** - Example of professional API design
- **Real-World Complexity** - Handles broker differences, data quality issues
- **Performance Focus** - Production-grade optimization techniques

### For Interviewers
- **Full-Stack Capability** - Backend, APIs, data processing, deployment
- **Domain Expertise** - Deep understanding of options and market mechanics
- **Problem Solving** - Creative solutions to complex technical challenges
- **Best Practices** - Type safety, logging, testing, documentation

---

## 📞 Contact

**Developer**: Karan Chavan
**Project**: Private Repository (Code available upon request)
**Purpose**: Professional Portfolio & Interview Showcase

---

## 📝 License

**Private** - Code is proprietary. This README is provided for portfolio and interview purposes.

---

## 🙏 Acknowledgments

- **FastAPI Team** - Outstanding async web framework
- **Alpaca Markets** - Reliable US market data API
- **Dhan** - Comprehensive Indian market data
- **Options Trading Community** - For GEX methodology insights

---

**Built with ❤️ for professional traders who demand precision and speed.**
