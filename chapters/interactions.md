# Interactions and polynomials

Addresses heterogeneity (effects varying across contexts) and flexibility (non-linear relationships) via two modeling strategies: multiplicative interactions and polynomial terms. It emphasizes that the same prediction/comparison/slopes toolkit handles these richer specifications, so analysts should interpret the transformed quantities rather than raw coefficients.

## Multiplicative interactions
- **Categorical×categorical**: simulate `Y ~ X * M` with logit link, interpret via `avg_predictions(mod, by = c("X","M"))` and `avg_comparisons()` conditioned on each moderator level (using `newdata = subset(M == "a")`, etc.). Shows how to detect moderation by plotting triangles/circles for each `M` level.
- **Binary×continuous**: compute marginal effects of `X` across values of `M` using `avg_comparisons(mod, variables = "X", by = "M")` or derivative curves with `plot_slopes(mod, variables = "X", condition = "M")`.
- **Continuous×continuous**: express the slope analytically (`β2 + β4*M`) and reproduce it via `slopes()` evaluated over a grid of `M`. Encourages reporting effect heterogeneity with tables/plots rather than quoting single coefficients.

## Polynomial regression
Shows how adding polynomial terms (e.g., `poly(age, degree=2)` or manual squares) captures curvature. Demonstrates building grids over the focal predictor (`datagrid(age = seq(min,max,length=100))`), computing predictions and slopes to interpret turning points, and using `hypotheses()` to test whether curvature terms jointly equal zero. Partial dependence plots illustrate combined effects (e.g., predicted outcome vs. age with shading for uncertainty).

Throughout, the chapter stresses: always include constitutive terms with interactions, beware post-treatment controls, and rely on marginaleffects functions to summarize heterogeneity (average marginal effects, group-specific contrasts, derivative plots). Functions highlighted: `avg_predictions()`, `avg_comparisons()`, `slopes()`, `plot_predictions()`, `plot_comparisons()`, `plot_slopes()`, plus `variables=`/`by=`/`condition=` arguments for targeting moderators.
