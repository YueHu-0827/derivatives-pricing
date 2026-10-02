# Derivatives Pricing & Delta-Hedging Simulation

A self-contained study comparing three option pricing methods (Black-Scholes,
CRR binomial tree, Monte Carlo), validating Greeks via finite differences,
and simulating delta-hedging under discrete rebalancing and transaction costs.

## Key Findings

1. **Convergence rates match theory.** Empirical convergence order for the
   binomial tree is -1.02 (theoretical -1), and for Monte Carlo -0.52
   (theoretical -0.5), confirmed via log-log regression over a range of
   step counts / path counts.
   ![Convergence comparison](figures/convergence.png)

2. **Optimal finite-difference step size exists.** Delta error vs. step size
   h shows a clear V-shape: truncation error dominates for large h, floating-point
   round-off error dominates for small h, with the optimal h around 1e-4 to 1e-5.
   ![Step size sensitivity](figures/fd_stepsize.png)

3. **Second-order Greeks are far more sensitive to numerical noise than
   first-order ones.** Naive finite-difference Gamma on the binomial tree
   can be off by three orders of magnitude near-the-money, due to interaction
   between the payoff kink and tree discretization. An adaptive step size
   (scaled to tree node spacing) brings it back within ~10% of the analytic value.

4. **Discrete hedging error decays as O(1/√n)** with rebalancing frequency,
   matching theory (empirical slope -0.48). The hedging P&L distribution is
   left-skewed, reflecting asymmetric loss from unhedged upside gamma exposure.

5. **Transaction costs create an optimal rebalancing frequency.** Without
   costs, more frequent hedging is strictly better. With proportional
   transaction costs, risk-adjusted P&L (mean − k·std) is hump-shaped, and
   the optimal frequency shifts lower as the cost rate increases.
   ![Optimal hedge frequency](figures/optimal_hedge_freq_multi.png)

6. **Volatility mis-specification produces systematic P&L**, not just noise.
   Hedging at a pricing vol below realized vol leads to systematic losses;
   hedging above realized vol leads to systematic gains — consistent with
   the standard result that delta-hedging removes directional risk but not
   volatility risk.

## Repository Structure

derivatives-pricing/
├── Option Pricing.ipynb # All code and experiments
├── figures/ # Saved plots
├── requirements.txt
└── README.md

## Methods Implemented
- Black-Scholes analytic pricing and Greeks (call/put)
- CRR binomial tree (European and American exercise)
- Monte Carlo with antithetic variates
- Central finite-difference Greeks (generic, works with any pricer)
- Discrete delta-hedging simulation with proportional transaction costs

## How to Run
```bash
pip install -r requirements.txt
jupyter notebook Option Pricing.ipynb
```

## Notes on Scope
This project uses synthetic parameters, not historical market data — the
goal is to isolate and study the numerical behavior of the pricing and
hedging methods themselves, not to backtest a trading strategy.