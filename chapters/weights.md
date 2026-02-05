# Weights

`chapters/weights.qmd` currently contains only section headers—Sampling, Matching, Inverse Probability Weighting—so no prose is available yet. The intent is to document how sampling weights, design-based matching weights, and IPW schemes integrate with the marginaleffects API. Until the chapter is written, use the following guideposts:
- **Sampling weights**: most `avg_*` functions accept a `weights=` argument so you can reproduce survey-weighted estimands or population-weighted averages after prediction/comparison steps.
- **Matching weights**: when using matched datasets, include the matching weights in `weights=` or incorporate them directly in the modeling stage; marginaleffects will propagate them through averages.
- **Inverse Probability Weighting**: pair treatment-propensity models with the `weights=` argument to reweight unit-level predictions/comparisons, or combine with G-computation for doubly-robust estimators.

Once the chapter is expanded, update this file with concrete examples and code references. For now, treat it as a placeholder signaling that weighting strategies belong in the workflow.
