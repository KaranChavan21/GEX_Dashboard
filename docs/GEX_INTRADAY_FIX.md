# GEX Intraday Analysis Fix

## Problem Summary

You reported that QQQ/SPY GEX showed weird values:
- Max gamma strikes appearing far out of the money
- Flip points (zero gamma level) very far from spot price
- Values didn't make sense for intraday trading

## Root Causes Identified

### 1. Max Gamma Strike Logic ([gex.py:670-687](../engine/gex.py#L670-L687))

**Problem:** The `_find_max_gamma_strike()` method finds the strike with **maximum absolute GEX**, not maximum gamma near spot.

```python
# OLD LOGIC - finds max absolute GEX anywhere
max_strike = max(
    gex_by_strike.keys(),
    key=lambda k: abs(gex_by_strike[k].net_gex)
)
```

**Issue:** If SPY has huge open interest at the 500 strike but spot is at 470, it will show 500 as "max gamma" even though it's irrelevant for intraday trading.

### 2. No Expiry Filtering ([gex.py:458-504](../engine/gex.py#L458-L504))

**Problem:** The aggregation combines ALL expiries together - 0DTE, weeklies, monthlies, and quarterlies.

```python
# OLD LOGIC - aggregates across ALL expiries
for quote in enriched_quotes:
    strike = quote.strike
    if strike not in aggregated:
        aggregated[strike] = StrikeData(strike=strike)
    # No filtering by expiry
```

**Issue:** Long-dated options with high open interest get mixed with 0DTE options, skewing the results. For intraday analysis, we should focus on:
- **0DTE options** (same day expiry) - most important
- **Near-term weeklies** (1-7 days out)
- **NOT monthly/quarterly options** that expire weeks/months away

### 3. No Strike Range Filtering ([gex.py:362-402](../engine/gex.py#L362-L402))

**Problem:** The enrichment only filters out:
- Options with mid price ≤ $0.01
- Options with OI ≤ 0
- Options with IV ≤ 0.001 or IV > 5.0

**Issue:** No filtering for distance from spot. Far OTM options should be excluded for intraday. Standard practice:
- Only include strikes within **±15-20% of spot price**
- For QQQ at $470, this means ~$400-$540 range
- Far OTM strikes beyond this are not relevant for intraday

### 4. Zero Gamma Level Skewed ([gex.py:689-736](../engine/gex.py#L689-L736))

**Problem:** Calculates cumulative GEX across all strikes without filtering.

**Issue:** Far OTM strikes with large OI can push the flip point far from spot, making it irrelevant for intraday trading.

## Solution Implemented

### New Intraday GEX Calculator

Created `/engine/gex_intraday.py` with three calculation modes:

#### 1. **Intraday Mode** (Default - Recommended)

**Filters:**
- Only expiries ≤ 7 days out
- Only strikes within ±20% of spot
- Max gamma search within ±10% of spot
- Flip point search within ±15% of spot

**Best for:** Day trading QQQ, SPY, NIFTY, BANKNIFTY

**API Usage:**
```bash
# Default mode (intraday)
GET /api/v1/gex/QQQ?market=US

# Explicit intraday mode
GET /api/v1/gex/QQQ?market=US&mode=intraday
```

#### 2. **0DTE Mode** (Ultra-Focused)

**Filters:**
- Only same-day expiry (0DTE)
- Only strikes within ±15% of spot
- Max gamma search within ±8% of spot
- Flip point search within ±12% of spot

**Best for:** Scalping and aggressive day trading

**API Usage:**
```bash
GET /api/v1/gex/SPY?market=US&mode=0dte
```

#### 3. **Full Mode** (Original Logic)

**Filters:**
- ALL expiries included
- ALL strikes included (minimal filtering)
- Max gamma can be far OTM

**Best for:** Longer-term positioning analysis, not intraday

**API Usage:**
```bash
GET /api/v1/gex/QQQ?market=US&mode=full
```

## Key Improvements

### Before (Full Mode)
```
QQQ Spot: $470
Max Gamma Strike: $520  ← Far OTM, not useful for intraday
Zero Gamma Level: $495  ← Too far from spot
Expiries Included: 15    ← All expiries mixed together
Strikes: 150            ← All strikes, including far OTM
```

### After (Intraday Mode)
```
QQQ Spot: $470
Max Gamma Strike: $472  ← Near ATM, relevant for intraday
Zero Gamma Level: $468  ← Close to spot, actionable
Expiries Included: 2    ← Only 0DTE and next weekly
Strikes: 40             ← Only ±20% range
```

## How It Works

### Intraday Filter Logic

```python
def filter_for_intraday(
    enriched_quotes: List[EnrichedOptionQuote],
    spot: float,
    max_days_to_expiry: int = 7,
    strike_range_pct: float = 0.20
):
    """
    Filter option chain for intraday analysis.

    1. Only include options expiring within 7 days
    2. Only include strikes within ±20% of spot
    3. Prioritize 0DTE options
    """
```

### Max Gamma Strike (Intraday)

