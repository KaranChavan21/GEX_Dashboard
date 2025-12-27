# GEX Analytics Documentation Index

> Complete documentation for understanding and using the GEX Analytics system

---

## 📚 Documentation Structure

```
docs/
├── INDEX.md                           ← You are here
│
├── math/                              ← Mathematical Foundations
│   └── OPTIONS_MATHEMATICS.md         Black-Scholes, Greeks, GEX formulas
│
├── research/                          ← Implementation Research
│   ├── GREEKS_IMPLEMENTATION_RESEARCH.md   IV solving, numerical methods
│   └── GEX_MARKET_MECHANICS.md        Market maker behavior, regime analysis
│
└── concepts/                          ← Practical Guides
    └── TRADING_CONCEPTS.md            Trading applications, strategies
```

---

## 📖 Reading Order

### For Traders (Practical Focus)

1. **Start Here:** [Trading Concepts](concepts/TRADING_CONCEPTS.md)
   - What is GEX?
   - Key levels explained
   - Trading strategies
   - Quick reference card

2. **Deep Dive:** [GEX Market Mechanics](research/GEX_MARKET_MECHANICS.md)
   - Market maker positioning
   - Regime detection research
   - Intraday dynamics

### For Developers (Technical Focus)

1. **Start Here:** [Options Mathematics](math/OPTIONS_MATHEMATICS.md)
   - Black-Scholes model
   - Greeks derivations
   - GEX formulas

2. **Implementation:** [Greeks Implementation Research](research/GREEKS_IMPLEMENTATION_RESEARCH.md)
   - IV solver algorithms
   - Numerical stability
   - Performance optimization

3. **Deep Dive:** [GEX Market Mechanics](research/GEX_MARKET_MECHANICS.md)
   - Aggregation strategies
   - Regime detection
   - Cross-market analysis

---

## 🎯 Quick Links by Topic

### Mathematics
| Topic | Document | Section |
|-------|----------|---------|
| Black-Scholes Formula | [OPTIONS_MATHEMATICS.md](math/OPTIONS_MATHEMATICS.md) | Section 2 |
| Greeks (Delta, Gamma, etc.) | [OPTIONS_MATHEMATICS.md](math/OPTIONS_MATHEMATICS.md) | Section 3 |
| GEX Formula | [OPTIONS_MATHEMATICS.md](math/OPTIONS_MATHEMATICS.md) | Section 5 |
| IV Calculation | [OPTIONS_MATHEMATICS.md](math/OPTIONS_MATHEMATICS.md) | Section 4 |

### Implementation
| Topic | Document | Section |
|-------|----------|---------|
| Newton-Raphson IV Solver | [GREEKS_IMPLEMENTATION_RESEARCH.md](research/GREEKS_IMPLEMENTATION_RESEARCH.md) | Section 1 |
| Gamma Numerical Stability | [GREEKS_IMPLEMENTATION_RESEARCH.md](research/GREEKS_IMPLEMENTATION_RESEARCH.md) | Section 2 |
| GEX Aggregation | [GREEKS_IMPLEMENTATION_RESEARCH.md](research/GREEKS_IMPLEMENTATION_RESEARCH.md) | Section 3 |
| Data Quality Filters | [GREEKS_IMPLEMENTATION_RESEARCH.md](research/GREEKS_IMPLEMENTATION_RESEARCH.md) | Section 5 |

### Trading
| Topic | Document | Section |
|-------|----------|---------|
| Regime Explanation | [TRADING_CONCEPTS.md](concepts/TRADING_CONCEPTS.md) | Section 3 |
| Key Levels | [TRADING_CONCEPTS.md](concepts/TRADING_CONCEPTS.md) | Section 2 |
| Trade Setups | [TRADING_CONCEPTS.md](concepts/TRADING_CONCEPTS.md) | Section 5 |
| Risk Management | [TRADING_CONCEPTS.md](concepts/TRADING_CONCEPTS.md) | Section 5.3 |

