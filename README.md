# Crop Yield Prediction Using Machine Learning

## Project Overview

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
* 
#### Data Collection Process
Retrieved annual crop yield records for Tennessee from **2000–2024** using the USDA NASS Quick Stats API.
- Collected yield data for:
  - Corn
  - Soybeans
  - Wheat
- Filtered records using:
  - Statistic: Yield
  - Unit: Bushels per Acre (BU/ACRE)
  - State: Tennessee

#### Data Preparation
- Combined yearly API responses into a single crop yield dataset.
- Removed:
  - "Other Counties"
  - Combined county records
  - Missing county names
- Converted yield values into numeric format.
- Standardized county names.

#### Final dataset
- Created a clean county-level annual crop yield dataset covering 2000–2024.
- Crop yield was used as the target variable for machine learning models and later merged with weather and soil features.

### NOAA Climate Data (link : https://www.ncei.noaa.gov/cdo-web/api/v2/data)

This workflow ensured that weather observations were accurately mapped from individual weather stations to county-level crop yield records.
* Precipitation (PRCP)
* Maximum temperature (TMAX)
* Minimum temperature (TMIN)

#### Weather Data Collection
- Collected monthly weather data from the NOAA NCEI API for Tennessee from **2000–2024**.
- Retrieved:
  - Monthly precipitation (PRCP)
  - Maximum temperature (TMAX)
  - Minimum temperature (TMIN)

#### Weather Data Preparation 
- Retrieved NOAA weather station locations and coordinates.
- Used U.S. Census TIGER/Line county shapefiles with GeoPandas spatial joins to assign weather stations to Tennessee counties.
- Assigned stations outside county boundaries to the nearest county using nearest-neighbor spatial joins to maximize coverage.(only one station outside the county)
- Converted weather data from long format to wide format to create monthly weather features.
- Aggregated station-level observations into county-year averages.

#### Final Dataset
- Generated county-level weather features that could be merged with USDA crop yield records.
- This workflow ensured weather observations were accurately mapped from weather stations to county-level crop production records.

### NRCS Soil Data(SSURGO) (link: https://www.nrcs.usda.gov/resources/data-and-reports/gridded-soil-survey-geographic-gssurgo-database¶)

* Soil water storage capacity
* Drainage characteristics
* Soil pH
* Organic matter
* Soil depth and water table information

#### Soil Data Collection
- Downloaded the SSURGO (Soil Survey Geographic Database) for Tennessee from USDA NRCS.
- Used multiple geodatabase layers to obtain soil physical and chemical properties.

#### Data Preparation 
- Loaded:
  - MUPOLYGON layer containing soil map unit polygons
  - Muaggatt table containing soil attributes
- Joined soil polygons with soil attributes using the MUKEY identifier.
- Selected important soil variables including:
  - Water storage capacity (0–25, 0–50, 0–100, 0–150 inches)
  - Slope
  - Bedrock depth
  - Water table depth
  - Drainage characteristics

#### County-Level Aggregation
- Downloaded Tennessee county boundaries from U.S. Census TIGER/Line shapefiles.
- Reprojected soil polygons and county boundaries to EPSG:5070 for accurate area calculations.
- Intersected soil polygons with county boundaries to calculate soil coverage.
- Generated area-weighted averages for continuous soil variables.
- Calculated percentage coverage for categorical soil properties such as drainage class and hydrologic group.

#### Soil Nutrient Features
- Extracted additional soil chemistry information:
  - Soil pH (ph1to1h2o_r)
  - Organic matter (om_r)
- Filled missing nutrient values using median imputation.
- Aggregated nutrient information through:
  - CHorizon → Component (COKEY)
  - Component → Map Unit (MUKEY)
  - Map Unit → County

#### Final dataset
- Combined soil physical properties and nutrient information into a county-level soil dataset.
- Exported final soil features for integration with weather and crop yield datasets.


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





## Technologies Used

### Programming Languages
* Python

### Data Processing and Analysis
* Pandas
* NumPy
* GeoPandas

### Data Visualization
* Matplotlib
* Seaborn

### Machine Learning
* Scikit-learn
  * Ridge Regression
  * Random Forest Regression
  * Pipeline and ColumnTransformer
  * Model evaluation metrics (MSE, MAE, R²)

### Geospatial Analysis
* GeoPandas
* Shapely
* U.S. Census TIGER/Line Shapefiles
* Coordinate Reference Systems (CRS) transformations

### Data Sources and APIs
* USDA NASS Quick Stats API
* NOAA NCEI Climate Data API
* USDA NRCS SSURGO Soil Database(contains soil type and distribution)


