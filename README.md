# ML Python Lab 2 — California Housing Regression

This lab demonstrates a compact, reproducible workflow for tabular regression with scikit-learn using the California Housing dataset. It provides:

- Reusable pipelines with preprocessing and models
- Hyperparameter search via `GridSearchCV` + `KFold`
- Evaluation with RMSE, MAE, and R²
- A Jupyter notebook to explore experiments and figures


## Project Structure

```
ml_python/lab2/
├─ src/
│  └─ housing.py            # Pipelines, training, evaluation utilities
├─ notebooks/
│  ├─ experiment.ipynb      # Main notebook to run experiments
│  └─ figures/              # Generated figures (kept in repo)
├─ requirements.txt         # Python dependencies
├─ .gitignore               # Ignore caches, data, models, etc.
└─ README.md                # You are here
```


## Requirements

- Python 3.9+ (3.10/3.11 recommended)
- See pinned packages in `requirements.txt`:
  - scikit-learn, numpy, pandas, matplotlib, jupyterlab
  - dev tools: ruff, nbstripout

Optional but recommended:
- A virtual environment (e.g., `venv`, `conda`)


## Quickstart

1) Create and activate a virtual environment

```
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
```

2) Install dependencies

```
pip install -r requirements.txt
```

3) Launch JupyterLab and open the notebook

```
jupyter lab
```

Open `notebooks/experiment.ipynb` and run the cells.


## Using the Library (Python API)

You can also run experiments from Python using the utilities in `src/housing.py`.

Available models (keys in `MODELS`):
- `simple_elastic`: ElasticNet with standard scaling
- `poly_elastic_2`: PolynomialFeatures (degree=2) + ElasticNet
- `poly_elastic_3`: PolynomialFeatures (degree=3) + ElasticNet
- `knn`: KNeighborsRegressor with standard scaling

Each model includes a `param_grid` for `GridSearchCV`.

Example workflow:

```python
from src.housing import set_seed, load_dataset, train, eval

# 1) Reproducibility
set_seed(1)

# 2) Load data
X_train, X_test, y_train, y_test = load_dataset(test_size=0.2, random_state=1)

# 3) Train with cross-validated grid search
search = train(
    model="simple_elastic",    # or "poly_elastic_2", "poly_elastic_3", "knn"
    X_train=X_train,
    y_train=y_train,
    cv_splits=5,
    random_state=1,
)

# 4) Evaluate on the test set
results = eval(search, X_test, y_test)
print("Best estimator:", results["estimator"])  # the fitted model inside the pipeline
print("RMSE:", results["rmse"])                 # note: scikit-learn returns MSE by default; RMSE ~ sqrt(MSE)
print("MAE:", results["mae"])                  
print("R^2:", results["r2"])                   
```

Notes:
- The returned `search` is a fitted `GridSearchCV`. The best full pipeline is at `search.best_estimator_`.
- `eval` returns predictions and metrics for convenience.


## Reproducibility & Configuration

- Randomness: Use `set_seed(seed)` and pass `random_state` to `load_dataset` and `train`.
- Cross-validation: Configure with `cv_splits` in `train` (default 5).
- Hyperparameters: Adjust `param_grid` per model inside `src/housing.py` or pass a custom configuration by extending the code.


## Notebook Hygiene

- `nbstripout` is included to help keep notebooks lightweight. To activate in this repo:

```
nbstripout --install
```

This installs a Git filter that strips large outputs on commit. You can reverse with `nbstripout --uninstall`.


## Data & Artifacts

- The dataset is fetched programmatically via `sklearn.datasets.fetch_california_housing`, so no local data is required.
- `.gitignore` excludes typical data/model artifacts (`data/`, `models/`, `*.pkl`, `*.csv`, etc.) to keep the repo clean.


## Troubleshooting

- If Jupyter cannot find the kernel, ensure your virtual environment is active when installing and launching JupyterLab.
- Version conflicts: Reinstall with a fresh environment and `pip install -r requirements.txt`.
- Long grid searches: Reduce `cv_splits`, narrow `param_grid`, or try a smaller model first.


## License

No license specified. If you plan to share or publish, consider adding a LICENSE file.