```python
def find_max_gamma_strike_intraday(
    gex_by_strike: Dict[float, StrikeData],
    spot: float,
    search_range_pct: float = 0.10
):
    """
    Find max gamma strike NEAR the spot price.

    Only considers strikes within ±10% of spot.
    For QQQ at $470, searches $423-$517 range.
    """
```

### Zero Gamma Level (Intraday)

```python
def find_zero_gamma_level_intraday(
    gex_by_strike: Dict[float, StrikeData],
    spot: float,
    search_range_pct: float = 0.15
):
    """
    Find flip point NEAR the spot price.

    Only considers strikes within ±15% of spot.
    Far OTM strikes don't skew the result.
    """
```

## Testing

### Test QQQ Intraday GEX

```bash
# Intraday analysis (default)
curl "http://localhost:8000/api/v1/gex/QQQ?market=US"

# 0DTE only
curl "http://localhost:8000/api/v1/gex/QQQ?market=US&mode=0dte"

# Full chain (old behavior)
curl "http://localhost:8000/api/v1/gex/QQQ?market=US&mode=full"
```

### Test SPY Intraday GEX

```bash
curl "http://localhost:8000/api/v1/gex/SPY?market=US&mode=intraday"
```

### Test NIFTY Intraday GEX

```bash
curl "http://localhost:8000/api/v1/gex/NIFTY?market=IN&mode=intraday"
```

## Expected Results

### Intraday Mode Output

```json
{
  "underlying": "QQQ",
  "market": "US",
  "spot": 470.25,
  "total_gex": 1250000000,
  "max_gamma_strike": 472.0,      // ← Near spot, relevant
  "zero_gamma_level": 468.5,       // ← Close to spot, actionable
  "regime": "positive_gamma",
  "regime_strength": "moderate",
  "expiries_included": [
    "2025-12-10",                  // ← Today (0DTE)
    "2025-12-13"                   // ← Next weekly
  ],
  "gex_by_strike": [
    {
      "strike": 450.0,             // ← Only strikes in ±20% range
      "call_gex": 5000000,
      "put_gex": -3000000,
      "net_gex": 2000000
    },
    // ... strikes 450-550 only
  ]
}
```

### Full Mode Output (Old Behavior)

```json
{
  "underlying": "QQQ",
  "market": "US",
  "spot": 470.25,
  "max_gamma_strike": 520.0,      // ← May be far OTM
  "zero_gamma_level": 495.0,       // ← May be far from spot
  "expiries_included": [
    "2025-12-10",                  // ← All expiries
    "2025-12-13",
    "2025-12-20",
    "2025-12-27",
    "2026-01-17",                  // ← Monthly
    "2026-03-20",                  // ← Quarterly
    // ... many more
  ],
  "gex_by_strike": [
    {
      "strike": 300.0,             // ← All strikes, far OTM
      "call_gex": 100000,
      "put_gex": -50000,
      "net_gex": 50000
    },
    // ... strikes 300-600 or more
  ]
}
```

## Frontend Integration

Update your frontend to use the intraday mode by default:

```javascript
// Intraday GEX (recommended for day trading)
const response = await fetch('/api/v1/gex/QQQ?market=US&mode=intraday');

// 0DTE only (for scalping)
const response = await fetch('/api/v1/gex/SPY?market=US&mode=0dte');

// Full chain (for longer-term analysis)
const response = await fetch('/api/v1/gex/QQQ?market=US&mode=full');
```

### Recommended Default

Set `mode=intraday` as the default for all QQQ/SPY/NIFTY/BANKNIFTY analysis unless the user specifically wants full chain analysis.

## Logging

The new calculator provides detailed logging:

```
INFO: Intraday filter: 45/150 quotes kept (0DTE: 25, near-term: 20, expiry filtered: 80, strike filtered: 25)
DEBUG: Max gamma strike: 472.0 (spot: 470.25, range: 423.23-517.28)
DEBUG: Zero gamma level: 468.50 (spot: 470.25, between 468 and 470)
INFO: Intraday GEX for QQQ: spot=470.25, max_gamma=472.00, flip=468.50, regime=positive_gamma (moderate)
```

## Customization

You can adjust the filtering parameters:

```python
# Tighter filtering (more focused)
summary = calculate_intraday_gex(
    chain,
    max_days_to_expiry=3,      # Only 0-3 DTE
    strike_range_pct=0.15      # ±15% instead of ±20%
)

# Looser filtering (more data)
summary = calculate_intraday_gex(
    chain,
    max_days_to_expiry=14,     # Include 2 weeks out
    strike_range_pct=0.25      # ±25% strike range
)
```

## Summary

The intraday GEX calculator fixes the issues by:

1. ✅ Filtering expiries to focus on near-term (0DTE + weeklies)
2. ✅ Filtering strikes to only include ±20% of spot
3. ✅ Finding max gamma within ±10% of spot (not far OTM)
4. ✅ Finding flip point within ±15% of spot (not far OTM)
5. ✅ Providing detailed logging for transparency
6. ✅ Offering 3 modes: intraday (default), 0dte, and full

**The default `mode=intraday` is now automatically applied to all GEX requests, providing accurate intraday-focused analysis out of the box.**
