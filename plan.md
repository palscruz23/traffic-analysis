# Kedro Plan: Geospatial Air Quality Analysis

## Summary

Build a learning-first Kedro project that uses public geospatial API data to explore air-quality patterns across locations. The project should teach Kedro structure, reproducible ML pipelines, and how the same dataset can support both unsupervised clustering and a simple classification task. The environment and dependency workflow should use `pyenv` for Python version management and `poetry` for packaging and dependencies.

## Implementation Changes

1. Milestone 1: Kedro project foundation

- Create a greenfield Kedro project in this repo with a package such as `traffic_analysis`.
- Set up the standard Kedro layout for `conf`, `data`, and `src`.
- Standardize local setup around:
  - `pyenv` to pin the Python version used for the project
  - `poetry` to manage dependencies, virtual environments, and script entrypoints
- Keep the project simple and reproducible rather than production-heavy.
- Add documentation that explains the learning goals:
  - learn Kedro pipelines and configuration
  - learn basic geospatial feature engineering
  - compare clustering and classification workflows on one dataset

2. Milestone 2: Public API setup and data contract

- Start from a public geospatial API pair:
  - Open-Meteo Geocoding API for city-to-coordinate resolution
  - Open-Meteo Air Quality API for hourly pollutant measurements
- Define a canonical raw input contract for the ingested data:
  - requested location name
  - resolved location metadata
  - latitude and longitude
  - hourly timestamp
  - pollutant variables
  - air-quality index variable for label creation
- Create a documented ingestion path for local experimentation.

3. Milestone 3: Data preparation and feature engineering

- Build a Kedro data-engineering pipeline that:
  - validates required columns
  - cleans null or impossible geospatial and pollutant values
  - creates a location-level table for clustering
  - creates a row-level table for classification
- Keep v1 features interpretable:
  - latitude and longitude
  - hour and day-of-week signals
  - pollutant measurements
  - simple aggregated pollutant summaries for location clustering

4. Milestone 4: Baseline clustering and classification

- Train a baseline `KMeans` model on location-level air-quality summaries.
- Train a baseline classifier that predicts whether a row exceeds a configurable AQI threshold.
- Evaluate the classification model on a holdout split and track:
  - accuracy
  - precision
  - recall
  - F1 score
- Save enough metadata to reproduce inference assumptions.

5. Milestone 5: Reporting and learning outputs

- Produce outputs that help a learner inspect what happened:
  - geocoded location table
  - cleaned hourly dataset
  - cluster assignments
  - classification metrics
  - prediction samples
- Document:
  - what each Kedro pipeline does
  - how labels are derived from AQI
  - what clustering reveals versus what classification predicts

## Public Interfaces and Data Contracts

- Input configuration contract: locations, pollutant list, history window, clustering parameters, and AQI threshold.
- Geocoded dataset contract: one row per resolved location with coordinates and metadata.
- Hourly dataset contract: one row per location-timestamp with pollutant columns and AQI.
- Clustering dataset contract: one row per location with aggregated pollutant features.
- Classification dataset contract: one row per location-timestamp with engineered features and a binary target label.

## Test Plan

- Data tests:
  - required columns exist after ingestion
  - coordinates stay within valid latitude and longitude ranges
  - AQI label generation is deterministic
- Model tests:
  - clustering produces labels for each input location
  - classifier training succeeds on a minimal sample
  - evaluation metrics are numeric and bounded
- Pipeline tests:
  - the feature-engineering stage produces both clustering and classification tables
  - the default parameters are sufficient to build a complete Kedro run when the APIs respond successfully

## Milestone Sequence

1. Kedro bootstrap and environment tooling
2. Geocoding and air-quality ingestion
3. Feature engineering for clustering and classification
4. Baseline model training and evaluation
5. Reporting and documentation

## Assumptions and Defaults

- The repo remains a learning exercise, not a production application.
- The first version uses one public API provider family and one baseline classifier.
- The classification target is derived from AQI thresholding rather than a hand-labeled dataset.
- The initial workflow is batch-oriented and API-driven.
- Python environment management is part of the learning exercise, with `pyenv` and `poetry` treated as required project setup rather than optional tooling.
