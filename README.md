# Irish Home Prices — Optuna + MLflow

A beginner-friendly machine learning notebook that predicts Irish residential property prices using the [Property Price Register](https://www.propertypriceregister.ie/) dataset.

## What This Notebook Covers

1. **Data loading & cleaning** — downloads the 2021 PPR dataset, normalises column names, and engineers features from sale dates and addresses.
2. **Hyperparameter tuning with Optuna** — automatically searches for the best LightGBM settings using cross-validation.
3. **Experiment tracking with MLflow** — logs parameters, metrics, and trained models to a local MLflow tracking store.
4. **Two prediction tasks:**
   - **Regression** — predict the sale price in euros.
   - **Classification** — predict whether a property is in the top 25% by price.
5. **Two deployment patterns:**
   - **Option A** — preprocessing baked into a scikit-learn `Pipeline`, registered as a single artifact.
   - **Option B** — preprocessing saved separately with a custom `mlflow.pyfunc` wrapper for more flexibility.

## Requirements

```
python >= 3.9
mlflow
optuna
lightgbm
scikit-learn
joblib
pandas
numpy
```

Install with:

```bash
pip install mlflow optuna lightgbm scikit-learn joblib pandas numpy
```

## Quick Start

Open the notebook in Jupyter, VS Code, or Google Colab and run all cells. The default settings (`SAMPLE_ROWS=15000`, `N_TRIALS=10`, `CV_FOLDS=2`) are tuned for a fast demo run.

For production-quality results, set `SAMPLE_ROWS = None`, `N_TRIALS = 50+`, and `CV_FOLDS = 5`.

## Dataset

The data is downloaded at runtime from the Smart Dublin open data portal (PPR 2021 CSV). No local data files are required.
