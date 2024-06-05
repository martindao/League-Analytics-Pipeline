# League Analytics Pipeline

## Overview
This project orchestrates the end-to-end analytics stack for professional
leagues—scraping schedules, enriching with betting lines, transforming the data
with dbt, and deploying dashboards. Prefect flows coordinate Databricks jobs,
Terraform provisions the Azure infrastructure, and the resulting datasets feed
the BI layer.

## Repository Layout
- `configs/` – shared YAML/JSON settings for schedules, API keys, and alerting.
- `databricks/` – jobs and notebooks executed inside the Databricks workspace.
- `dbt/` – sources, models, and macros that power the curated warehouse layer.
- `flows/` – Prefect deployments (`etl_all_league-deployment.yaml`, etc.) that
  fan out across leagues.
- `resources/` – ARM templates, design docs, and BI artifacts.
- `terraform/` – Infrastructure-as-code for storage, compute, and secrets.

## Environment Setup
1. Install dependencies:
   ```powershell
   pip install -r requirements.txt
   ```
2. Authenticate:
   - `az login` for Azure resources.
   - `prefect cloud login --key <PREFECT_API_KEY>` for orchestration.
   - Configure Databricks CLI (`databricks configure --token`).
3. Export environment variables (examples):
   ```powershell
   $env:LEAGUE_API_KEY = "<key>"
   $env:PREFECT_PROFILE = "prod"
   ```

## Running Workflows
- Trigger a per-league ETL:
  ```powershell
  prefect deployment run etl-per-league/production
  ```
- Refresh the dbt layer locally:
  ```powershell
  cd dbt
  dbt deps && dbt build --profiles-dir profiles
  ```
- Apply infrastructure changes:
  ```powershell
  cd terraform
  terraform init
  terraform plan
  terraform apply
  ```

## Quality & Automation
- Prefect deployments (`flows/*.yaml`) double as reproducible schedules; update
  them whenever you change flow code.
- Use `dbt test` plus `dbt docs generate` to validate models before promoting.
- Terraform changes require code review—run `terraform fmt` and `terraform
  validate` prior to pull requests.
