# AI381 Lab 3 — Churn Prediction MLOps

Dr. Tariq Al-Omari · AI381 Machine Learning Operations · JUST · Fall 2026/27

## Overview

This project implements an end-to-end MLOps workflow for a customer churn prediction service.

It extends the churn API with experiment tracking, candidate model evaluation, automated model promotion, containerization, CI/CD, and deployment verification.

## Current Champion

The selected champion is `logreg_balanced`.

| Metric | Result |
|---|---:|
| ROC-AUC | 0.797 |
| F1-score | 0.589 |
| p95 inference latency | 7.66 ms |
| Model size | 3.55 KB |

The champion improved F1 from 0.495 to 0.589, exceeding the configured minimum improvement threshold.

See [MODEL_SELECTION.md](MODEL_SELECTION.md) for the candidate comparison and selection details.

## Project Features

- FastAPI prediction service with `/health`, `/predict`, and `/metrics`.
- Input validation and data schema checks.
- MLflow experiment tracking using Docker Compose.
- Three candidate models evaluated using performance, latency, model size, and contract-level metrics.
- Automated champion promotion gate.
- MLflow model registration and `@champion` alias.
- Multi-stage Docker image with a non-root runtime.
- GitHub Actions workflows for testing, training, model promotion, image building, scanning, and publishing.
- Docker Compose deployment with a smoke test.

## Setup

Create a virtual environment using `python3 -m venv .venv` and activate it using `source .venv/bin/activate`.

Install development dependencies using `pip install -r requirements-dev.txt`.

## Run Tests and Linting

Run the test suite using `pytest -q`.

Check code quality using `ruff check .`.

## Build the Docker Image

Build the image using:

`docker build --build-arg GIT_SHA=$(git rev-parse HEAD) -t churn-api:local .`

## Local Deployment

The implemented deployment target is local Docker Compose.

Start the API using:

`docker compose -f docker-compose.deploy.yml up -d`

Check the deployment using:

`docker compose -f docker-compose.deploy.yml ps`

Run the smoke test using:

`bash scripts/smoke_test.sh http://localhost:8000`

Stop the deployment using:

`docker compose -f docker-compose.deploy.yml down`

## MLflow and Model Promotion

The MLflow environment is defined in `docker-compose.mlflow.yml`.

The training workflow evaluates three candidate models and records parameters, metrics, a data hash, the model signature, and model artifacts.

The promotion gate checks candidate eligibility and compares the selected candidate against the current champion before promotion.

See `MODEL_SELECTION.md` for the candidate comparison.

## GitHub Actions and Container Registry

GitHub Actions automates testing and model training.

The CI workflow builds the Docker image, runs a smoke test, scans the image with Trivy, and publishes it to GitHub Container Registry (GHCR) when the workflow conditions are met.

Published images are tagged using the Git commit SHA.

## Deployment Scope

The implemented deployment target for this project is local Docker Compose.

The Azure deployment workflow is retained as an example configuration and is not part of the active deployment path.

## Repository Structure

- `.github/workflows/` — CI and training workflows.
- `artifacts/` — Champion model and metadata.
- `data/` — Dataset files.
- `scripts/` — Promotion and smoke-test scripts.
- `src/app/` — FastAPI application.
- `src/train/` — Model training workflows.
- `tests/` — API, model, and data tests.
- `Dockerfile` — Docker image instructions.
- `docker-compose.deploy.yml` — Local API deployment.
- `docker-compose.mlflow.yml` — MLflow services.
- `MODEL_SELECTION.md` — Candidate model comparison.
- `requirements.txt` — Runtime dependencies.
- `requirements-dev.txt` — Development dependencies.
