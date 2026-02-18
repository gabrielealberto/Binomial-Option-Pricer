# Binomial Option Pricer (CRR)

Vectorized implementation of the **Cox-Ross-Rubinstein (CRR)** binomial model for pricing European and American options using real market data.

The project focuses on **algorithmic efficiency**, replacing the traditional full-tree construction with a memory-optimized approach that stores only the nodes required during backward induction.

Originally implemented with nested loops and a full price matrix, the pricer was later redesigned using vectorized operations, reducing memory complexity from **O(n²) to O(n)** and significantly improving performance.

---

## Overview

This pricer:

- Prices **European and American calls and puts**
- Uses **real market data** via `yfinance`
- Estimates **historical volatility**
- Includes a **continuous dividend yield**
- Verifies **put–call parity** for European options
- Avoids constructing the full binomial tree

The model has been tested on **equity data** and is currently intended for stocks rather than commodities, which typically require additional modeling assumptions such as storage costs or convenience yields.

---

## Model

The binomial framework assumes that over a short time interval Δt, the asset price moves either up or down:

u = e^(σ√Δt)
d = 1/u


Risk-neutral probability:

p = (e^{(r-q)Δt} - d) / (u - d)


Option values are computed through backward induction:

V = e^{-rΔt} (pV_up + (1-p)V_down)


American options are handled by comparing the continuation value with immediate exercise at each node.

---

## Data Pipeline

Market data is downloaded twice:

- **Adjusted prices** → used for volatility estimation  
- **Non-adjusted prices** → used for the current spot price  

Adjusted prices incorporate corporate actions such as splits and dividends, producing more consistent return series, while the non-adjusted close reflects the actual traded market price.

---

## Algorithmic Design

Traditional binomial implementations construct a full `(n+1) × (n+1)` tree.

This project avoids that by:

- computing only terminal nodes  
- shrinking the value vector during backward induction  
- generating intermediate prices on demand (for American exercise)  

Result:

Memory: O(n²) → O(n)


This makes the pricer scalable and significantly faster for large step counts.

---

## User Parameters

| Variable | Description |
|--------|-------------|
| `ticker` | Underlying asset |
| `s0` | Spot price |
| `k` | Strike price |
| `n` | Number of binomial steps |
| `t` | Time to maturity (days) |
| `r` | Risk-free rate |
| `q` | Dividend yield |
| `opttype` | Call (`c`) or Put (`p`) |
| `option_style` | European (`eu`) or American (`am`) |

These inputs allow the model to adapt to different pricing scenarios.

---

## Example Output

Option Style: European
Actual underlying price: 263.88 USD
Dividend Yield used: 0.39%
Option type: Call
Strike price: 271.7964 USD
Binomial Call option price: 19.1419 USD
Call-put parity verified


For European options, the parity check acts as a numerical consistency test for the implementation.

---

## Installation

Install the required dependencies:

```bash
pip install numpy pandas yfinance
```
Usage
Run the pricer from the command line:

python Vectorized_pricer.py
Model parameters (ticker, strike, maturity, etc.) can be modified directly at the top of the script to test different pricing scenarios.

Model Scope
The pricer has been tested on equity data and is currently designed for stocks.
Extending the framework to commodities would require additional modeling assumptions, such as storage costs or convenience yields.

Key Assumptions
Dividends are modeled as a continuous yield

Volatility is estimated from historical returns

The model follows the standard CRR recombining tree structure

These assumptions keep the implementation tractable while remaining consistent with common practice in binomial pricing.

```bash
Project Structure
.
├── Binomial_options_pricer.py   # Main pricing script
├── Report.pdf
└── README.md
```
Author
Gabriele Alberto