### Research
| Topic | Document | Section |
|-------|----------|---------|
| Market Maker Behavior | [GEX_MARKET_MECHANICS.md](research/GEX_MARKET_MECHANICS.md) | Section 1 |
| Regime Detection Algorithm | [GEX_MARKET_MECHANICS.md](research/GEX_MARKET_MECHANICS.md) | Section 2 |
| Wall Behavior Studies | [GEX_MARKET_MECHANICS.md](research/GEX_MARKET_MECHANICS.md) | Section 3 |
| 0DTE Dynamics | [GEX_MARKET_MECHANICS.md](research/GEX_MARKET_MECHANICS.md) | Section 4 |

---

## 📊 Key Formulas Reference

### Black-Scholes Call Price
```
C = S·N(d₁) - K·e^(-rT)·N(d₂)

d₁ = [ln(S/K) + (r + σ²/2)T] / (σ√T)
d₂ = d₁ - σ√T
```

### Greeks
```
Delta (Δ) = N(d₁)                      [for calls]
Gamma (Γ) = N'(d₁) / (S·σ·√T)
Theta (Θ) = -S·N'(d₁)·σ/(2√T) - r·K·e^(-rT)·N(d₂)
Vega  (ν) = S·√T·N'(d₁)
```

### Gamma Exposure (GEX)
```
GEX = Γ × Open Interest × Contract Multiplier × Spot²

Sign Convention:
  Calls → Positive GEX (Resistance)
  Puts  → Negative GEX (Support)
```

---

## 🔬 Research Methodology

All research in this documentation follows these principles:

1. **Empirical Validation** - Claims backed by historical data analysis
2. **Code Examples** - Conceptual implementations (not production code)
3. **Probability-Based** - No absolutes, only probabilities
4. **Risk Awareness** - Every strategy includes risk considerations

### Research Disclaimer

The code examples in research documents are:
- **Conceptual** - For understanding, not production use
- **Simplified** - Edge cases may not be fully handled
- **Educational** - Meant to explain concepts, not provide trading systems

---

## 📈 Document Statistics

| Document | Lines | Topics Covered | Code Examples |
|----------|-------|----------------|---------------|
| OPTIONS_MATHEMATICS.md | ~800 | 8 major sections | Formulas only |
| GREEKS_IMPLEMENTATION_RESEARCH.md | ~700 | 6 research areas | 15+ examples |
| GEX_MARKET_MECHANICS.md | ~700 | 6 research areas | 12+ examples |
| TRADING_CONCEPTS.md | ~500 | 7 sections | Practical examples |

**Total:** ~2,700 lines of documentation

---

## 🎓 Learning Path

### Beginner (1-2 hours)
1. Read [Trading Concepts](concepts/TRADING_CONCEPTS.md) - Sections 1-3
2. Understand what GEX is and why it matters
3. Learn to identify regimes

### Intermediate (3-4 hours)
1. Complete [Trading Concepts](concepts/TRADING_CONCEPTS.md)
2. Read [GEX Market Mechanics](research/GEX_MARKET_MECHANICS.md) - Sections 1-2
3. Understand market maker hedging mechanics

### Advanced (6+ hours)
1. Study [Options Mathematics](math/OPTIONS_MATHEMATICS.md) fully
2. Read [Greeks Implementation Research](research/GREEKS_IMPLEMENTATION_RESEARCH.md)
3. Complete [GEX Market Mechanics](research/GEX_MARKET_MECHANICS.md)
4. Understand algorithms and implementation details

---

## 🔗 External References

### Academic Papers
- Black, F., & Scholes, M. (1973). "The Pricing of Options"
- Brenner, M., & Subrahmanyam, M. (1988). "A Simple Formula for IV"
- Corrado, C., & Miller, T. (1996). "IV Approximation"

### Books
- Hull, J.C. "Options, Futures, and Other Derivatives"
- Natenberg, S. "Option Volatility and Pricing"
- Taleb, N.N. "Dynamic Hedging"

### Industry Research
- SqueezeMetrics (GEX methodology)
- SpotGamma (Market analysis)

---

## 📝 Contributing

This documentation is maintained as part of the GEX Analytics project. For questions or suggestions, please refer to the main project README.

---

**Last Updated:** December 2025
**Author:** Karan Chavan
**Purpose:** Educational & Portfolio Documentation
