# Geospatial Air Quality Analysis with Kedro

This repo is a learning-oriented Kedro project for exploring public geospatial data with a reproducible Python workflow built around `pyenv`, `poetry`, and `kedro`.

The project uses the Open-Meteo Geocoding API and Air Quality API to build a small end-to-end pipeline that:

- resolves a list of cities into latitude/longitude coordinates
- downloads hourly air-quality measurements for those locations
- prepares features for unsupervised and supervised analysis
- runs `KMeans` clustering on location-level summaries
- trains a baseline classifier that predicts whether air quality crosses a configurable AQI threshold

## Learning goals

- Understand how Kedro organizes modular pipelines and data catalogs
- Practice basic geospatial feature engineering with latitude and longitude data
- Compare an unsupervised task (`KMeans`) with a simple classification problem
- Keep the environment reproducible with `pyenv` and `poetry`

## Tooling

- `pyenv` manages the Python version
- `poetry` manages dependencies and the virtual environment
- `kedro` organizes the project structure and pipeline execution

## Setup

1. Install the Python version in [`.python-version`](/C:/Users/61422/Desktop/OMSCS/projects/MLops/traffic-analysis/.python-version).
2. Create the environment with `poetry install`.
3. Activate the environment with `poetry shell` or run commands through `poetry run`.

## Run the pipeline

Run the default pipeline:

```bash
poetry run kedro run
```

The default parameters pull a small set of cities and a short history window to keep the first run manageable.

## Pipeline overview

- `ingestion`
  Fetches geocoding and air-quality data from Open-Meteo.
- `data_engineering`
  Validates, cleans, and reshapes raw API data into modeling tables.
- `data_science`
  Produces cluster assignments and baseline classification metrics.

## Key configuration

Most learning-time changes should happen in [parameters.yml](/C:/Users/61422/Desktop/OMSCS/projects/MLops/traffic-analysis/conf/base/parameters.yml):

- change the list of locations
- add or remove pollutant variables
- adjust the AQI threshold for classification
- tune the number of clusters

## Outputs

After a run, Kedro writes:

- geocoded locations
- hourly air-quality observations
- location-level clustering features and cluster assignments
- row-level classification features
- classifier metrics and sample predictions

## Notes

- The project depends on public APIs, so a pipeline run needs network access.
- The first version favors clear, inspectable transformations over heavy modeling or mapping.
- Read [plan.md](/C:/Users/61422/Desktop/OMSCS/projects/MLops/traffic-analysis/plan.md) for the implementation roadmap and project framing.
