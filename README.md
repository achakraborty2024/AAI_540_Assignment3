# Neighborhood Feature Group with SageMaker Feature Store

This project demonstrates how to engineer and store rich neighborhood-level features derived from California housing data and Google Maps address metadata using **Amazon SageMaker Feature Store**. These features are designed to improve housing price prediction models by incorporating contextual neighborhood information.

---

## Project Overview

The goal is to:
- Join housing data with Google Maps address data
- Engineer meaningful features at the **neighborhood** level
- Store the features in an **online + offline feature store**
- Query the features for real-time and batch ML training

---

## Features Created

Each neighborhood entry in the feature group includes:

| Feature Name               | Description                                                             |
|---------------------------|-------------------------------------------------------------------------|
| `primary_key`             | Neighborhood name                                                       |
| `event_time`              | Timestamp of ingestion in ISO 8601 format                               |
| `less_than_1h_ocean`      | One-hot feature from `ocean_proximity`                                  |
| `inland`                  | One-hot feature from `ocean_proximity`                                  |
| `island`                  | One-hot feature from `ocean_proximity`                                  |
| `near_bay`                | One-hot feature from `ocean_proximity`                                  |
| `near_ocean`              | One-hot feature from `ocean_proximity`                                  |
| `median_house_value_capped` | Average capped at \$500,000                                           |
| `median_house_age_grouped` | Discretized in 10-year age buckets                                     |
| `total_households`        | Average number of households (rounded up)                               |
| `bedrooms_per_household`  | Average bedrooms per household (with postal-code-level imputation)      |

---

## Data Sources

- `housing.csv`: California housing data (e.g., from StatLib or similar)
- `housing_gmaps_data_raw.csv`: Google Maps API results with address metadata
- Custom engineered file: `neighborhood_feature_group.csv`

---

## AWS Workflow

### 1. Create Feature Group
- Define schema using `boto3` (not just high-level SageMaker SDK)
- Enable both online and offline store (Athena + S3)
- Automatically link to Glue Data Catalog

### 2. Ingest Feature Records
- Sanitize inputs
- Convert timestamps
- Ensure feature types conform to SageMaker limits

### 3. Query the Feature Store
- Real-time: `get_record()` via `sagemaker-featurestore-runtime`
- Batch training: Athena SQL query on the offline store

---

## Sample Query

```python
featurestore_runtime.get_record(
    FeatureGroupName="neighborhood-feature-group-xxxxxx",
    RecordIdentifierValueAsString="Fisherman's Wharf"
)
