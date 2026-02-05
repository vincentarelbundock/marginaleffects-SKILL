# Multilevel regression with poststratification

Walks through estimating public opinion by combining hierarchical models with poststratification weights. The case study measures support for cutting police budgets by state using CES survey data.

1. **Multilevel modeling**
   - Frequentist example: fit `glmmTMB(defund ~ age + education + military + gender + (1 + gender | state), family = binomial)` to allow state-level random intercepts and gender slopes. Use `predictions()` to compare California vs. Alabama respondents with the same demographic profile. `avg_comparisons(mod, variables = "age")` reports how probability shifts across age bins, highlighting shrinkage behavior.
   - Bayesian example: replicate the model in `brms` with priors (`prior(normal(0,1e6), class="b")` vs. informative `normal(0,0.2)`) and run prior predictive checks via `sample_prior = "only"`. Use `avg_predictions()` on prior-only draws to inspect whether implied probabilities are plausible before fitting to data. After fitting, draw posterior predictions/comparisons and summarize with credible intervals.

2. **Poststratification**
   - Construct a poststrat grid representing population cells (state × gender × race × age × education) and compute predictions for each cell with the fitted model.
   - Weight each cell by its population frequency and aggregate to produce state-level estimates (`avg_predictions(mod, by = "state", weights = population_weights)`). This corrects for sample imbalance and yields MRP estimates.

3. **Uncertainty**
   - Frequentist results include uncertainty from fixed effects but not random-effects variance; the chapter flags this limitation and suggests Bayesian modeling or simulation to propagate hierarchical uncertainty.
   - Demonstrates reading posterior draws (`posterior_draws()`), summarizing them with `tt()` tables, and mapping credible intervals across states.

The chapter concludes with practical guidance: align survey covariates with available poststratification data, beware empty cells (violating positivity), and validate predictions against external benchmarks. Functions to remember: `glmmTMB`, `brm`, `predictions()`, `avg_predictions()` with `newdata=`/`weights=`, and `marginaleffects` support for `brmsfit` objects.
