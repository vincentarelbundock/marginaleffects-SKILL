# Counterfactual comparisons

This chapter develops the second core estimand class: contrasts between predictions made under different predictor values. Counterfactual comparisons generalize risk differences, risk ratios, odds ratios, percentage lifts, and multi-way contrasts. They power causal reasoning (ATE/ATT/CATE) but are equally useful for descriptive “what if” stories. The chapter treats comparisons as functions `C = f(Φ(X=b, Z; β), Φ(X=a, Z; β))` and shows how to compute, aggregate, and test them across models.

## 1. Quantity: specify the contrast function and focal shift
- **Functional forms**: Differences (`Φ_b - Φ_a`), ratios (`Φ_b / Φ_a`), log ratios (`log Φ_b - log Φ_a`), lift (`(Φ_b - Φ_a)/Φ_a`), odds ratios, or user-supplied functions passed via the `transform` argument. The chapter explains when each summary is interpretable (probabilities for risk differences/ratios, positive outcomes for lift) and ties them to domain conventions (e.g., epidemiology’s risk ratio vs. marketing’s lift).
- **Focal predictor definition**: For categorical/binary predictors, define discrete jumps (0→1, category A→B). For continuous predictors, define deltas via `variables = list(distance = 1)` or `list(age = 5)` to represent one-unit or multi-unit changes. For multi-dimensional interventions look at cross comparisons: `variables = c("Ta","Tb")` with `cross = TRUE` computes `Φ(Ta=1, Tb=1) - Φ(Ta=0, Tb=0)`.
- **Multiple functions simultaneously**: The text demonstrates `comparisons(..., transform = c("difference","ratio"))` to output several contrast types in one call, or using custom functions to compute more exotic estimands (e.g., percent change scaled by baseline variance).

## 2. Predictors (Grid): how to construct counterfactual scenarios
- **Row duplication strategy**: For unit-level contrasts, duplicate each observation once per treatment condition and set the focal predictor to the desired values, yielding two copies per unit (`newdata = datagrid(newdata = original, grid_type = "counterfactual", incentive = 0:1)`). This ensures other covariates remain at observed values.
- **Targeted grids**: When you want to evaluate contrasts at specific personas or evenly spaced covariate combinations, build custom grids via `datagrid()`. Example: `datagrid(incentive = c(0,1), agecat = "18 to 35", distance = seq(0,5,1))` to examine treatment effects across distances for a single age group.
- **Conditional subsets**: Use `newdata = subset(agecat == "18 to 35")` (R) or Polars filters to restrict the grid to certain subgroups before computing comparisons. This is essential for CATE or ATT/ATU estimation.
- **Complex scenarios**: The chapter highlights factorial designs where both predictors shift, requiring cross contrasts. It also covers “what-if” policies (e.g., raising minimum age from 18 to 21) by constructing `newdata` where a column is manually edited before calling `comparisons()`.

## 3. Aggregation: from unit-level contrasts to average effects
- **`comparisons()` vs. `avg_comparisons()`**: The former returns one contrast per row in `newdata`, the latter averages over rows, optionally grouped by `by=`. Use unit-level results for distributional plots, leverage averages for policy statements.
- **ATE, ATT, ATU**: Show how to compute these by filtering or weighting the aggregation step: full sample for ATE; treated subset for ATT (`newdata = subset(D == 1)` or `weights = D`); untreated subset for ATU. Weighted averages support survey weights or poststratification (MRP) contexts.
- **CATE**: supply `by = "state"`, `by = c("gender","agecat")`, etc., to estimate heterogeneity. The chapter discusses diagnosing effect modification by plotting `avg_comparisons()` outputs and using `hypotheses()` to contrast them.
- **Quantile/Custom summaries**: Use the `hypothesis` argument to compute quantiles of unit-level contrasts (e.g., 10th/50th/90th percentile effect) or to collapse categories (e.g., average difference for `affairs > 0`).

