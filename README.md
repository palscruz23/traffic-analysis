# Manufacturing Throughput Optimisation with Kedro

This repo is set up as a learning exercise for three topics:

- Kedro for reproducible ML pipelines
- throughput optimisation in a manufacturing setting
- first-principles-informed ML

The project structure is intentionally small. It gives you a working Kedro-shaped scaffold with enough code to inspect how data engineering, modeling, and recommendation stages fit together.

## Learning goals

- Understand how Kedro organizes nodes, pipelines, parameters, and datasets
- Train a baseline model that predicts throughput
- Add simple first-principles constraints so recommendations remain operationally plausible

## Proposed workflow

1. Place a public manufacturing dataset in `data/01_raw/manufacturing_throughput.csv`
2. Update `conf/base/parameters.yml` to match the dataset columns
3. Run the data engineering, modeling, and recommendation pipelines
4. Compare unconstrained and constrained recommendations

## Project layout

- `src/manufacturing_throughput/pipelines/data_engineering`
  Cleans raw data, validates required columns, and prepares features.
- `src/manufacturing_throughput/pipelines/data_science`
  Trains a baseline regression model and computes evaluation metrics.
- `src/manufacturing_throughput/pipelines/recommendation`
  Scores candidate operating settings and applies feasibility checks.

## Dataset guidance

Use a public manufacturing or industrial process dataset that includes:

- a throughput-like target
- controllable settings or process variables
- enough context to define simple feasible ranges

The current code assumes CSV input and a supervised regression target, but the parameters are meant to be adapted as you learn the dataset.

## Suggested next learning steps

1. Read [plan.md](/C:/Users/61422/Desktop/OMSCS/projects/MLops/traffic-analysis/plan.md) and map each milestone to the code scaffold.
2. Inspect [pipeline_registry.py](/C:/Users/61422/Desktop/OMSCS/projects/MLops/traffic-analysis/src/manufacturing_throughput/pipeline_registry.py) to see how Kedro composes pipelines.
3. Trace the data flow through the node functions before changing anything.
4. Replace the default assumptions in `parameters.yml` with choices based on your dataset.
