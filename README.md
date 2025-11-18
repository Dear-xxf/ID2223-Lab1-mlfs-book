# Air Quality Prediction – Visby, Sweden
This repository contains our solution for **ID2223 – Lab 1 (HT2025)**.  
A serverless machine learning system that predicts daily **PM2.5** air quality levels in Visby, Sweden using Hopsworks
Feature Store, scheduled feature pipelines, and a batch inference pipeline.


## Dashboard
Our dashboard visualizes:

- Forecasted PM2.5 levels for the next 7–10 days
- Historical measured PM2.5 for the selected sensor
- A **hindcast** plot showing predictions vs. outcomes for recent days

 **Public dashboard URL:**  TODO



## Sensor Location
We monitor air quality using the following sensor:
- AQICN_COUNTRY=sweden
- AQICN_CITY=visby
- AQICN_STREET=ostervag-17
- AQICN_URL=https://api.waqi.info/feed/@13987



## Lab Tasks

- **Task 1 – Backfill feature pipeline**  
  `notebooks/airquality/1_air_quality_feature_backfill.ipynb`  
  Downloads historical air quality (CSV from AQICN) and historical weather data  
  (> 1 year where available), and writes them as two Feature Groups in Hopsworks:
  `air_quality` (v1 & v2) and `weather` (v1).

- **Task 2 – Daily feature pipeline & scheduling**    
  `notebooks/airquality/2_air_quality_feature_pipeline.ipynb`  
  Daily pipeline that:
  - downloads yesterday’s air quality and weather,
  - downloads weather forecast for the next 7–10 days,
  - updates the Feature Groups in Hopsworks, including lag features in
    `air_quality` v2.  
  The notebook is scheduled using **GitHub Actions**  

- **Task 3 – Training pipeline**  
  `notebooks/airquality/3_air_quality_training_pipeline.ipynb`  
  Creates a Feature View from the Feature Groups, trains an XGBoost regression
  model to predict PM2.5, evaluates it, and registers the model in the Hopsworks
  Model Registry.

- **Task 4 – Batch inference pipeline & dashboard**  
  `notebooks/airquality/4_air_quality_batch_inference.ipynb`  
  Batch inference notebook that downloads the trained model from Hopsworks,
  predicts PM2.5 for the next 7–10 days, and generates PNG plots for the dashboard.

- **Task 5 – Monitoring with hindcast graph**  
  The batch inference pipeline also computes a **hindcast**: predictions over
  recent days are compared against the measured PM2.5 values and saved as a PNG
  that is shown on the dashboard.

- **Task 6 – Lagged PM2.5 features (Grade C)**  
  We extend the feature set with lagged PM2.5 values from the previous 1, 2,
  and 3 days and train a second model (version 2). See Section **“Models and
  performance (v1 vs v2)”** below.



## Models and Performance

We train two versions of the model in the training pipeline notebook.

### Baseline model – **version 1**

- **Training data:** Feature View v1  
- **Model:** `XGBRegressor` with default hyperparameters  
- **Metrics (on test set):**
  - MSE: `TODO: mse`  
  - R²: `TODO: r2`  

**Observation.**  
This model only uses weather features to explain daily PM2.5 levels. It captures
the impact of temperature, precipitation and wind, but does not explicitly use
the temporal dependence of PM2.5 itself.

### Updated model – **version 2** (with lagged PM2.5)

- **New features:** `pm25_1d_ago`, `pm25_2d_ago`, `pm25_3d_ago` from
  `air_quality` v2  
- **Training data:** Feature View v2
- **Model:** `XGBRegressor` with the same configuration for a fair comparison  
- **Metrics (on test set):**
  - MSE: `TODO: mse_v2`  
  - R²: `TODO: r2_v2`  
