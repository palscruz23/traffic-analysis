# Kedro Plan: Manufacturing Throughput Optimisation

## Summary

Build a learning-first Kedro project that uses a public manufacturing dataset to recommend operating settings that improve throughput while respecting first-principles constraints. The project should teach Kedro structure, reproducible ML pipelines, and how simple operational logic can keep model recommendations realistic. The environment and dependency workflow should use `pyenv` for Python version management and `poetry` for packaging and dependencies.

## Implementation Changes

1. Milestone 1: Kedro project foundation

- Create a greenfield Kedro project in this repo with a package such as `manufacturing_throughput`.
- Set up the standard Kedro layout for `conf`, `data`, and `src`.
- Standardize local setup around:
  - `pyenv` to pin the Python version used for the project
  - `poetry` to manage dependencies, virtual environments, and script entrypoints
- Keep the project simple and reproducible rather than production-heavy.
- Add documentation that explains the learning goals:
  - learn Kedro pipelines and configuration
  - learn throughput-oriented ML workflows
  - learn how first-principles constraints guide recommendations

2. Milestone 2: Public dataset setup and data contract

- Start from a public manufacturing or process dataset with:
  - throughput or production-rate target
  - controllable or operational input settings
  - enough metadata to define feasible ranges or capacity-style constraints
- Define a canonical input contract for the raw data:
  - timestamp or batch identifier
  - line, process, or station identifier if available
  - controllable settings
  - throughput target
  - optional supporting process variables
- Create a documented ingestion path for local experimentation.

3. Milestone 3: Data preparation and feature engineering

- Build a Kedro data-engineering pipeline that:
  - validates required columns
  - cleans obvious null or impossible values
  - separates controllable variables from context variables
  - creates a training-ready feature table
- Keep v1 features interpretable:
  - current operating settings
  - simple interactions if clearly useful
  - optional lag or rolling signals only if the dataset is time-indexed
- Preserve a clear distinction between:
  - variables the model can recommend changing
  - variables that describe context but should not be optimized directly

4. Milestone 4: Baseline throughput model

- Train a baseline regression model that predicts throughput from the prepared features.
- Start with a simple, explainable baseline before trying heavier models.
- Evaluate on a holdout split and track:
  - RMSE or MAE
  - R-squared
  - error behavior on high-throughput cases
- Save the trained model and enough metadata to reproduce inference assumptions.

5. Milestone 5: First-principles-informed recommendation workflow

- Build a recommendation pipeline that proposes better controllable settings by scoring feasible candidates with the trained model.
- Add lightweight first-principles checks grounded in operations logic:
  - settings must remain within known feasible ranges
  - derived throughput recommendations must not exceed a configured bottleneck capacity
  - recommendations should remain close enough to observed operating regions to avoid obviously unrealistic jumps
- Compare:
  - unconstrained best-scoring candidates
  - constraint-aware feasible recommendations

6. Milestone 6: Reporting and learning outcomes

- Produce outputs that help a learner inspect what happened:
  - validation summary
  - model metrics
  - recommended settings
  - reasons candidate settings were rejected by constraints
- Document:
  - what each Kedro pipeline does
  - which rules come from first principles rather than pure ML
  - tradeoffs between predictive performance and operational plausibility

## Public Interfaces and Data Contracts

- Raw input contract: tabular manufacturing records with a throughput target and identifiable controllable settings.
- Prepared dataset contract: cleaned feature table plus a declared set of controllable columns.
- Model artifact contract: trained regression model with feature-column metadata.
- Recommendation output contract: candidate settings with predicted throughput, feasibility flag, and rejection reason when infeasible.

## Test Plan

- Data tests:
  - required columns exist
  - controllable-setting ranges are enforced
  - invalid throughput rows are removed or flagged
- Model tests:
  - training succeeds on a minimal sample
  - predictions are numeric and finite
  - evaluation metrics are produced consistently
- Recommendation tests:
  - infeasible candidates are rejected
  - feasible candidates are ranked by predicted throughput
  - bottleneck-capacity limits are respected

## Milestone Sequence

1. Kedro bootstrap and learning-oriented repo structure
2. Public dataset ingestion and validation
3. Feature engineering for throughput prediction
4. Baseline model training and evaluation
5. Constraint-aware recommendation workflow
6. Reporting and documentation

## Assumptions and Defaults

- The repo remains a learning exercise, not a production application.
- The first version uses one public dataset and one baseline model.
- The recommendation task is constrained optimisation over observed or user-specified operating settings, not control of a live system.
- First-principles guidance stays lightweight and interpretable, using feasibility rules and bottleneck-style limits rather than full simulation.
- The initial workflow is batch-oriented and file-based.
- Python environment management should be part of the learning exercise, with `pyenv` and `poetry` treated as required project setup rather than optional tooling.
