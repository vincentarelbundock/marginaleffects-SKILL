# Categorical and ordinal outcomes

Explains how to interpret models with discrete outcomes (multinomial logit, ordered probit). Using the Fair (1978) extramarital affairs dataset, the chapter fits an ordered probit with `polr(affairs ~ children + yearsmarried + gender, method="probit")` and shows how the latent-variable coefficients translate into category-specific probabilities.

Key components:
- **Predictions**: `predictions()` returns one probability per outcome level for any covariate profile. The chapter computes the distribution for a woman with children married 10 years, then uses `avg_predictions(mod)` to obtain overall probabilities and `by="children"` to compare those with/without children. It explains how the `group` column indexes outcome levels and how to interpret the resulting probabilities.
- **Aggregation & custom summaries**: demonstrates collapsing categories by supplying a `hypothesis` function to `avg_predictions()`—e.g., sum probabilities for `affairs > 0` vs. exactly zero using base R `aggregate()` or tidyverse pipelines.
- **Counterfactual comparisons**: `avg_comparisons(mod, variables = list(yearsmarried = 5))` quantifies how increasing marriage length by 5 years shifts each outcome probability (e.g., -k percentage points for zero affairs, +k for >10). It also evaluates binary predictors like `gender`, showing small, insignificant differences.
- **Uncertainty**: standard errors rely on the delta method and reflect multinomial correlations; results report confidence intervals/p-values for each category-specific effect.

Visualization tips include `plot_predictions(mod, by = c("group","children"))` to show probability bars by category and covariate, reinforcing interpretation of complex models. The chapter underscores that, despite multiple cutpoints/coefficients, analysts should focus on transformed quantities (probabilities, contrasts) using marginaleffects to avoid latent-scale ambiguity.
