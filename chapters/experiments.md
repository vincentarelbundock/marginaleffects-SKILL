# Experiments

Applies the marginaleffects workflow to randomized experiments. Two focal designs:

1. **Two-arm randomized controlled trial (Thornton HIV incentive)**
   - Start with the linear probability model `lm(outcome ~ incentive)` to estimate the ATE directly; `avg_comparisons(mod, variables = "incentive")` reproduces the coefficient and allows heteroskedasticity-robust SEs via `vcov = "HC2"`.
   - To improve precision, fit covariate-adjusted models with full interaction between treatment and covariates (`incentive * (age + distance + hiv2004)`), following Lin (2013). Rather than hand-interpreting a dozen coefficients, compute the adjusted ATE with `avg_comparisons()` and request robust SEs. This ensures the estimator remains unbiased under randomization while lowering variance.
   - Demonstrates how to compare adjusted vs. unadjusted estimates, interpret the percentage-point differences, and report both for transparency.

2. **2×2 factorial experiment (simulated `Ta`, `Tb`)**
   - Fit `lm(Y ~ Ta * Tb)` (or GLM equivalents). Use `plot_predictions(mod, by = c("Ta","Tb"))` to visualize cell means.
   - Compute simple contrasts: effect of toggling `Ta` when `Tb=0` via `avg_comparisons(mod, variables = "Ta", newdata = subset(Tb == 0))`.
   - Compute cross-contrasts (joint changes in both treatments) using `variables = c("Ta","Tb"), cross = TRUE`, which automatically sums main and interaction effects with correct SEs.

The chapter also covers practical testing advice: use `hypotheses()` for linear constraints (e.g., is the interaction zero?), cluster SEs if treatments are assigned at cluster level, and visualize interaction surfaces. Analysts can adapt these patterns to multi-arm trials, conjoint experiments, or regression-adjusted RCT analyses by swapping in the relevant `variables`/`newdata` arguments.
