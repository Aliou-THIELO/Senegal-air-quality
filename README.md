# senegal-air-quality

> *Résumé : collecte quotidienne, contrôle qualité et prévision du PM2.5 (1, 3 et 6 h)
> à partir des capteurs bas coût OpenAQ au Sénégal. Projet en construction : seule la
> mise en place (jalon 0) est terminée. Les hypothèses de départ viennent d'une
> exploration préliminaire, décrite dans `docs/exploration.md`.*

Daily collection, quality control and short-term forecasting (1 h, 3 h, 6 h) of PM2.5
from low-cost OpenAQ stations in Senegal.

**Status:** under construction. Milestone 0 (project setup) is done.

## Context

Fine particulate matter (PM2.5) is a major air-pollution risk for health.
The stations available through OpenAQ in Senegal are low-cost sensors (AirGradient).
They are cheap and dense, but noisy: spikes, exact zeros and gaps are common.

## Problem

Can low-cost sensor data from Senegal be cleaned reliably enough to forecast PM2.5
a few hours ahead, and does the forecast beat simple baselines?

## Objectives

- Build a reproducible ingestion pipeline for the OpenAQ API, run every morning
- Apply and document explicit data-quality rules (spikes, zeros, short gaps)
- Forecast PM2.5 at 1 h, 3 h and 6 h and compare against baselines
- Package the pipeline with Docker Compose, then deploy it to the cloud
- Expose the data and forecasts through an API and a BI dashboard

## Planned stack

| Tool | Role | Status |
|---|---|---|
| Python, pandas, Pydantic | Ingestion, validation, rejects zone | Planned |
| Parquet | Storage (local, then S3) | Planned |
| Docker, Docker Compose | Reproducible local run | Planned |
| GitHub Actions | CI (lint, tests) | Planned (next step) |
| dbt | Transformations and data tests | Planned |
| Airflow | Orchestration, daily run | Planned |
| MLflow | Experiment tracking, model registry | Planned |
| FastAPI | Forecast API | Planned |
| Power BI | Dashboard on the clean tables | Planned |
| AWS | Cloud deployment, scheduled run | Planned |
| Databricks | Portability test | Stretch goal, if time allows |

## Data

- Source: OpenAQ API, 7 low-cost stations (Ziguinchor, Bignona, Thiès, Rufisque, UAM)
- About one year of hourly history at the time of the exploration
- Raw data is never committed (`data/` is git-ignored)
- OpenAQ data licences must be checked before any redistribution

## Approach

- Quality control: mask extreme spikes, treat exact zeros as missing, fill only short
  gaps (3 hours at most)
- Model: gradient boosting (scikit-learn `HistGradientBoostingRegressor`), one model
  per horizon
- Evaluation (planned): strict temporal split, causal features only (no interpolation
  using future values), baselines (persistence and hour-of-day profile), and checks on
  stations unseen during training

## Scheduling (planned)

The pipeline is meant to run once a day in the morning. Ingestion restarts from the
last stored hour and is idempotent, so a missed run is recovered by the next one.

## Roadmap

- [x] Milestone 0: repository and tooling
- [ ] Milestone 1: ingestion (validation, rejects zone, idempotent reruns)
- [ ] Quality-control rules and a per-station report
- [ ] Minimal end-to-end version (model vs baselines, simple API)
- [ ] Daily scheduling, orchestration and transformations (Airflow, dbt)
- [ ] Model tracking and monitoring (MLflow, drift detection)
- [ ] BI dashboard (Power BI)
- [ ] Deployment: local first, then AWS (Databricks only if time allows)

Results will be published here once the evaluation is done with this repository's code.

## Working assumptions and limits

These come from a preliminary exploration, described in
[docs/exploration.md](docs/exploration.md). This repository's code will re-check them.

- Low-cost sensors, not validated against reference instruments
- About one year of data: one dry season and one rainy season
- 4 of the 7 selected stations are in the south; central Dakar is not covered
  (to be confirmed with the station coordinates)
- Exact zeros are treated as missing values (suspected sensor faults)
- Station availability changes over time: one station sent very few measurements recently
- OpenAQ station licences not yet verified

## Development

## Development

```bash
python -m venv .venv
source .venv/Scripts/activate   # Git Bash on Windows
python -m pip install -e ".[dev]"
ruff check .
pytest -q
```

## Conventions

Code, documentation and commit messages are in English. Commit messages follow
Conventional Commits (`feat:`, `fix:`, `docs:`, `chore:`).