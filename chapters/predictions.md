# Predictions

This chapter operationalizes the first pillar of the framework: computing model-based predictions that translate parameters into intuitive expectations. A prediction is defined as the expected outcome evaluated at a specific covariate profile `x` with parameter estimates `β`: `\hat Y = Φ(x; \hat β)`. The chapter develops this definition across generalized linear models, complex grids, aggregation schemes, uncertainty estimators, and visualization strategies. Throughout, it uses Thornton’s (2008) HIV incentive experiment to provide concrete R and Python code.

## 1. Quantity: what is being predicted?
- **Response vs. link scale**: In GLMs the linear predictor `η = Xβ` is mapped to the response via an inverse link `g^{-1}`. For a logit model `Pr(O=1) = g(β0 + β1 incentive + ...)` with `g(z) = 1/(1+e^{-z})`. The chapter walks through computing linear predictors manually, emphasizing how `predictions(type="link")` returns `η` while `type="response"` applies the inverse link. Users are encouraged to report probabilities (response scale) unless the analysis specifically targets log-odds or log counts.
- **Invariant conversions**: Because of plug-in principle and MLE invariance, any smooth function of coefficients can produce valid predictions, including exponential transformations for Poisson log-links or logistic CDFs. The text contrasts naive coefficient inspection with evaluating `Φ` to produce numbers that audiences immediately interpret (e.g., “58% chance of returning to clinic”).
- **Custom transformations**: For exotic estimands, pass a `transform` function or manipulate the `predictions()` output manually; the chapter includes examples converting log predictions to dollar amounts or computing expected counts with offset terms.

## 2. Predictors (Grid): where are predictions evaluated?
- **Observed data**: Calling `predictions(model)` without `newdata` returns fitted values for each observation. This is useful for diagnostics but ties interpretations to the sample distribution.
- **Custom grids with `datagrid()`**: The chapter demonstrates `datagrid(incentive = 0:1, agecat = c("<18","18 to 35"," >35"), distance = c(0, 2, 5))` to create all combinations, explaining options `grid_type = "balanced"` (equal weight to each combination) vs. `"counterfactual"` (duplicate actual rows for each treatment). It also covers setting numeric sequences (`distance = seq(0, 5, by = 0.5)`), using functions like `unique`/`median`, and referencing factors by label.
- **Case-specific predictions**: For narrative clarity, the chapter recommends building data frames that describe personas (“a 35-year-old 2 km away with incentive=1”) and evaluating predictions there. Both R (`data.frame(...)`) and Python (Polars DataFrame, `.with_columns`) examples are provided.
- **Partial dependence grids**: For PDP-style plots, create grids where one predictor sweeps across a range while others stay at representative values. The text illustrates sampling 10,000 rows to build a counterfactual grid and warns about the computational cost of using the full dataset.

## 3. Aggregation: from individual predictions to margins
- **Unit-level vs. averages**: `predictions()` returns one row per observation/grid row. `avg_predictions()` averages across all rows, yielding marginal means. This is the default when you need average predicted probabilities, expected values, or population-level statements.
- **By-groups**: `avg_predictions(mod, by = "agecat")` calculates average predictions for each age group; multiple grouping variables are allowed (`by = c("agecat","distance_bin")`). The output’s `group` column enumerates the combinations so you can join back to metadata or plotting aesthetics.
- **Weights**: supply `weights = population_weights` to reflect survey or poststratification schemes. Example: `avg_predictions(mod, by = "state", newdata = poststrat_grid, weights = poststrat_grid$freq)` returns MRP-style predictions after weighting by census counts.
- **Custom summaries**: Use `hypothesis` callbacks to produce nonstandard summaries (e.g., sum probabilities of `group > 0`, compute quantiles). The chapter gives base R and tidyverse examples for collapsing categorical outcomes.

## 4. Uncertainty: delta, robust, bootstrap, simulation, conformal
- **Delta method (default)**: Leveraging the model’s vcov matrix, marginaleffects computes standard errors for predictions even when they’re nonlinear functions of `β`. The chapter shows how `conf.low`/`conf.high` are filled and how to change the vcov (`vcov = "HC2"`, `vcov = cluster(village)`).
- **Bootstrap**: For non-smooth models or small samples, wrap predictions in bootstrap loops or use `infer = list(method="bootstrap", draws=999)`. Discusses at-length how to interpret percentile vs. basic bootstrap intervals.
- **Simulation draws**: Set `infer = list(method="simulation", draws=1000)` to draw from the asymptotic multivariate normal of `β` and propagate through predictions. Especially useful for presenting posterior-like intervals or when stacking predictions into new functions.
- **Conformal prediction preview**: For forecasting/out-of-sample tasks, conformal prediction offers valid coverage without distributional assumptions. The chapter references the dedicated uncertainty chapter for full details but notes that predictions can be augmented with conformal bands using tidymodels integrations.

## 5. Hypothesis tests on predictions
- Illustrates comparing predictions against target benchmarks: `hypotheses(pred_obj, "estimate = 0.5")` tests if the predicted return rate equals 50%. For subgroup differences, compute predictions for each group and feed them into `hypotheses()` with constraints (e.g., `term:"18 to 35" - term:">35" = 0`). Equivalent code is shown in Python, using statsmodels objects and `marginaleffects` wrappers.

## 6. Visualization patterns
- **`plot_predictions()`**: automatically reshapes output for ggplot/plotnine. Examples show two-dimensional plots (estimate vs. distance with color for incentive) and faceted plots by age category. The chapter explains `condition=` for x-axis, `by=` for facets/lines, `draw = TRUE/FALSE`, and customizing geoms.
- **Partial dependence**: when using `newdata` grids, call `plot_predictions(mod, newdata = grid, condition = "distance", by = c("incentive"))` to display PDP curves, optionally with ribbons representing confidence intervals.
- **Distribution overlays**: overlay histograms/density of observed predictor values to illustrate where predictions are extrapolating (useful cautionary figure).

## 7. Implementation parity (R & Python)
- Each section mirrors R code with Python analogues: `statsmodels` formulas, `polars` for `newdata`, `plotnine` for plotting. The chapter emphasizes small syntax differences (e.g., `.to_pandas()` before statsmodels, `pl.lit` for constant columns) so practitioners can translate workflows seamlessly.

By the end of the chapter, readers can state precisely which quantity they are predicting, build the grid to evaluate it, choose aggregation/weights that match the estimand, quantify uncertainty appropriately, test hypotheses against practical benchmarks, and present visualizations that highlight interpretable scales. Predictions become the default lens through which to interpret fitted models rather than a byproduct of coefficient tables.
