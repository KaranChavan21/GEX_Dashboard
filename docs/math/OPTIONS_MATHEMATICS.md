# Options Mathematics - Complete Guide

> Comprehensive mathematical foundations for options pricing, Greeks, and Gamma Exposure

## Table of Contents

1. [Introduction to Options](#1-introduction-to-options)
2. [The Black-Scholes Model](#2-the-black-scholes-model)
3. [The Greeks - First & Second Order Derivatives](#3-the-greeks)
4. [Implied Volatility Calculation](#4-implied-volatility-calculation)
5. [Gamma Exposure (GEX) Theory](#5-gamma-exposure-gex-theory)
6. [Delta Exposure (DEX)](#6-delta-exposure-dex)
7. [Market Maker Hedging Mechanics](#7-market-maker-hedging-mechanics)
8. [Mathematical Proofs & Derivations](#8-mathematical-proofs--derivations)

---

## 1. Introduction to Options

### 1.1 What is an Option?

An **option** is a derivative contract that gives the holder the **right, but not the obligation**, to buy (call) or sell (put) an underlying asset at a predetermined price (strike) before a certain date (expiry).

### 1.2 Option Terminology

| Term | Symbol | Description |
|------|--------|-------------|
| Spot Price | S | Current price of underlying asset |
| Strike Price | K | Predetermined exercise price |
| Time to Expiry | T | Time remaining until expiration (in years) |
| Volatility | σ | Annualized standard deviation of returns |
| Risk-Free Rate | r | Annualized risk-free interest rate |
| Option Price | V | Current market value of the option |

### 1.3 Payoff Functions

**Call Option Payoff at Expiry:**
```
Payoff_Call = max(S_T - K, 0)
```

**Put Option Payoff at Expiry:**
```
Payoff_Put = max(K - S_T, 0)
```

Where S_T is the spot price at expiration.

### 1.4 Moneyness

| State | Call Condition | Put Condition | Description |
|-------|---------------|---------------|-------------|
| ITM (In-The-Money) | S > K | S < K | Has intrinsic value |
| ATM (At-The-Money) | S ≈ K | S ≈ K | Near break-even |
| OTM (Out-of-The-Money) | S < K | S > K | No intrinsic value |

---

## 2. The Black-Scholes Model

### 2.1 Assumptions

The Black-Scholes-Merton (BSM) model assumes:

1. **Log-normal distribution** of asset prices
2. **Constant volatility** (σ) over the option's life
3. **Constant risk-free rate** (r)
4. **No dividends** (or known dividend yield)
5. **European-style options** (exercise only at expiry)
6. **No transaction costs** or taxes
7. **Continuous trading** is possible
8. **Markets are efficient** (no arbitrage)

### 2.2 The Fundamental PDE

The Black-Scholes Partial Differential Equation:

```
∂V/∂t + ½σ²S²(∂²V/∂S²) + rS(∂V/∂S) - rV = 0
```

Where:
- V = Option value
- t = Time
- S = Spot price
- σ = Volatility
- r = Risk-free rate

### 2.3 Closed-Form Solution

**Call Option Price:**
```
C = S·N(d₁) - K·e^(-rT)·N(d₂)
```

**Put Option Price:**
```
P = K·e^(-rT)·N(-d₂) - S·N(-d₁)
```

Where:
```
d₁ = [ln(S/K) + (r + σ²/2)T] / (σ√T)

d₂ = d₁ - σ√T
```

And N(x) is the **cumulative standard normal distribution function**:
```
N(x) = (1/√2π) ∫_{-∞}^{x} e^(-t²/2) dt
```

### 2.4 Intuitive Interpretation

Breaking down the call option formula:

```
C = S·N(d₁) - K·e^(-rT)·N(d₂)
    └──┬───┘   └───────┬───────┘
     Part A         Part B
```

- **Part A: S·N(d₁)** = Expected value of receiving stock if ITM
- **Part B: K·e^(-rT)·N(d₂)** = Present value of paying strike if ITM
- **N(d₂)** ≈ Probability that option expires ITM (risk-neutral)
- **N(d₁)** = Delta (hedge ratio)

### 2.5 Put-Call Parity

For European options with the same strike and expiry:

```
C - P = S - K·e^(-rT)
```

This relationship allows deriving put prices from call prices and vice versa.

---

## 3. The Greeks

The "Greeks" measure the sensitivity of option prices to various factors.

### 3.1 First-Order Greeks

#### Delta (Δ) - Price Sensitivity

**Definition:** Rate of change of option price with respect to underlying price.

```
Δ = ∂V/∂S
```

**Formulas:**
```
Δ_call = N(d₁)        Range: [0, 1]
Δ_put  = N(d₁) - 1    Range: [-1, 0]
```

**Interpretation:**
- Delta ≈ Probability of expiring ITM (approximately)
- Delta = Hedge ratio (shares needed to hedge 1 option)
- ATM options have delta ≈ ±0.5

**Example:**
- Call with Δ = 0.60 means:
  - If stock moves +$1, option moves +$0.60
  - Need to short 60 shares to delta-hedge 100 calls

#### Theta (Θ) - Time Decay

**Definition:** Rate of change of option price with respect to time.

```
Θ = ∂V/∂t
```

**Formulas:**
```
Θ_call = -[S·N'(d₁)·σ / (2√T)] - r·K·e^(-rT)·N(d₂)

Θ_put  = -[S·N'(d₁)·σ / (2√T)] + r·K·e^(-rT)·N(-d₂)
```

Where N'(x) is the standard normal PDF:
```
N'(x) = (1/√2π)·e^(-x²/2)
```

**Interpretation:**
- Theta is typically negative (options lose value over time)
- Theta accelerates as expiry approaches
- ATM options have highest theta

**Daily Theta:**
```
Θ_daily = Θ_annual / 365
```

#### Vega (ν) - Volatility Sensitivity

**Definition:** Rate of change of option price with respect to volatility.

```
ν = ∂V/∂σ
```

**Formula (same for calls and puts):**
```
ν = S·√T·N'(d₁)
```

**Interpretation:**
- Vega is always positive (higher vol = higher option price)
- ATM options have highest vega
- Vega decreases as expiry approaches

**Convention:** Usually quoted per 1% change in volatility:
```
ν_1% = ν / 100
```

#### Rho (ρ) - Interest Rate Sensitivity

**Definition:** Rate of change of option price with respect to risk-free rate.

```
ρ = ∂V/∂r
```

**Formulas:**
```
ρ_call = K·T·e^(-rT)·N(d₂)
ρ_put  = -K·T·e^(-rT)·N(-d₂)
```

**Interpretation:**
- Calls increase with higher rates (rho > 0)
- Puts decrease with higher rates (rho < 0)
- Less significant for short-dated options

### 3.2 Second-Order Greeks

#### Gamma (Γ) - Delta Acceleration

**Definition:** Rate of change of delta with respect to underlying price.

```
Γ = ∂²V/∂S² = ∂Δ/∂S
```

**Formula (same for calls and puts):**
```
Γ = N'(d₁) / (S·σ·√T)
```

**Interpretation:**
- Gamma measures how fast delta changes
- Always positive for long options
- Highest for ATM options near expiry
- **Critical for GEX calculations**

**Gamma Profile:**
```
     Γ
     │    ╱╲
     │   ╱  ╲
     │  ╱    ╲
     │ ╱      ╲
     └─────────────── S
       OTM  ATM  ITM
```

#### Vanna - Delta-Vol Cross

**Definition:** Sensitivity of delta to volatility (or vega to spot).

```
Vanna = ∂²V/∂S∂σ = ∂Δ/∂σ = ∂ν/∂S
```

**Formula:**
```
Vanna = (ν/S)·[1 - d₁/(σ√T)]
     = -N'(d₁)·d₂/σ
```

**Interpretation:**
- Measures how delta changes with volatility
- Important for understanding vol-driven moves
- Used in advanced hedging strategies

#### Charm - Delta-Time Cross

**Definition:** Rate of change of delta over time.

```
Charm = ∂²V/∂S∂t = ∂Δ/∂t = ∂Θ/∂S
```

**Formula:**
```
Charm = -N'(d₁)·[2(r-q)T - d₂·σ√T] / (2T·σ√T)
```

**Interpretation:**
- "Delta bleed" - how delta changes overnight
- Critical for 0DTE options
- Explains gamma flips during the trading day

#### Vomma (Volga) - Vega Convexity

**Definition:** Rate of change of vega with respect to volatility.

```
Vomma = ∂²V/∂σ² = ∂ν/∂σ
```

**Formula:**
```
Vomma = ν·d₁·d₂/σ
```

**Interpretation:**
- Measures vega's sensitivity to vol changes
- Important for vol-of-vol trading
- Highest for OTM options

### 3.3 Greeks Summary Table

| Greek | Symbol | Formula | Measures |
|-------|--------|---------|----------|
| Delta | Δ | ∂V/∂S | Price sensitivity |
| Gamma | Γ | ∂²V/∂S² | Delta acceleration |
| Theta | Θ | ∂V/∂t | Time decay |
| Vega | ν | ∂V/∂σ | Vol sensitivity |
| Rho | ρ | ∂V/∂r | Rate sensitivity |
| Vanna | | ∂²V/∂S∂σ | Delta-vol cross |
| Charm | | ∂²V/∂S∂t | Delta decay |
| Vomma | | ∂²V/∂σ² | Vega convexity |

---

## 4. Implied Volatility Calculation

### 4.1 The Problem

**Given:** Market option price V_market
**Find:** Volatility σ that satisfies BS(σ) = V_market

This is an **inverse problem** - no closed-form solution exists.

### 4.2 Newton-Raphson Method

**Algorithm:**
```
σ_{n+1} = σ_n - [BS(σ_n) - V_market] / Vega(σ_n)
```

**Pseudo-code:**
```python
def calculate_iv_newton(target_price, S, K, T, r, option_type):
    sigma = 0.30  # Initial guess
    tolerance = 0.0001
    max_iterations = 50

    for i in range(max_iterations):
        price = black_scholes_price(S, K, T, sigma, r, option_type)
        vega = black_scholes_vega(S, K, T, sigma, r)

        diff = price - target_price

        if abs(diff) < tolerance:
            return sigma

        if vega > 0.0001:  # Avoid division by zero
            sigma = sigma - diff / vega

        # Bounds: 1% to 500%
        sigma = max(0.01, min(5.0, sigma))

    return NaN  # Did not converge
```

**Convergence:**
- Newton-Raphson converges **quadratically** (very fast)
- Typically 3-5 iterations for ATM options
- May struggle for deep OTM options

### 4.3 Brent's Method (Fallback)

For cases where Newton-Raphson fails (e.g., very low vega):

```python
def calculate_iv_brent(target_price, S, K, T, r, option_type):
    """
    Brent's method: bracket the root, then converge
    More robust but slower than Newton-Raphson
    """
    def objective(sigma):
        return black_scholes_price(S, K, T, sigma, r, option_type) - target_price

    # Bracket: volatility between 1% and 500%
    sigma_low = 0.01
    sigma_high = 5.0

    # Use scipy's brentq or implement manually
    return brentq(objective, sigma_low, sigma_high)
```

### 4.4 IV Surface

Implied volatility varies by:
1. **Strike** (volatility smile/skew)
2. **Expiry** (term structure)

```
        IV
        │     ╱ OTM Puts
        │    ╱
        │   ╱    ╲ OTM Calls
        │  ╱      ╲
        │ ╱        ╲
        └──────────────── Strike
              ATM

        "Volatility Smile"
```

---

## 5. Gamma Exposure (GEX) Theory

### 5.1 What is Gamma Exposure?

**Gamma Exposure (GEX)** measures the **total gamma-weighted open interest** across all options at a given strike. It quantifies the **hedging pressure** that market makers must exert.

### 5.2 The GEX Formula

For a single option contract:
```
GEX_contract = Γ × OI × Multiplier × S²
```

Where:
- Γ = Gamma of the option
- OI = Open Interest (number of contracts)
- Multiplier = Contract multiplier (100 for US equities)
- S = Current spot price

**Aggregate GEX at a strike:**
```
GEX_strike = Σ(GEX_calls) + Σ(GEX_puts)
```

### 5.3 Sign Convention

**Critical insight:** The sign depends on WHO holds the option.

**Assumption:** Market makers are NET SHORT options (retail is long)

| Position | Call GEX | Put GEX |
|----------|----------|---------|
| Market Maker Short Call | Negative Gamma | |
| Market Maker Short Put | | Positive Gamma |
| **Convention Used** | +GEX (Resistance) | -GEX (Support) |

**Why this convention?**
- Short calls: MM must sell as price rises → creates resistance
- Short puts: MM must buy as price falls → creates support

### 5.4 GEX Calculation Example

**Given:**
- SPY Spot: $580
- Call at $585 strike
- Gamma: 0.02
- Open Interest: 10,000 contracts
- Multiplier: 100

**Calculation:**
```
GEX = 0.02 × 10,000 × 100 × 580²
    = 0.02 × 10,000 × 100 × 336,400
    = 6,728,000,000 (≈ $6.7 billion)
```

This means: For every $1 move in SPY, market makers must hedge $6.7B worth of stock.

### 5.5 Total GEX

**Aggregate across all strikes:**
```
Total GEX = Σ(GEX_strike) for all strikes
```

**Interpretation:**
- **Positive Total GEX:** Net call gamma dominates → mean reversion
- **Negative Total GEX:** Net put gamma dominates → trending/volatile
- **Near Zero:** Balanced positioning → unpredictable

### 5.6 Key GEX Levels

#### Max Gamma Strike
```
Max Gamma Strike = argmax|GEX_strike|
```
The strike with highest absolute GEX concentration.

#### Zero Gamma Level (Flip Point)
```
Find S* where: Σ GEX(S*) = 0
```
The price level where net GEX flips from positive to negative.

**Calculation Method:**
```python
def find_zero_gamma(gex_by_strike, spot):
    # Sort strikes
    strikes = sorted(gex_by_strike.keys())

    # Calculate cumulative GEX
    cumulative = 0
    for strike in strikes:
        prev_cumulative = cumulative
        cumulative += gex_by_strike[strike]

        # Check for zero crossing
        if prev_cumulative * cumulative < 0:
            # Interpolate
            ratio = abs(prev_cumulative) / (abs(prev_cumulative) + abs(cumulative))
            return strikes[i-1] + ratio * (strike - strikes[i-1])

    return None  # No flip point found
```

#### Call Wall / Put Wall
```
Call Wall = Strike with highest positive GEX
Put Wall = Strike with most negative GEX (highest magnitude)
```

These represent major support/resistance levels.

---

## 6. Delta Exposure (DEX)

### 6.1 DEX Formula

```
DEX_contract = Δ × OI × Multiplier × S
```

### 6.2 Aggregate DEX

```
Total DEX = Σ(DEX_calls) + Σ(DEX_puts)
```

### 6.3 Interpretation

- **DEX** measures directional exposure
- Less useful than GEX for intraday levels
- More relevant for understanding dealer positioning

---

## 7. Market Maker Hedging Mechanics

### 7.1 The Delta Hedging Process

**Scenario:** Market maker sells 100 call options (Δ = 0.50)

**Initial Hedge:**
```
Shares to buy = Δ × Contracts × Multiplier
             = 0.50 × 100 × 100
             = 5,000 shares
```

**As Price Moves:**
- Price ↑ → Delta ↑ (e.g., 0.50 → 0.60)
- New hedge = 0.60 × 100 × 100 = 6,000 shares
- **Action: Buy 1,000 more shares**

This is why **short gamma = buy high, sell low** (amplifies moves).

### 7.2 Gamma Regimes

#### Positive Gamma Regime (Above Max Gamma)

```
Price ↑ → Dealers sell (hedge) → Resistance
Price ↓ → Dealers buy (hedge) → Support
Result: Mean reversion, low volatility
```

**Visualization:**
```
        Price
          ↑  ← Dealers sell
          │
          │  ← Equilibrium (Max Gamma)
          │
          ↓  ← Dealers buy

        Dampening Effect (Low Vol)
```

#### Negative Gamma Regime (Below Max Gamma)

```
Price ↑ → Dealers buy (chase) → Amplification
Price ↓ → Dealers sell (chase) → Amplification
Result: Trending, high volatility
```

**Visualization:**
```
        Price
          ↑  ← Dealers buy (chase)
          │
          │  ← Below Max Gamma
          │
          ↓  ← Dealers sell (chase)

        Amplification Effect (High Vol)
```

### 7.3 GEX Flow Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                    POSITIVE GAMMA REGIME                     │
│                    (Spot > Max Gamma)                        │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│    Price Rises          │           Price Falls             │
│         ↓               │                ↓                  │
│    Delta ↑              │           Delta ↓                 │
│         ↓               │                ↓                  │
│  MM Over-hedged         │       MM Under-hedged             │
│         ↓               │                ↓                  │
│    SELL Stock           │           BUY Stock               │
│         ↓               │                ↓                  │
│  Creates Resistance     │        Creates Support            │
│         ↓               │                ↓                  │
│  MEAN REVERSION ← ─ ─ ─ ┴ ─ ─ ─ → MEAN REVERSION           │
│                                                              │
│               Volatility SUPPRESSED                          │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                   NEGATIVE GAMMA REGIME                      │
│                    (Spot < Max Gamma)                        │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│    Price Rises          │           Price Falls             │
│         ↓               │                ↓                  │
│    Delta ↑              │           Delta ↓                 │
│         ↓               │                ↓                  │
│  MM Under-hedged        │       MM Over-hedged              │
│         ↓               │                ↓                  │
│    BUY Stock            │          SELL Stock               │
│         ↓               │                ↓                  │
│   Adds Fuel to Rally    │      Adds Fuel to Selloff        │
│         ↓               │                ↓                  │
│  MOMENTUM ← ─ ─ ─ ─ ─ ─ ┴ ─ ─ ─ ─ → MOMENTUM               │
│                                                              │
│               Volatility AMPLIFIED                           │
└─────────────────────────────────────────────────────────────┘
```

---

## 8. Mathematical Proofs & Derivations

### 8.1 Deriving the Black-Scholes Formula

**Starting Point:** Risk-neutral pricing

The option price is the discounted expected payoff under the risk-neutral measure Q:

```
C = e^(-rT) · E_Q[max(S_T - K, 0)]
```

**Step 1:** Under Q, the stock follows:
```
dS/S = r·dt + σ·dW_Q
```

**Step 2:** Therefore:
```
S_T = S_0 · exp[(r - σ²/2)T + σ√T·Z]
```
Where Z ~ N(0,1)

**Step 3:** The expectation becomes:
```
C = e^(-rT) · ∫_{-∞}^{∞} max(S_0·e^((r-σ²/2)T + σ√T·z) - K, 0) · φ(z) dz
```

**Step 4:** The integral evaluates to:
```
C = S_0·N(d₁) - K·e^(-rT)·N(d₂)
```

(Full derivation requires 2-3 pages of calculus)

### 8.2 Deriving Delta

**By definition:**
```
Δ_call = ∂C/∂S = ∂/∂S [S·N(d₁) - K·e^(-rT)·N(d₂)]
```

**Using the chain rule:**
```
= N(d₁) + S·N'(d₁)·∂d₁/∂S - K·e^(-rT)·N'(d₂)·∂d₂/∂S
```

**Key insight:**
```
∂d₁/∂S = ∂d₂/∂S = 1/(S·σ√T)
```

**And the identity:**
```
S·N'(d₁) = K·e^(-rT)·N'(d₂)
```

**Therefore:**
```
Δ_call = N(d₁)
```

### 8.3 Deriving Gamma

```
Γ = ∂Δ/∂S = ∂N(d₁)/∂S = N'(d₁)·∂d₁/∂S
```

Since:
```
∂d₁/∂S = 1/(S·σ√T)
```

Therefore:
```
Γ = N'(d₁)/(S·σ√T)
```

### 8.4 GEX Derivation

**Market maker's delta hedge for short position:**
```
Hedge = -Δ × Position × Multiplier
```

**Change in hedge for $1 move:**
```
ΔHedge = Γ × Position × Multiplier × ΔS
```

**For ΔS = $1:**
```
ΔHedge = Γ × OI × Multiplier
```

**Dollar value of hedge change:**
```
GEX = Γ × OI × Multiplier × S²
```

The S² term comes from:
- One S from the gamma formula (∂Delta per $1)
- One S from the notional value of shares to trade

---

## Summary

This document covered:

1. **Options Fundamentals** - Terminology, payoffs, moneyness
2. **Black-Scholes Model** - Assumptions, PDE, closed-form solution
3. **Greeks** - All first and second-order sensitivities
4. **IV Calculation** - Newton-Raphson and Brent's method
5. **GEX Theory** - Formula, sign convention, key levels
6. **DEX** - Delta-based exposure
7. **Market Maker Mechanics** - Hedging flows and regime effects
8. **Mathematical Proofs** - Derivations for key formulas

---

## References

1. Black, F., & Scholes, M. (1973). "The Pricing of Options and Corporate Liabilities"
2. Hull, J. C. (2018). "Options, Futures, and Other Derivatives" (10th ed.)
3. Taleb, N. N. (1997). "Dynamic Hedging: Managing Vanilla and Exotic Options"
4. Wilmott, P. (2006). "Paul Wilmott on Quantitative Finance"
5. Natenberg, S. (1994). "Option Volatility and Pricing"

---

**This document represents original research and synthesis of established financial mathematics.**
