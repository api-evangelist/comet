---
name: Run a dataset-backed evaluation experiment in Opik
description: Create a dataset, add items, then run an experiment that scores model outputs against the dataset.
api: openapi/comet-opik-openapi-original.yml
operations:
- createDataset
- createOrUpdateDatasetItems
- createExperiment
- createExperimentItems
- findDatasetItemsWithExperimentItems
---

# Run a dataset-backed evaluation experiment in Opik

Use this skill to build an evaluation dataset and run an experiment against it.

## Auth
- Opik Cloud: `authorization: <API_KEY>` (no `Bearer `) + `Comet-Workspace: <workspace>`.
- Cloud base URL: `https://www.comet.com/opik/api/v1`.

## Steps
1. **Create a dataset** — `POST /v1/private/datasets` (`createDataset`).
2. **Add dataset items** — `PUT /v1/private/datasets/items` (`createOrUpdateDatasetItems`). Items are keyed by id so the call upserts.
3. **Create an experiment** — `POST /v1/private/experiments` (`createExperiment`), referencing the dataset by `dataset_name`.
4. **Attach experiment items** — `POST /v1/private/experiments/items` (`createExperimentItems`) linking each `experiment_id` to a `dataset_item_id` and the produced `trace_id`.
5. **Review results** — `GET /v1/private/datasets/{id}/items/experiments/items` (`findDatasetItemsWithExperimentItems`) to compare outputs and feedback scores per dataset item.

## Conventions & errors
- Pagination: `page` + `size`; filtering via the `filters` JSON predicate string.
- Errors: `ErrorMessage` `{code, message, details}`; write errors `{errors:[...]}`. Handle 404 (missing dataset/experiment id) and 409 (duplicate).
- See `conventions/comet-conventions.yml`, `data-model/comet-data-model.yml`, and `errors/comet-error-codes.yml`.
