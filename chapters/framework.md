# Conceptual framework

This chapter is the spine of *Model to Meaning*. It argues that every interpretation task can be decomposed into five disciplined questions—Quantity, Predictors (Grid), Aggregation, Uncertainty, and Test—and it shows how these questions map to concrete marginaleffects functions. The framework deliberately separates what you want to learn (estimand) from how you learn it (estimator) so analysts stop defaulting to “look at coefficient tables” and instead design outputs that align with stakeholder questions.

## Quantity: define the estimand before touching code
- **Predictions**: expectations of a fitted model for covariate profile `(x, z)` defined as `Φ(x, z; β)`. The chapter stresses choosing the correct scale (response vs. link) and references GLM inverse-link functions (logistic `g`, log link, identity). `predictions(type = "link")` vs `type = "response"` is the practical knob.
- **Comparisons**: functions of two or more predictions such as risk differences `Φ(x=b) - Φ(x=a)`, ratios, log-odds contrasts, or composite estimands like ATE `E[Φ(D=1,Z) - Φ(D=0,Z)]`. Expressing the functional form up front clarifies what `comparisons()` or `avg_comparisons()` should compute and whether multiple predictors change simultaneously (`cross=TRUE`).
- **Slopes**: partial derivatives `∂Φ/∂x` describing instantaneous rates of change. The framework shows how slopes relate to derivatives of linear predictors (often just coefficients) and warns that in nonlinear models slopes depend on the evaluation point.
- **Tests**: The fifth question (Test) loops back to Quantity because you must specify whether you are testing `H0: Φ = c`, `H0: Φ1 - Φ2 = 0`, or equivalence intervals. `hypotheses()` implements arbitrary `h(β)` constraints and is most useful when the estimand has already been declared.

## Predictors (Grid): decide where the estimand is evaluated
- The chapter distinguishes between *observed* grids (using the original data so estimates describe the sample) and *counterfactual* or *balanced* grids constructed with `datagrid()`. Examples include building a grid with `datagrid(incentive = 0:1, agecat = c("<18","18 to 35"," >35"))` or generating counterfactual draws by duplicating rows and flipping treatment indicators.
- Guidance includes: replicate the data once per treatment to compute individual-level contrasts, use `grid_type = "balanced"` to average over identical covariate distributions, or feed custom data frames when analyzing hypothetical policies. The chapter underscores that unclear grids change estimands (ATE over sample vs. over target population) and encourages explicitly documenting the grid in prose.

## Aggregation: link unit-level quantities to audiences
- Aggregation choices answer “whose effect?” or “whose prediction?” The chapter defines: unit-level (no aggregation), group means (`by = c("state","gender")`), weighted means (`weights = population_weights`), and summary functions like quantiles (via `hypothesis` callbacks). It demonstrates Simpson’s paradox: averaging before or after filtering can reverse conclusions, so specify aggregation in the estimand statement.
- For predictions: `avg_predictions()` yields marginal means overall or by subgroup; for comparisons/slopes the same logic uses `avg_comparisons()` / `avg_slopes()`. The chapter provides recipes for ATT/CATE by filtering or weighting the aggregation stage.

## Uncertainty: choose an inferential engine
- Lists default delta method (using model vcov), robust adjustments via `vcov = "HC3"`, cluster corrections, bootstrap (`infer = list(method="bootstrap", draws=500)`), simulation (`infer = list(method="simulation", draws=1000)`), and conformal prediction for interval forecasts. Decision guides: delta is fast but relies on smoothness/asymptotics; bootstrap covers nonlinear, non-smooth estimands; simulation is useful for Bayesian models or when drawing from multivariate normal approximations; conformal is for predictive coverage guarantees.
- The framework encourages thinking about uncertainty at the design stage: if you plan to test equivalence, ensure the estimation path provides standard errors or draws for the relevant quantity. It also notes when to propagate poststratification uncertainty via replicate weights or hierarchical draws.

## Test: make hypotheses explicit
- Explains that every reported number should be accompanied by a test or an interval tied to the estimand. Examples: `hypotheses(predictions_object, "estimate = 0.5")` to test a target rate; `hypotheses(comparisons_object, c("estimate = 0", "estimate = 0.05"), equivalence = TRUE)` to run TOST; pairwise contrasts like `hypotheses(comparisons_object, "term:older - term:younger = 0")`. The chapter shows how to run simultaneous tests by supplying matrices of constraints, and how to interpret `p.value` vs. `conf.low`/`conf.high` with respect to practical significance.

## Putting it together
The chapter ends with a canonical workflow for any new analysis:
1. Write the estimand in mathematics or plain language (Quantity).
2. Decide where to evaluate it (Predictors/Grid) and construct the relevant data via `datagrid()` or direct transformation.
3. Choose whether to average and over whom (Aggregation), being explicit about weights or by-groups.
4. Select an uncertainty strategy consistent with the model and estimand (Uncertainty) and configure `vcov`/`infer` parameters before running marginaleffects.
5. Define the hypothesis/equivalence test or interval to report (Test) and run `hypotheses()` accordingly.
The framework becomes the checklist you apply when reading the rest of the book or when building skills: every chapter answers these five questions with concrete marginaleffects calls.
