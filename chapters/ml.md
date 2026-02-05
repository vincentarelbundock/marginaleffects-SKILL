# Machine learning

Demonstrates that marginaleffects can interpret models fitted with `tidymodels`, `mlr3`, or scikit-learn. The running example fits an XGBoost regressor to London Airbnb prices using a `tidymodels` workflow (`boost_tree(mode="regression", engine="xgboost")`). It outlines:

1. **Data prep**: split into training/testing via `initial_split`, create preprocessing pipelines with `recipe()` + `step_dummy(all_nominal_predictors())`, and fit the model inside a `workflow()`.
2. **Predictions**: call `predictions(mod, newdata = test)` to obtain fitted prices for the held-out set; visualize observed vs. predicted scatterplots to gauge calibration.
3. **Aggregation**: `avg_predictions(mod, by = "unit_type", newdata = test)` compares expected prices for private rooms vs. entire homes, showing model-level differences even when parameters aren’t directly interpretable.
4. **Partial dependence**: `plot_predictions(mod, by = c("bedrooms","unit_type"), newdata = airbnb)` traces how predicted price moves as bedrooms change. A counterfactual grid built via `datagrid(..., grid_type = "counterfactual")` ensures other covariates are balanced when computing curves.
5. **Counterfactual comparisons**: `avg_comparisons(mod, variables = list(bedrooms = 2), newdata = airbnb)` quantifies how adding two bedrooms changes price; cross contrasts with `variables = c("bedrooms","Wireless Internet"), cross=TRUE` evaluate joint interventions.

The chapter notes that many ML estimators lack closed-form vcov matrices, so standard errors are omitted unless resampling or conformal prediction is used. It recommends using built-in resampling tools (`vfold_cv`, `last_fit`) or conformal methods (see the uncertainty chapter) for interval estimates. The key takeaway: predictions/comparisons/slopes remain valid summarizers even when the underlying model is a boosted tree or random forest.
