
# Spatio-Temporal Fire Activity Prediction in Kalimantan

An undergraduate thesis project on predicting next-day fire activity at the spatial grid-cell level in Kalimantan and Sumatera, Indonesia, using historical satellite-based fire observations and meteorological conditions.

## Overview

Forest and land fires are influenced by temporal patterns of previous fire activity as well as environmental and meteorological conditions.

This research develops a machine learning pipeline to predict whether a spatial grid cell will have **observed fire activity on the following day** based on information available from previous days.

The current research focuses on:

- Temporal fire activity patterns
- Fire Radiative Power (FRP)
- Meteorological conditions
- Spatial relationships between neighboring cells
- Advanced machine learning and deep learning approaches

## Research Objective

The main objective is to develop and evaluate predictive models for next-day fire activity at the cell-day level.

The prediction task is formulated as a binary classification problem:

- `1` — observed fire activity on the following day
- `0` — no fire detection observed on the following day

### Important Label Interpretation

Fire activity is derived from satellite observations. Therefore, a cell with no satellite fire detection should **not** be interpreted as proof that no fire occurred.

The target represents **observed satellite fire activity**, rather than absolute ground-truth fire presence or absence.

## Dataset

The research dataset consists of:

### Fire Data

Fire detections are derived from **NASA FIRMS VIIRS** satellite observations.

The fire detections are transformed from point observations into spatial grid cells and aggregated at the daily level.

The resulting fire-related variables include:

- Fire detection count
- Fire Radiative Power (FRP)
- Historical lag features
- Rolling fire activity features

### Meteorological Data

Meteorological variables are obtained from hourly weather data and aggregated into daily cell-level features.

The current weather features include:

- Temperature
- Relative humidity
- Precipitation
- Boundary layer height
- Vapour pressure deficit
- Wind speed
- Wind components

### Study Area

The current modeling experiments focus on **Kalimantan and Sumatera, Indonesia**.

The available observations cover approximately:

**May 2026 – September 2026**

Raw and processed datasets are not included in this repository because of their size.

## Prediction Framework

The prediction pipeline can be summarized as:

