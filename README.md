# data_orchestrator

Public orchestrator for the **private** `marek5050/mlb` data pipeline.

This repo contains no application code. Its only job is to run the daily MLB
data export on GitHub Actions. GitHub Actions minutes are free and unlimited on
public repos, and billing is charged to the repo that *owns* the run — so the
workflow here checks out the private `marek5050/mlb` code transiently onto the
runner (it is never committed to this repo) and runs the pipeline for free.

## How it works

`.github/workflows/daily_export.yml`:
1. Checks out `marek5050/mlb@main` using the `PRIVATE_REPO_PAT` secret.
2. Installs deps with `uv` and runs the same data-refresh + retrain-trigger
   steps that used to live in the private repo's `daily_export.yml`.

Runs twice daily (9:00 and 20:00 UTC, Apr–Oct) plus manual `workflow_dispatch`.

## Required secrets

Set these in **Settings → Secrets and variables → Actions**:

| Secret | Purpose |
|--------|---------|
| `PRIVATE_REPO_PAT` | Fine-grained/classic PAT with **read** access to `marek5050/mlb` (checkout) |
| `PROXY_USER`, `PROXY_PASS` | Scraper proxy |
| `ODDS_API_KEY` | OddsAPI snapshots |
| `SUPABASE_URL`, `SUPABASE_KEY` | Supabase writes |
| `DATABASE_URL` | Direct Postgres (feature builds) |
| `VISUAL_CROSSING_API_KEY` | Historical weather |
| `KALSHI_API_KEY_ID`, `KALSHI_PRIVATE_KEY` | Kalshi player map / scopes |
| `GCP_SA_KEY`, `GCP_PROJECT`, `GCP_LOCATION` | Cloud Run retrain triggers |

## Keeping in sync

The step list mirrors `marek5050/mlb/.github/workflows/daily_export.yml`. Only
the first checkout step differs (it targets the private repo). When the private
pipeline's steps change, mirror them here.
