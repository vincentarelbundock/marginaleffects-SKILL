# Slopes

Defines slopes (marginal effects) as partial derivatives of the outcome with respect to a focal predictor while holding other covariates fixed: `∂Φ(X,z;β)/∂X`. Highlights that in linear models the slope equals the coefficient, whereas in nonlinear models (logit/probit, interactions, polynomials) it varies by observation. For categorical predictors, the implementation falls back to finite differences equivalent to counterfactual comparisons.

Key insights:
- Derivative intuition: graphical examples show how derivatives reveal whether a function is increasing/decreasing/flat. Figures connect linear functions (constant slope), quadratic polynomials (sign switches), and logistic S-curves (largest slope near the mean).
- Computation: `slopes()` produces unit-level derivatives; `avg_slopes()` averages them to report marginal effects at the mean or across the sample. `variables=` selects focal predictors; `by=` slices results by subgroup. Numeric deltas can be customized with `wrt = list(X = 5)` to evaluate 5-unit marginal changes.
- Grids & aggregation: the same `datagrid()` and `newdata=` patterns let you compute slopes at specific covariate profiles (e.g., slope of incentive effect among 18–35-year-olds at distance 2), or average slopes by moderator values.
- Uncertainty: delta method by default, with options for robust/clustered vcovs or bootstrap when derivatives depend on complex transformations. Simulation from the asymptotic distribution of `β` can approximate slope distributions for non-smooth models.
- Testing: `hypotheses()` lets you test if a slope equals zero or compare slopes across groups (e.g., difference between male and female slopes of `distance`), enabling statements like "the marginal effect of distance is -3 percentage points per kilometer (p<0.01)."

Visualization: `plot_slopes()` (or `plot_predictions` with `dydx=` aesthetics) renders derivative curves across the predictor space (partial dependence style) and can overlay multiple moderators (e.g., slopes of incentive by age category). The chapter underscores the difference between instantaneous slopes and finite differences, and when each interpretation is preferable.