```text
Satellite Fire Detections
          │
          ▼
   Spatial Binning
          │
          ▼
     Cell-Day Data
          │
          ├───────────────┐
          │               │
          ▼               ▼
   Fire History       Weather Data
      Features           │
          │               │
          └───────┬───────┘
                  ▼
         Feature Engineering
                  │
                  ▼
        Temporal Data Splitting
                  │
          ┌───────┼────────┐
          ▼       ▼        ▼
        Train  Validation  Test
          │       │        │
          └───────┼────────┘
                  ▼
             Model Training
                  │
                  ▼
            Model Evaluation
````

## Feature Engineering

The main benchmark feature set consists of 20 features.

### Fire History Features

10 fire-history features are used:

* `fire_count_lag1`
* `fire_count_lag3`
* `fire_count_lag7`
* `frp_sum_lag1`
* `frp_sum_lag3`
* `frp_sum_lag7`
* `fire_count_roll3`
* `fire_count_roll7`
* `frp_sum_roll3`
* `frp_sum_roll7`

The lag and rolling features are constructed using previous-day fire observations to avoid using information from the prediction day.

### Weather Features

10 meteorological features are used:

* `temperature_mean`
* `temperature_max`
* `relative_humidity_mean`
* `precipitation_sum`
* `boundary_layer_height_mean`
* `vapour_pressure_deficit_mean`
* `wind_speed_mean`
* `wind_speed_max`
* `wind_u_mean`
* `wind_v_mean`

## Temporal Data Split

The experiments use a chronological split to prevent future observations from being used to train models for earlier periods.

The main split is:

| Dataset    | Period                  |
| ---------- | ----------------------- |
| Train      | 2026-05-08 – 2026-07-31 |
| Validation | 2026-08-01 – 2026-08-31 |
| Test       | 2026-09-01 – 2026-09-10 |

An additional temporal split is used during model tuning so that the August validation set remains untouched during hyperparameter selection.

## Evaluation

The primary evaluation metric is:

**PR-AUC (Precision-Recall Area Under the Curve)**

PR-AUC is emphasized because the target distribution is imbalanced and the evaluation needs to consider the trade-off between precision and recall across classification thresholds.

Additional evaluation metrics include:

* Precision
* Recall
* F1-score
* Accuracy
* Confusion matrix

## Models and Experiments

The research currently includes several model families and feature experiments.

### Benchmark Models

* Logistic Regression
* Random Forest
* Gradient Boosting
* XGBoost

### Advanced Machine Learning

* LightGBM
* Spatial lag features

### Deep Learning

* LSTM
* ConvLSTM

### Additional Feature Experiments

* Air-quality features
* Fire Weather Index (FWI)
* Spatial neighbor features

The experiments are designed to investigate whether additional nonlinear modeling, temporal sequence modeling, spatial information, or environmental representations provide predictive information beyond the benchmark feature set.

## Current Research Status

The project is currently in the methodological development stage.

The initial benchmark and several additional experiments have been implemented. Further experiments are being conducted to investigate potential improvements over the benchmark approach.

The repository therefore represents an **ongoing research project**, and the current experiments should not be interpreted as the final thesis model.

## Project Structure

```text
skripsi-karhut/
│
├── data/
│   ├── raw/
│   ├── intermediate/
│   └── processed/
│
├── notebooks/
│   ├── 01_dataset_audit.ipynb
│   ├── 02_fire_poc.ipynb
│   ├── 03_spatial_binning.ipynb
│   ├── 04_feature_engineering.ipynb
│   ├── 05_modeling.ipynb
│   ├── 06_spatial_lag_modeling.ipynb
│   ├── 07_advanced_modeling.ipynb
│   ├── 08_lstm_modeling.ipynb
│   ├── 09_convlstm_modeling.ipynb
│   └── 09_fwi_feature_engineering.ipynb
│
├── results/
│   ├── figures/
│   └── tables/
│
├── .gitignore
├── README.md
└── requirements.txt
```

## Notebook Workflow

| Notebook                           | Description                                               |
| ---------------------------------- | --------------------------------------------------------- |
| `01_dataset_audit.ipynb`           | Initial dataset inspection and quality audit              |
| `02_fire_poc.ipynb`                | Proof-of-concept processing of fire observations          |
| `03_spatial_binning.ipynb`         | Conversion of fire detections into spatial grid cells     |
| `04_feature_engineering.ipynb`     | Creation of fire-history, weather, and modeling features  |
| `05_modeling.ipynb`                | Baseline modeling, benchmarking, ablation, and evaluation |
| `06_spatial_lag_modeling.ipynb`    | Spatial neighbor feature construction and modeling        |
| `07_advanced_modeling.ipynb`       | Advanced machine learning experiments                     |
| `08_lstm_modeling.ipynb`           | Sequence-based LSTM experiments                           |
| `09_convlstm_modeling.ipynb`       | Spatial-temporal ConvLSTM experiments                     |
| `09_fwi_feature_engineering.ipynb` | Fire Weather Index feature calculation and evaluation     |

## Reproducibility

The repository provides the analysis and modeling notebooks used throughout the research.

The datasets themselves are excluded from version control through `.gitignore`.

To reproduce the experiments, the required Python packages are listed in:

```text
requirements.txt
```

The raw and processed datasets must be available locally according to the directory structure used by the notebooks.

## Data Management

Large datasets and local Python environments are intentionally excluded from Git version control:

```text
data/
.venv/
```

This keeps the repository focused on:

* Research code
* Experimental methodology
* Documentation
* Reproducible analysis workflow

## Author

**Audy Natalie Cecilia Rumahorbo**

Computer Engineering
Universitas Indonesia

---

*This repository is part of an ongoing undergraduate thesis research project.*

