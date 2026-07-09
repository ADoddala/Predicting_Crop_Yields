# Predicting_Crop_Yields

# Crop Yield Prediction Using Machine Learning

This project develops machine learning models to predict crop yield (bushels per acre) using historical crop production data combined with weather and soil characteristics. The goal is to understand how environmental factors such as precipitation, temperature, and soil properties influence crop productivity and build a model that can predict future yields.

The project focuses on three major crops:
- Corn
- Soybeans
- Wheat


## Data Sources

The project combines multiple datasets:

### USDA National Agricultural Statistics Service (NASS) (link: https://quickstats.nass.usda.gov/api/api_GET/)
* Historical county-level crop yield data
* Crop type and yearly yield measurements

### NOAA Climate Data (link : https://www.ncei.noaa.gov/cdo-web/api/v2/data)
* Precipitation (PRCP)
* Maximum temperature (TMAX)
* Minimum temperature (TMIN)

### NRCS Soil Data(SSURGO) (link: https://www.nrcs.usda.gov/resources/data-and-reports/gridded-soil-survey-geographic-gssurgo-database¶)
* Soil water storage capacity
* Drainage characteristics
* Soil pH
* Organic matter
* Soil depth and water table information


## Data Preparation

The data preprocessing steps included:

* Removing duplicates
* Standardized county names
* Filled missing weather by median
* Combining crop yield, weather, and soil datasets
* Renaming soil variables

A time-based split was used to simulate real-world prediction, where future years are unknown during model training.
- Training data: years before 2018
- Unseen test data: years 2018 and later

## Feature Engineering

Additional features were created to capture environmental effects on crop growth:

* Average temperature: `(TMAX + TMIN) / 2`
* Temperature range:- `TMAX - TMIN`
* Previous year's yield: Lag-1 yield feature by county

#### Growing season weather features:
* Total precipitation during important growing months
* Critical month precipitation
* Critical month maximum temperature

#### Weather interaction features:
* Rainfall and temperature interaction
* Heat and drought indicator

### Growing seasons were customized by crop:

| Crop    | Critical Month | Growing Season |
|------   |--------------- |----------------|
| Corn    |   July         | June-August    | 
| Soybeans|   August       | July-September |
| Wheat   |   May          | April-June     |


## Machine Learning Models

The following regression models were evaluated:

### Ridge Regression
Used as a baseline linear model to understand the relationship between environmental variables and yield.

### Random Forest Regression
Used to capture nonlinear relationships between:
* Weather conditions
* Soil characteristics
* Crop yield patterns

Random Forest showed improved performance compared with Ridge Regression, indicating that crop yield depends on complex interactions between environmental factors.

## Model Evaluation

Models were evaluated using:

* Mean Squared Error (MSE)
* Mean Absolute Error (MAE)
* R² Score

The evaluation included:

1. Internal validation using historical years
2. Future unseen data evaluation (2018–2024)

## Key Findings

* Weather variables, especially precipitation and temperature, strongly influence crop yield predictions.
* Random Forest performed better than linear models because it can capture nonlinear relationships.
* Previous-year yield provides useful information because crop productivity often follows historical patterns.
* Time-based validation provided a more realistic estimate of future prediction performance.

## Future Improvements

Potential improvements include:

* Testing Gradient Boosting and XGBoost models
* Expand the analysis to additional states and crop types
* Developing county level yield forecasting dashboards

