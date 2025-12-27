# API Usage Examples

> Real-world examples of using the GEX Analytics API for trading decisions

## Table of Contents

- [Quick Start](#quick-start)
- [US Market Examples](#us-market-examples)
- [Indian Market Examples](#indian-market-examples)
- [Trading Scenarios](#trading-scenarios)
- [Response Interpretation](#response-interpretation)
- [Integration Examples](#integration-examples)

---

## Quick Start

### Base URL
```
http://localhost:6666
```

### Authentication
Currently using IP whitelisting (production would use JWT tokens).

---

## US Market Examples

### Example 1: SPY Intraday Analysis

**Request**:
```bash
GET /api/ai/gex-summary/SPY?market=US&strike_count=10&include_ohlc=true
```

**Response**:
```json
{
  "symbol": "SPY",
  "market": "US",
  "spot": 580.50,
  "timestamp": "2025-12-24T14:30:00Z",
  "broker_timestamp": "2025-12-24T14:29:55Z",

  "regime": "positive_gamma",
  "regime_strength": "strong",

  "key_levels": {
    "max_gamma_strike": 580.0,
    "zero_gamma_flip": 575.0,
    "call_wall": 585.0,
    "put_wall": 575.0
  },

  "strikes": [
    {
      "strike": 570.0,
      "quote_timestamp": "2025-12-24T14:29:50Z",
      "distance_pct": -1.81,
      "call_gex": 5200000,
      "put_gex": -15800000,
      "net_gex": -10600000,
      "call_oi": 8500,
      "put_oi": 18200,
      "put_call_ratio": 2.14,
      "total_volume": 4520,
      "call_iv": 0.145,
      "put_iv": 0.162
    },
    {
      "strike": 575.0,
      "quote_timestamp": "2025-12-24T14:29:52Z",
      "distance_pct": -0.95,
      "call_gex": 12500000,
      "put_gex": -45000000,
      "net_gex": -32500000,
      "call_oi": 15000,
      "put_oi": 35000,
      "put_call_ratio": 2.33,
      "total_volume": 8920,
      "call_iv": 0.138,
      "put_iv": 0.155
    },
    {
      "strike": 580.0,
      "quote_timestamp": "2025-12-24T14:29:55Z",
      "distance_pct": -0.09,
      "call_gex": 85000000,
      "put_gex": -25000000,
      "net_gex": 60000000,
      "call_oi": 45000,
      "put_oi": 22000,
      "put_call_ratio": 0.49,
      "total_volume": 15680,
      "call_iv": 0.132,
      "put_iv": 0.148
    },
    {
      "strike": 585.0,
      "quote_timestamp": "2025-12-24T14:29:53Z",
      "distance_pct": 0.77,
      "call_gex": 92000000,
      "put_gex": -8500000,
      "net_gex": 83500000,
      "call_oi": 52000,
      "put_oi": 9500,
      "put_call_ratio": 0.18,
      "total_volume": 12340,
      "call_iv": 0.140,
      "put_iv": 0.145
    },
    {
      "strike": 590.0,
      "quote_timestamp": "2025-12-24T14:29:48Z",
      "distance_pct": 1.64,
      "call_gex": 35000000,
      "put_gex": -2800000,
      "net_gex": 32200000,
      "call_oi": 28000,
      "put_oi": 3200,
      "put_call_ratio": 0.11,
      "total_volume": 6780,
      "call_iv": 0.148,
      "put_iv": 0.142
    }
  ],

  "top_resistance": [
    {"strike": 585.0, "gex": 83500000},
    {"strike": 590.0, "gex": 32200000},
    {"strike": 595.0, "gex": 18500000}
  ],

  "top_support": [
    {"strike": 575.0, "gex": -32500000},
    {"strike": 570.0, "gex": -10600000},
    {"strike": 565.0, "gex": -8200000}
  ],

  "flow_signals": {
    "net_call_oi": 185000,
    "net_put_oi": 122000,
    "pcr": 0.66,
    "sentiment": "bullish_positioning",
    "total_gex": 245000000,
    "call_gex_pct": 68.5
  },

  "iv_data": {
    "avg_call_iv": 0.142,
    "avg_put_iv": 0.152,
    "iv_skew": 0.010,
    "skew_interpretation": "puts_expensive"
  },

  "ohlc_data": {
    "timeframe": "5Min",
    "bars_count": 12,
    "period": "1 hour",
    "latest": {
      "time": "2025-12-24T14:25:00Z",
      "open": 580.35,
      "high": 580.62,
      "low": 580.28,
      "close": 580.50,
      "volume": 1250000
    },
    "period_metrics": {
      "high": 581.20,
      "low": 579.80,
      "range": 1.40,
      "range_pct": 0.24,
      "change_pct": 0.58,
      "trend": "bullish"
    },
    "volume_analysis": {
      "total_volume": 8500000,
      "avg_volume": 708333,
      "recent_volume": 1250000,
      "volume_ratio": 1.76,
      "volume_trend": "increasing"
    }
  }
}
```

**Interpretation**:
- **Spot at $580.50** - Right at max gamma strike ($580)
- **Regime: Positive Gamma (Strong)** - Expect mean reversion, limited volatility
- **Call Wall at $585** - Strong resistance with 92M GEX
- **Put Wall at $575** - Strong support with -32.5M GEX
- **PCR 0.66** - Bullish positioning (more calls than puts)
- **IV Skew** - Puts slightly expensive (fear premium)
- **Recent Trend** - Bullish (+0.58% in 1hr), volume increasing

**Trading Plan**:
- ✅ **Stay long** above $575 (flip level and put wall)
- 🎯 **Target $585** (call wall resistance)
- ❌ **Cut losses** below $575 (regime change to negative gamma)
- ⚠️ **Expect chop** between $575-$585 (positive gamma regime)

---

### Example 2: QQQ 0DTE Trading

**Request**:
```bash
GET /api/ai/quick-levels/QQQ?market=US
```

**Response**:
```json
{
  "symbol": "QQQ",
  "spot": 525.50,
  "broker_timestamp": "2025-12-24T14:29:55Z",
  "max_gamma": 525.0,
  "flip": 520.0,
  "call_wall": 530.0,
  "put_wall": 520.0,
  "regime": "positive_gamma",
  "pcr": 0.85
}
```

**Use Case**: Ultra-fast polling (every 30 seconds) for algorithmic trading

---

### Example 3: NVDA Weekly Options

**Request**:
```bash
GET /api/ai/gex-summary/NVDA?market=US&strike_count=15&include_ohlc=false
```

**Key Levels Returned**:
```json
{
  "symbol": "NVDA",
  "spot": 145.80,
  "regime": "negative_gamma",
  "key_levels": {
    "max_gamma_strike": 150.0,
    "zero_gamma_flip": 148.0,
    "call_wall": 155.0,
    "put_wall": 140.0
  }
}
```

**Interpretation**:
- **Negative Gamma Regime** - Expect amplified moves (gamma squeeze potential)
- **Below Max Gamma** - Dealers will sell into dips (downward pressure)
- **Above Flip Level** - But still in transition zone
- **Wide Call/Put Walls** - Large expected range ($140-$155 = 10%)

**Trading Strategy**:
- Watch for break above $150 (max gamma) → regime change to positive
- Below $148 (flip) → expect acceleration down to $140 put wall
- High IV environment → sell premium strategies

---

## Indian Market Examples

### Example 4: NIFTY Weekly Expiry

**Request**:
```bash
GET /api/ai/gex-summary/NIFTY?market=IN&strike_count=20
```

**Response**:
```json
{
  "symbol": "NIFTY",
  "market": "IN",
  "spot": 23450.0,
  "broker_timestamp": "2025-12-24T09:15:30Z",

  "regime": "negative_gamma",
  "regime_strength": "moderate",

  "key_levels": {
    "max_gamma_strike": 23500.0,
    "zero_gamma_flip": 23400.0,
    "call_wall": 23600.0,
    "put_wall": 23300.0
  },

  "flow_signals": {
    "net_call_oi": 2500000,
    "net_put_oi": 4200000,
    "pcr": 1.68,
    "sentiment": "bearish_positioning",
    "total_gex": -85000000,
    "call_gex_pct": 35.2
  },

  "iv_data": {
    "avg_call_iv": 0.165,
    "avg_put_iv": 0.198,
    "iv_skew": 0.033,
    "skew_interpretation": "puts_expensive"
  }
}
```

**Interpretation**:
- **High PCR (1.68)** - Heavy put buying (fear)
- **Negative Gamma** - Expect volatile moves
- **Expensive Puts** - +3.3% IV skew (hedging demand)
- **Below Max Gamma** - Dealers selling into dips

**Trading Strategy**:
- Short-term bearish (below max gamma in negative regime)
- Watch $23,400 flip level closely
- Break above $23,500 → regime change, potential short squeeze
- Put selling at $23,300 wall (high IV, strong support)

---

### Example 5: BANKNIFTY Intraday

**Request**:
```bash
GET /api/ai/gex-summary/BANKNIFTY?market=IN&strike_count=15&include_ohlc=true
```

**Key Data**:
```json
{
  "symbol": "BANKNIFTY",
  "spot": 51250.0,
  "regime": "positive_gamma",

  "key_levels": {
    "max_gamma_strike": 51200.0,
    "zero_gamma_flip": 50900.0,
    "call_wall": 51500.0,
    "put_wall": 50900.0
  },

  "ohlc_data": {
    "period_metrics": {
      "high": 51380.0,
      "low": 51120.0,
      "range": 260.0,
      "range_pct": 0.51,
      "change_pct": 0.32,
      "trend": "bullish"
    }
  }
}
```

**Interpretation**:
- **Positive Gamma + Above Max Gamma** - Range-bound expected
- **Tight Range** - 260 points (0.51%) in last hour
- **Strong Walls** - $51,500 call wall, $50,900 put wall (600 point range)

**Intraday Plan**:
- **Range**: 50,900 - 51,500
- **Strategy**: Sell iron condor (positive gamma → low volatility)
- **Stop**: Close if breaks out of range (regime change)

---

## Trading Scenarios

### Scenario 1: Short Squeeze Setup Detection

**Indicators**:
```json
{
  "regime": "negative_gamma",
  "pcr": 0.45,  // Low PCR (lots of calls)
  "spot": 578.0,
  "max_gamma_strike": 585.0,  // Spot below max gamma
  "call_wall": 590.0
}
```

**Analysis**:
- Negative gamma + spot below max gamma → dealers sell on rallies
- But low PCR shows heavy call positioning
- If breaks above $585 → gamma flip + call delta hedging → squeeze

**Trade**: Buy calls if breaks $585 with volume

---

### Scenario 2: Put Wall Defense

**Indicators**:
```json
{
  "regime": "positive_gamma",
  "put_wall": 575.0,
  "put_gex": -85000000,  // Massive put GEX
  "spot": 576.50,
  "iv_skew": 0.025  // Puts expensive
}
```

**Analysis**:
- Huge put wall at $575 (-85M GEX)
- Positive gamma regime → dealers buy dips
- Expensive puts → high hedging demand
- Spot just above wall

**Trade**: Sell put spreads at $575 (high IV, strong support)

---

### Scenario 3: Volatility Expansion Warning

**Indicators**:
```json
{
  "regime": "neutral",
  "regime_strength": "very_weak",
  "total_gex": 5000000,  // Very low total GEX
  "spot": 581.0,
  "flip": 580.0,
  "iv_data": {
    "avg_call_iv": 0.125,
    "avg_put_iv": 0.128,
    "iv_skew": 0.003  // No skew
  }
}
```

**Analysis**:
- Very low GEX → no dealer hedging pressure
- Spot near flip level
- Low IV + no skew → market complacent

**Trade**: Buy straddles (expect volatility expansion)

---

## Response Interpretation

### GEX Values Guide

| Net GEX | Interpretation | Expected Behavior |
|---------|---------------|------------------|
| > +100M | Very Strong Resistance | Mean reversion, low vol |
| +50M to +100M | Strong Resistance | Moderate mean reversion |
| +10M to +50M | Moderate Resistance | Slight dampening |
| -10M to +10M | Neutral | Unpredictable |
| -50M to -10M | Moderate Support | Slight amplification |
| -100M to -50M | Strong Support | Moderate amplification |
| < -100M | Very Strong Support | High vol, trending |

### PCR (Put/Call Ratio) Guide

| PCR | Sentiment | Interpretation |
|-----|-----------|---------------|
| < 0.5 | Extreme Bullish | Potential top, squeeze risk |
| 0.5 - 0.8 | Bullish | Healthy uptrend |
| 0.8 - 1.2 | Neutral | Balanced positioning |
| 1.2 - 1.5 | Bearish | Hedging demand |
| > 1.5 | Extreme Bearish | Potential bottom, unwind risk |

### Regime Trading Guide

| Regime | Spot vs Max Gamma | Strategy |
|--------|------------------|----------|
| Positive Gamma | Spot > Max Gamma | Sell premium, range trading |
| Positive Gamma | Spot < Max Gamma | Long bias, breakout watch |
| Negative Gamma | Spot > Max Gamma | Short bias, breakdown watch |
| Negative Gamma | Spot < Max Gamma | Momentum trading, wide stops |
| Neutral | Any | Wait for clarity, reduce size |

---

## Integration Examples

### Python Client

```python
import httpx
import asyncio

class GEXClient:
    def __init__(self, base_url="http://localhost:6666"):
        self.base_url = base_url
        self.client = httpx.AsyncClient(timeout=30.0)

    async def get_gex_summary(
        self,
        symbol: str,
        market: str,
        strike_count: int = 10
    ):
        url = f"{self.base_url}/api/ai/gex-summary/{symbol}"
        params = {
            "market": market,
            "strike_count": strike_count,
            "include_ohlc": True
        }
        response = await self.client.get(url, params=params)
        response.raise_for_status()
        return response.json()

    async def get_quick_levels(self, symbol: str, market: str):
        url = f"{self.base_url}/api/ai/quick-levels/{symbol}"
        params = {"market": market}
        response = await self.client.get(url, params=params)
        response.raise_for_status()
        return response.json()

# Usage
async def main():
    client = GEXClient()

    # Get SPY analysis
    spy_data = await client.get_gex_summary("SPY", "US")
    print(f"SPY Spot: {spy_data['spot']}")
    print(f"Regime: {spy_data['regime']}")
    print(f"Call Wall: {spy_data['key_levels']['call_wall']}")

    # Quick poll QQQ
    qqq_data = await client.get_quick_levels("QQQ", "US")
    print(f"QQQ Flip: {qqq_data['flip']}")

asyncio.run(main())
```

### Trading Bot Example

```python
class GEXTradingBot:
    def __init__(self, gex_client, broker_client):
        self.gex = gex_client
        self.broker = broker_client

    async def check_breakout(self, symbol: str, market: str):
        """Monitor for regime change breakouts"""
        data = await self.gex.get_quick_levels(symbol, market)

        spot = data['spot']
        flip = data['flip']
        call_wall = data['call_wall']
        regime = data['regime']

        # Short squeeze setup
        if regime == "negative_gamma" and spot > flip + 1:
            # Breaking above flip in negative gamma
            print(f"🚀 BREAKOUT SIGNAL: {symbol} breaking flip level")
            await self.broker.buy_calls(symbol, call_wall, quantity=10)

        # Breakdown warning
        elif regime == "positive_gamma" and spot < flip - 1:
            # Breaking below flip in positive gamma
            print(f"⚠️ BREAKDOWN SIGNAL: {symbol} breaking below flip")
            await self.broker.buy_puts(symbol, data['put_wall'], quantity=10)

    async def range_trading(self, symbol: str, market: str):
        """Range trading in positive gamma"""
        data = await self.gex.get_gex_summary(symbol, market)

        if data['regime'] != "positive_gamma":
            return  # Only trade in positive gamma

        spot = data['spot']
        call_wall = data['key_levels']['call_wall']
        put_wall = data['key_levels']['put_wall']

        # Sell at call wall
        if abs(spot - call_wall) < 1:
            print(f"📉 Selling at call wall: {call_wall}")
            await self.broker.sell_stock(symbol, quantity=100)

        # Buy at put wall
        elif abs(spot - put_wall) < 1:
            print(f"📈 Buying at put wall: {put_wall}")
            await self.broker.buy_stock(symbol, quantity=100)
```

### Alert System

```python
import time

async def monitor_regime_changes(symbols: list):
    """Alert on regime changes"""
    client = GEXClient()
    previous_regimes = {}

    while True:
        for symbol in symbols:
            data = await client.get_quick_levels(symbol, "US")
            current_regime = data['regime']

            if symbol in previous_regimes:
                if previous_regimes[symbol] != current_regime:
                    # Regime changed!
                    print(f"🔔 ALERT: {symbol} regime changed!")
                    print(f"   {previous_regimes[symbol]} → {current_regime}")
                    print(f"   Spot: {data['spot']}, Flip: {data['flip']}")

                    # Send email/SMS/Discord notification
                    await send_alert(
                        f"{symbol}: {previous_regimes[symbol]} → {current_regime}"
                    )

            previous_regimes[symbol] = current_regime

        await asyncio.sleep(30)  # Check every 30 seconds

# Monitor SPY, QQQ, NVDA
asyncio.run(monitor_regime_changes(["SPY", "QQQ", "NVDA"]))
```

---

## Advanced Use Cases

### Multi-Symbol Correlation Analysis

```python
async def analyze_correlation():
    client = GEXClient()

    # Get data for correlated symbols
    spy = await client.get_gex_summary("SPY", "US")
    qqq = await client.get_gex_summary("QQQ", "US")
    iwm = await client.get_gex_summary("IWM", "US")

    # Check for divergence
    if spy['regime'] == "positive_gamma" and qqq['regime'] == "negative_gamma":
        print("⚠️ SPY/QQQ regime divergence detected")
        print(f"SPY: {spy['regime']}, QQQ: {qqq['regime']}")
        print("Possible sector rotation or market uncertainty")
```

### Volatility Term Structure

```python
async def vol_term_structure(symbol: str):
    """Compare GEX across different DTE windows"""

    # 0DTE
    dte0 = await get_gex_summary(symbol, max_dte=0)

    # Weekly
    dte7 = await get_gex_summary(symbol, max_dte=7)

    # Monthly
    dte30 = await get_gex_summary(symbol, max_dte=30)

    print(f"0DTE GEX: {dte0['flow_signals']['total_gex']}")
    print(f"Weekly GEX: {dte7['flow_signals']['total_gex']}")
    print(f"Monthly GEX: {dte30['flow_signals']['total_gex']}")
```

---

**These examples demonstrate production-ready API usage for real-world trading applications.**
