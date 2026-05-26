# AI-Based Sales Demand Forecasting


A university machine learning project for forecasting demand using historical sales data. The project compares several predictive modelling approaches, including a baseline neural network, clustering-enhanced neural network, NARX/LSTM, XGBoost, and a hybrid NARX + XGBoost model.

The aim is to support food-service supply chain planning by predicting future dish quantities from previous sales behaviour and related variables such as price, revenue and discount values.

## Project Overview

This repository contains a configurable forecasting pipeline built in Python/Jupyter Notebook. The workflow loads sales files, prepares lag-based time-series features, trains multiple machine learning models, evaluates their performance, and supports hyperparameter optimisation through Keras Tuner.

The project is designed so that the same notebook can be reused with different datasets by editing configuration files rather than changing the main code.

## Main Features

- Config-driven data loading from CSV or Excel files.
- Custom column mapping, data types, aliases, skipped rows and aggregation rules.
- Chronological ordering of input files using filename-based date extraction.
- Lag-based feature creation for time-series forecasting.
- Baseline dense neural network for demand prediction.
- KMeans clustering to create additional dish/behaviour features.
- Cluster-enhanced neural network model.
- NARX-style LSTM model for temporal demand forecasting.
- XGBoost regression model for structured tabular forecasting.
- Hybrid NARX + XGBoost model, where XGBoost learns residual errors from the NARX model.
- TimeSeriesSplit cross-validation for time-aware evaluation.
- Hyperparameter tuning for neural network, NARX, XGBoost and hybrid model variants.
- Evaluation using MAE, MSE and R².
- Diagnostic visualisations, including training curves and prediction error plots.

## Repository Structure

```text
.
├── project code.ipynb      # Main Jupyter Notebook containing the full forecasting workflow
├── data.cfg                # Data-loading and modelling configuration file
├── tuner.cfg               # Hyperparameter tuning configuration file
├── README.md               # Project documentation
└── data/                   # Local data folder, not included in the repository by default
```

The dataset is intentionally not included in this repository. To run the project, create a `data` folder in the same directory as the notebook and place the relevant data files inside it.

## Configuration Files

### `data.cfg`

Controls how the data is loaded and how the forecasting problem is defined.

Important settings include:

| Setting | Purpose |
|---|---|
| `directory` | Folder containing the input data files. Default: `data` |
| `fields` | Columns to load, with optional data types and aliases |
| `index_field` | Unique identifier used as the index, such as dish/product code |
| `skiprows` / `skipfooter` | Rows to ignore at the start or end of each source file |
| `aggregate` | Aggregation logic when merging repeated records |
| `y_name` | Target variable to predict |
| `x_fields` | Input variables used by the models |
| `pred_fields` | Fields used when generating predictions |
| `show_fields` | Extra fields shown with prediction outputs |
| `epoch_count` | Maximum number of training epochs |
| `batch_size` | Training batch size |
| `patience_count` | Early stopping patience |
| `lag` | Number of previous time periods used as model input |
| `forecast_size` | Number of future periods to forecast |
| `sort_expression`, `sort_pattern`, `sort_repl` | Rules used to extract and reorder dates from filenames |

### `tuner.cfg`

Controls the hyperparameter search process.

Important settings include:

| Setting | Purpose |
|---|---|
| `max_trials_count` | Maximum number of hyperparameter trials |
| `best_num_count` | Number of best models/configurations to keep |
| `max_dense_count` | Maximum number of dense layers searched |
| `min_neuron_count`, `max_neuron_count`, `step_neuron_count` | Search range for neural network layer sizes |
| `aim_objective` | Metric optimised by the tuner, such as `val_mae` |
| `aim_loss` | Model loss function, such as `mse` |
| `choice_activation` | Activation functions available to the tuner |
| `choice_optimizer` | Optimisers available to the tuner |
| `choice_metrics` | Evaluation metrics used during training |
| `choice_batch_size` | Batch size used during tuning |
| `choice_epochs` | Number of epochs used during tuning |

## Data Setup

By default, the notebook expects this structure:

```text
project-folder/
├── project code.ipynb
├── data.cfg
├── tuner.cfg
└── data/
    ├── sales_file_1.xlsx
    ├── sales_file_2.xlsx
    └── ...
```

To test a different dataset:

1. Create a `data` folder in the same directory as the notebook.
2. Place the relevant CSV or Excel files inside the `data` folder.
3. Place the matching `data.cfg` file in the same directory as the notebook.
4. Check that the column names, aliases, target variable and input fields in `data.cfg` match the dataset.
5. Run the notebook from top to bottom.

The default data directory can be changed by editing the `directory` value in `data.cfg`.

## Installation

Create and activate a Python environment, then install the main dependencies:

```bash
pip install pandas numpy matplotlib scikit-learn tensorflow keras-tuner xgboost openpyxl
```

If running the notebook in Google Colab, some packages may already be installed. If running locally, remove or comment out any Colab-specific imports that are not needed in your environment.

## How to Run

1. Clone the repository:

```bash
git clone <repository-url>
cd <repository-folder>
```

2. Add the configuration files and data:

```text
project code.ipynb
data.cfg
tuner.cfg
data/
```

3. Open the notebook:

```bash
jupyter notebook "project code.ipynb"
```

4. Run the cells in order:

- Load libraries and configuration files.
- Load and merge the sales data.
- Create lag-based forecasting features.
- Train baseline models.
- Train clustered and temporal models.
- Run hyperparameter optimisation.
- Compare models using MAE, MSE and R².
- Generate prediction and error visualisations.

## Models Implemented

### 1. Dense Neural Network

A baseline feed-forward neural network trained on lagged sales and related numeric features.

### 2. Clustered Neural Network

Uses KMeans clustering to group similar records and adds cluster indicators as extra model inputs.

### 3. NARX/LSTM Model

A temporal neural network approach that uses lagged historical values to model demand patterns over time.

### 4. XGBoost Regressor

A gradient boosting model used as a strong tabular-data baseline for demand prediction.

### 5. Hybrid NARX + XGBoost Model

Combines temporal and residual-learning approaches:

1. NARX/LSTM predicts the base demand signal.
2. Residual errors are calculated from the NARX predictions.
3. XGBoost learns the residual pattern.
4. Final prediction combines the NARX prediction and XGBoost residual correction.

## Evaluation

The project evaluates model performance using:

- Mean Absolute Error (MAE)
- Mean Squared Error (MSE)
- R² score
- Time-series cross-validation
- Training/validation learning curves
- Actual vs predicted plots
- Prediction error visualisations

MAE is used as the main optimisation objective because it is easy to interpret in the context of demand forecasting.

## Notes and Limitations

- The repository does not include the original sales dataset.
- Forecast quality depends heavily on the amount and consistency of available historical data.
- Very small datasets may limit the reliability of neural networks and hyperparameter tuning.
- The notebook is research/prototype code rather than a production deployment system.
- Configuration files must match the dataset column names and folder structure.

## Future Improvements

- Convert the notebook into a modular Python package.
- Add a command-line interface for selecting datasets and model types.
- Add automated unit tests for configuration loading and feature engineering.
- Save trained models and prediction outputs in a structured `outputs/` folder.
- Add clearer experiment tracking for model comparison.
- Build a simple user interface for uploading data and changing model parameters.

## Suggested Citation / Academic Context

This project was developed as part of a university machine learning/data science project focused on applying AI and predictive analytics to food-service supply chain demand forecasting.
