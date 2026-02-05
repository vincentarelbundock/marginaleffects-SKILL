# Uncertainty

Catalogues four approaches for quantifying uncertainty around transformed quantities (predictions, comparisons, slopes, hypothesis contrasts) and shows how to implement each with marginaleffects.

1. **Delta method**
   - Derives first-order Taylor expansions for univariate (`Var[log(\hat β_2)] ≈ (1/β_2^2) Var(\hat β_2)`) and multivariate cases (`Var[h(β)] = J^T Var(β) J`). Provides worked examples using linear models and log transformations. Demonstrates computing the same result automatically via `hypotheses(mod, "log(b2) = 0")`.
   - Explains when to prefer robust or clustered vcovs by passing `vcov = "HC3"` or `vcov = cluster(id)` to marginaleffects functions.

2. **Bootstrap**
   - Discusses resampling observations, pairs bootstrap, Bayesian bootstrap, and when to rely on them (non-smooth estimands, small samples). Shows how to wrap marginaleffects calls inside bootstrap loops or use `infer = list(method = "bootstrap", draws = 500)` for built-in support. Highlights cost trade-offs vs. delta method.

3. **Simulation-based inference**
   - Covers drawing from the asymptotic distribution of coefficients (multivariate normal with `vcov`) or from Bayesian posteriors. Example: simulate thousands of coefficient draws, compute predictions/comparisons for each, then summarize quantiles. Connects to `marginaleffects::posterior_draws()` for Bayesian models and to `infer = list(method = "simulation", draws = 1000)` for frequentist models.

4. **Conformal prediction**
   - Introduces conformal prediction as a distribution-free way to obtain prediction intervals that maintain coverage under exchangeability. Outlines the conformal algorithm (split data, compute conformity scores, form quantile-based bands) and preview where the chapter later implements it for out-of-sample prediction tasks. Notes integration with tidymodels pipelines.

The chapter ties these approaches back to the workflow: choose a quantity, specify `vcov`/`infer` parameters when calling `predictions()`/`comparisons()`/`slopes()`, inspect `conf.low`/`conf.high`, and use `hypotheses()` to turn uncertainty estimates into tests. It also flags domain-specific considerations (e.g., heteroskedasticity, clustered sampling, simulation error) when selecting an inference method.
