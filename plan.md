# Kedro Plan: Sydney Traffic Congestion Prediction

## Summary

Build a greenfield Kedro project that predicts 1-hour-ahead congestion class for Sydney road segments using historical batch traffic data and runs on an hourly batch cadence. The first production consumer is the internal ML team, so v1 should prioritize a clean experimentation workflow, reproducible pipelines, model evaluation, and operational monitoring over a polished public dashboard.

## Implementation Changes

1. Milestone 1: Project foundation and Kedro bootstrap

- Create a Kedro project in this repo with a package such as `traffic_analysis`.
- Define environments for local, dev, and prod.
- Set up a minimal data catalog structure for raw, intermediate, feature, model, prediction, and reporting datasets.
- Establish naming conventions early:
  - `raw_sydney_traffic`
  - `validated_traffic`
  - `road_segment_features`
  - `congestion_labels`
  - `trained_model`
  - `hourly_predictions`
  - `monitoring_metrics`
- Add basic project docs that state the prediction target, spatial unit, cadence, and success criteria.

2. Milestone 2: Data ingestion and Sydney scoping

- Implement a raw-data ingestion pipeline for historical batch traffic data.
- Define the required input schema:
  - `timestamp`
  - road segment identifier
  - location or geometry reference
  - traffic speed, travel time, volume, or delay fields
- Add Sydney-specific filtering and validation:
  - keep only Sydney road segments
  - normalize timestamps to a single timezone
  - remove duplicates and impossible values
- Decide and document the canonical road-segment reference used throughout the project.
- Output a trusted, cleaned dataset for downstream feature engineering.

3. Milestone 3: Congestion target definition and feature engineering

- Define congestion as a 3-class label for each road segment at each hour:
  - `low`
  - `medium`
  - `high`
- Choose a deterministic labeling rule from historical speed/travel-time delay relative to segment baseline.
- Build feature pipelines for:
  - lagged traffic conditions
  - rolling-window statistics
  - hour-of-day/day-of-week signals
  - peak-period indicators
  - segment-level historical baselines
- Keep the first version batch-only and tabular; do not introduce streaming features or online stores.
- Store training-ready feature tables and label tables as versioned Kedro outputs.

4. Milestone 4: Model training, evaluation, and registry-ready packaging

- Start with a strong baseline classifier before trying heavier models.
- Train on historical windows and evaluate with time-aware splits, not random splits.
- Track metrics that match the classification problem:
  - macro F1
  - per-class precision/recall
  - confusion matrix
  - calibration quality if probabilities are used
- Save model artifacts, training metadata, and feature schema together so inference can reproduce training assumptions.
- Define acceptance criteria for promotion:
  - model beats a simple historical baseline
  - no class collapses
  - feature schema matches inference schema

5. Milestone 5: Batch inference and consumable outputs

- Build an hourly inference pipeline that scores the latest available segment-hour records.
- Produce outputs tailored to the internal ML team:
  - predictions by segment and hour
  - top predicted congestion hotspots
  - feature distributions for the scoring batch
  - model version used
- Keep serving offline first: write predictions to files/tables rather than building an API in v1.

6. Milestone 6: Monitoring and alerting

- Add data-quality monitoring on every hourly run:
  - row counts
  - null rates
  - unseen segment IDs
  - missing expected hours
  - schema drift
- Add model monitoring after predictions:
  - prediction class distribution drift
  - feature drift vs training baseline
  - delayed performance tracking once actual labels arrive
- Define alert thresholds for:
  - ingestion failure
  - excessive missingness
  - severe distribution drift
  - degraded classification performance
- Store monitoring outputs as datasets so they can be visualized later or wired into alerting tools.

7. Milestone 7: Retraining workflow

- Create a retraining pipeline separate from hourly inference.
- Use a scheduled retraining cadence, with a default of weekly retraining using the latest validated historical data.
- Add trigger conditions that can force retraining earlier:
  - sustained feature drift
  - sustained prediction distribution drift
  - performance drop after labels arrive
- Require automatic evaluation against the currently deployed model before replacement.
- Promote a new model only if it passes predefined acceptance thresholds and schema checks.

8. Milestone 8: Production hardening

- Add reproducibility and operational controls:
  - parameterized date windows
  - deterministic train/test splits
  - environment-specific config
  - run metadata logging
- Add failure handling for missing batches and late-arriving labels.
- Document the runbook:
  - how ingestion runs
  - how training runs
  - how inference runs
  - what monitoring signals mean
  - when retraining is triggered

## Public Interfaces and Data Contracts

- Raw input contract: batch traffic records keyed by timestamp and road segment.
- Validated dataset contract: cleaned Sydney-only records with canonical segment IDs and normalized timestamps.
- Label contract: one congestion class per segment-hour for training windows.
- Prediction output contract: segment-hour prediction records with predicted class, probability, model version, and run ID.
- Monitoring output contract: run-level quality checks, drift metrics, and delayed performance metrics.

## Test Plan

- Data tests:
  - Sydney filter excludes out-of-scope records
  - timestamps normalize correctly
  - impossible traffic values are rejected or flagged
- Feature tests:
  - lag features do not leak future information
  - rolling windows use only past data
  - feature schema is identical between train and inference
- Model tests:
  - time-based split logic is correct
  - baseline comparison is computed consistently
  - class predictions remain within the allowed label set
- Pipeline tests:
  - hourly inference succeeds on a minimal batch
  - retraining pipeline produces a candidate model and evaluation report
  - monitoring pipeline emits alerts when thresholds are breached

## Milestone Sequence

1. Kedro project bootstrap and environment/config structure
2. Historical Sydney traffic ingestion and validation
3. Congestion labeling and feature engineering
4. Baseline model training and evaluation
5. Hourly batch inference outputs
6. Data and model monitoring
7. Scheduled and trigger-based retraining
8. Production runbook and hardening

## Assumptions and Defaults

- Sydney traffic data is available in historical batch form before any live integration.
- The first model predicts 1-hour-ahead congestion class, not continuous travel time.
- The first spatial unit is road segment, not suburb or corridor.
- The first production consumer is the internal ML team, so v1 emphasizes reproducibility and monitoring over a public-facing app.
- Retraining defaults to weekly, while inference runs hourly.
- v1 stays fully batch-oriented; no streaming or low-latency online serving is included.
