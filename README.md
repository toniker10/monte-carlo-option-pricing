# Monte Carlo Option Pricing: A Performance Benchmark

How fast can you price an option? This project benchmarks four different ways of
pricing a European call option with Monte Carlo simulation — **Pure Python**,
**vectorized NumPy**, **Numba (JIT & parallel)**, and **Cython** — and validates
every result against the closed-form **Black-Scholes** price.

## Overview

Monte Carlo simulation is a standard technique in quantitative finance for pricing
derivatives, but its performance depends heavily on *how* it's implemented. This
notebook takes the same option-pricing logic and implements it five times, using
progressively lower-level tools, then measures:

- **Execution speed** across implementations
- **Numerical accuracy** vs. the analytical Black-Scholes formula
- **Latency scaling** as the number of simulations grows

## Methods Compared

| Method | Description |
|---|---|
| **Pure Python** | A plain `for` loop over simulated paths — the naive baseline. |
| **NumPy Vectorized** | Fully vectorized array operations, no explicit loops. |
| **Numba JIT** | The pure-Python loop compiled just-in-time with `@jit(nopython=True)`. |
| **Numba Parallel** | Same as above, using `prange` to parallelize across cores. |
| **Cython** | Statically-typed C-level loop compiled via `%%cython`, using a custom Box-Muller normal sampler. |

Each method prices a European call option:

```
S0 = 100.0   # Spot price
K  = 100.0   # Strike price
T  = 1.0     # Time to maturity (years)
r  = 0.05    # Risk-free rate
sigma = 0.20 # Volatility
```

## Validation

Every Monte Carlo estimate is checked against the analytical Black-Scholes price,
with a 95% confidence interval computed from the simulation's standard error. The
notebook asserts that **all methods converge within tolerance** before reporting
any performance numbers — correctness first, speed second.

## Results

The notebook produces:
1. A **bar chart** of median execution time per method (log scale), annotated with
   simulation counts.
2. A **line chart** showing how latency scales with the number of simulations
   (10K → 1M) for NumPy, Numba JIT, Numba Parallel, and Cython.

Typical takeaway: Pure Python is by far the slowest and is only run at a reduced
simulation count (100K vs. 1M for the rest) to keep runtime reasonable; NumPy gives
a large speedup for free; Numba and Cython push performance further, with Numba
Parallel and Cython typically the fastest for large simulation counts.

*(Run the notebook to reproduce the exact numbers on your own machine — timings are
hardware-dependent.)*

## Requirements

```
numpy
scipy
pandas
matplotlib
numba
cython
jupyter
```

Install with:

```bash
pip install -r requirements.txt
```

## Running the Notebook

```bash
git clone https://github.com/toniker10/monte-carlo-option-pricing.git
cd monte-carlo-option-pricing
pip install -r requirements.txt
jupyter notebook
```

Open `Monte_Carlo_Option_Pricing_A_Performance_Benchmark_Across_Pure_Python__NumPy__Numba__and_Cython.ipynb`
and run all cells. The Cython cell uses the `%%cython` magic (via `%load_ext Cython`),
so a working C compiler is required on your system.