## 4. Uncertainty: inference for transformed contrasts
- **Delta method**: Works for smooth functions of β (differences, ratios). The chapter shows how marginaleffects automatically derives SEs via Jacobians and how you can inspect them via `std.error`/`conf.low`. It explains why delta-based SEs may break for ratios when denominators are near zero and suggests log transforms or bootstrap as alternatives.
- **Robust/clustered vcov**: Use `vcov = "HC3"` or `vcov = cluster(village)` to handle heteroskedasticity or clustered assignment (e.g., cluster-level treatments). The text emphasizes that specifying `vcov` once propagates to comparisons’ SEs.
- **Bootstrap & simulation**: For nonlinear contrasts or small samples, wrap `avg_comparisons()` in bootstrap loops or set `infer = list(method="bootstrap", draws=1000)`. Simulation draws from the multivariate normal approximation are especially useful when generating contrasts for many hypothetical grids.
- **Bayesian draws**: When using `brms` or other Bayesian models, extract posterior draws and compute contrasts for each draw to obtain credible intervals. marginaleffects can operate directly on `brmsfit` objects, returning draws via `posterior_draws()`.

## 5. Hypothesis testing and equivalence
- **Testing zero effects**: `hypotheses(avg_comparisons_obj, "estimate = 0")` tests whether the average contrast differs from zero. Use this for treatment effects, difference-in-differences, or factorial cross effects.
- **Comparing subgroups**: After computing `avg_comparisons(by = "agecat")`, run constraints like `term:">35" - term:"18 to 35" = 0` to test heterogeneity formally.
- **Equivalence tests**: Use `hypotheses(..., equivalence = c(-0.02, 0.02))` to argue that an effect is practically negligible (e.g., marketing lift within ±2 percentage points). The chapter includes business and epidemiology examples to illustrate when equivalence metrics matter.
- **Multiple contrasts**: Provide matrices/vectors of constraints to test several effects simultaneously, e.g., all treatment effects across states equal a policy target.

## 6. Visualization and communication
- **`plot_comparisons()`**: Accepts `variables` and `by` just like `avg_comparisons()`, producing coefficient-style plots with confidence intervals. Use for average treatment effects by subgroup, factorial interactions, or marginal effect curves over a continuous variable.
- **Effect surfaces**: For numeric moderators, pipe unit-level contrasts into `ggplot`/`plotnine` heatmaps (e.g., effect of incentive by distance and age). The chapter shows how to combine `comparisons()` output with tile plots for rapid heterogeneity exploration.
- **Distribution of effects**: Plot histograms/density of unit-level contrasts to show variation across individuals, especially when average effects mask heterogeneity.

## 7. Relationship to causal estimands
- **ATE/ATT/ATU**: The chapter links the functions to G-computation and potential outcomes notation, explaining how `avg_comparisons()` equals g-formula estimators under conditional exchangeability. It emphasizes that the same code works for descriptive contrasts; the causal interpretation hinges on design assumptions.
- **Difference-in-differences**: Show how to compute DiD estimands by treating the comparison as `(post - pre)` differences across treatment groups (either via data transformation or using `variables = c("time","treatment"), cross = TRUE`).
- **Heterogeneous treatment effects**: Encourage storing `comparisons()` output with covariates and fitting secondary models (meta-learners) to describe heterogeneity.

## 8. Implementation notes (R & Python)
- Provide full Thornton example in both languages: `avg_comparisons(mod, variables = "incentive", vcov = "HC2")` vs. Python `avg_comparisons(mod, variables="incentive", vcov="HC2")`. Explain how to specify `newdata` using base R `subset()` or Polars filters, and how to interpret `term`/`contrast` columns.
- For factorial designs, show `avg_comparisons(mod, variables = c("Ta","Tb"), cross = TRUE)` and interpret sums of coefficients. Equivalent Python code uses statsmodels OLS and `marginaleffects` API.

By the end of the chapter, readers can (1) articulate the precise counterfactual they are evaluating, (2) build the correct grid, (3) compute both unit-level and aggregated contrasts, (4) quantify uncertainty with methods aligned to their estimand, (5) test hypotheses/equivalence statements, and (6) visualize heterogeneity in ways stakeholders immediately understand. Counterfactual comparisons become the default reporting unit for causal and descriptive “what if” claims.
