# sml-salesinsights-gbq

AtScale SML catalog "Sales Insights - gbq": the AdventureWorks Internet Sales demo on Google BigQuery.

This repository holds the BigQuery version of the Sales Insights demo, written in SML (Semantic Modeling Language, the YAML format that AtScale uses to define a catalog as code). The shared customer, date, geography, gender, and product objects come from the package [sml-salesinsights-common](https://github.com/muellermarco/sml-salesinsights-common). The repository is connected to the [atscale-cicd-pipeline](https://github.com/muellermarco/atscale-cicd-pipeline).

## Technologies

- AtScale 2026.9.2
- sml-cli 2025.12.0 (pinned in the pipeline's `validate` workflow)
- SML catalog `version: 1.0` (see `atscale.yml`)
- Google BigQuery, through the AtScale connection `gbq`
- GitHub Actions (reusable workflows from `muellermarco/atscale-cicd-pipeline`)

## Catalog at a glance

| Item | Value |
|---|---|
| Catalog | `Sales Insights - gbq`. The catalog file is `atscale.yml`, not `catalog.yml`. |
| Connection | AtScale connection `gbq`, defined as `common_connection` in `connections/GBQ.yml`. Project and dataset: see that file. |
| Package | `Common-Objects` from sml-salesinsights-common, branch `gbq`, commit `475b8f8` (see `package.yml`) |
| Model settings | `model_settings.yml` disables demand-defined aggregates for the `Shipments` model. |

## Models

| Model | Label | Purpose |
|---|---|---|
| `Internet Sales` | Internet Sales2 | Sales metrics by customer, date, order, and product |
| `Deliveries` | Deliveries | Subset of 18 metrics on the same fact dataset |
| `Shipments` | Shipments | Subset of 18 metrics on the same fact dataset, without demand-defined aggregates |
| `Simple` | Simple | Minimal model with one metric |

## Repository contents

| Path | Count | Contents |
|---|---|---|
| `atscale.yml` | 1 | Catalog definition |
| `model_settings.yml` | 1 | Aggregate settings for `Shipments` |
| `connections/` | 1 | `GBQ.yml` |
| `datasets/` | 2 | `dimorder`, `factinternetsales` |
| `dimensions/` | 5 | `Color Dimension`, `Order Dimension`, `Size Dimension`, `Style Dimension`, `Weight` |
| `metrics/` | 12 | Base metrics, for example sales amount, order quantity, customer count |
| `calculations/` | 133 | Calculated metrics, mainly time-relative (Retail 4-4-5 and custom 4-4-5 periods) |
| `models/` | 4 | See the table above |

The package adds more datasets, dimensions, and row security objects at install time.

## How the pipeline deploys this repo

| Event | Workflow | Result |
|---|---|---|
| Pull request | `validate` | sml-cli installs the package and validates the catalog. No deploy. |
| Merge to `main` | `deploy-qa` | The catalog deploys to the QA instance. |
| Tag `v*` | `release-live` | The catalog deploys to the live instance. |

QA and live deploy the catalog as `Sales Insights - gbq_main`.

## Change the model and release it

1. Create a branch from `main`.
2. Edit the SML files.
3. Install the package: `npx -y sml-cli@2025.12.0 install .`.
4. Validate: `npx -y sml-cli@2025.12.0 validate .`.
   Result: the output contains `Validation SUCCESSFUL`.
5. Open a pull request to `main`.
   Result: the `validate` check is green.
6. Merge the pull request.
   Result: `deploy-qa` deploys the catalog to the QA instance.
7. Push a release tag, for example `git tag v1.1 && git push origin v1.1`.
   Result: `release-live` deploys the catalog to the live instance.

## License

MIT License. See [LICENSE](LICENSE).
