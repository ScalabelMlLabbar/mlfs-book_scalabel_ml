# mlfs-book
O'Reilly book - Building Machine Learning Systems with a feature store: batch, real-time, and LLMs


## ML System Examples


[Dashboards for Example ML Systems](https://featurestorebook.github.io/mlfs-book/)


# Air Quality Prediction System

This repository contains the air quality prediction ML system from the O'Reilly book. We have successfully deployed it locally, connected it to Hopsworks Feature Store, and **enhanced the model with lagged PM2.5 features** to improve prediction accuracy.

## What We've Done

Starting from the book's source code, we have:
- ✅ Set up the complete ML pipeline on our local machine
- ✅ Connected to Hopsworks Feature Store for data management
- ✅ Configured our air quality sensor (Stockholm, Hornsgatan-108)
- ✅ **Added lagged PM2.5 features** (1, 2, and 3 days) to capture temporal patterns
- ✅ Updated the feature groups to version 2 to include lagged features
- ✅ Retrained the XGBoost model with the enhanced feature set
- ✅ Modified the daily feature pipeline to compute lagged features
- ✅ Adapted the batch inference pipeline to handle rolling forecasts with lagged features

## Overview of Notebooks in `notebooks/airquality/`

### 1. Feature Backfill (`1_air_quality_feature_backfill.ipynb`)
**Purpose:** Initialize the feature store with historical data

This notebook:
- Downloads historical PM2.5 measurements from AQICN sensor as CSV
- Fetches historical weather data from Open-Meteo API
- **Creates lagged PM2.5 features** (pm25_lag_1d, pm25_lag_2d, pm25_lag_3d)
- Validates data quality using Great Expectations
- Creates feature groups in Hopsworks:
  - `air_quality` (v2) - PM2.5 measurements **with lagged features** ⭐
  - `weather` (v1) - Daily weather observations

**Our Enhancement:** Added lagged feature computation using pandas shift operations, dropped rows with NaN lagged values, and updated the feature group to version 2.

### 2. Daily Feature Pipeline (`2_air_quality_feature_pipeline.ipynb`)
**Purpose:** Update feature store daily with fresh data

This notebook:
- Retrieves today's PM2.5 reading from AQICN API
- Fetches 7-day weather forecast from Open-Meteo
- **Queries the feature store for the last 3 days of PM2.5** to compute lagged features
- Inserts new records into feature groups

**Our Enhancement:** Added logic to fetch the previous 3 days of PM2.5 measurements from the feature store and attach them as lagged features to today's data.

### 3. Training Pipeline (`3_air_quality_training_pipeline.ipynb`)
**Purpose:** Train and register the prediction model

This notebook:
- Creates a feature view joining air quality and weather data
- Trains an XGBoost regression model with features:
  - **Lagged PM2.5:** pm25_lag_1d, pm25_lag_2d, pm25_lag_3d ⭐
  - **Weather:** temperature, precipitation, wind speed, wind direction
- Evaluates model performance (MSE, R²)
- Saves model to Hopsworks Model Registry

**Our Enhancement:** Updated the feature view to include the three new lagged PM2.5 features, increasing the total feature count from 4 to 7.

### 4. Batch Inference Pipeline (`4_air_quality_batch_inference.ipynb`)
**Purpose:** Generate daily air quality forecasts

This notebook:
- Downloads the latest model from Model Registry
- Fetches weather forecasts for the next 7 days
- **Retrieves recent PM2.5 for lagged features**
- Makes predictions using a rolling forecast approach
- Generates visualization dashboards (forecast and hindcast)

**Our Enhancement:** Added logic to fetch the last 3 days of PM2.5 from the feature store and implement a rolling forecast mechanism where predictions become lagged features for subsequent days.






