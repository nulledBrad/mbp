# Session 01 (wave 0): Inputs Check & Report

Run `892325824190755`, branch `bw-bakehouse-test` (Git folder verified on branch at `20954e97`), mode `edit`, 2026-11-27.

## What was done

1. Loaded `sdk_config.json` with `config.load_config` and ran `config.validate_config`: 0 errors.
2. Ran `config.expand_tables(cfg, WorkspaceClient())`: 5 tables (`samples.bakehouse.media_customer_reviews`, `sales_customers`, `sales_franchises`, `sales_suppliers`, `sales_transactions`).
3. Ran `config.check_access(cfg, ws)`: 0 problems. Additionally confirmed each table returns a row with `SELECT * LIMIT 1`, and fetched column counts (4, 12, 10, 10, 10).
4. Confirmed target catalog `genie_sdk_bakehouse` exists; supplemental dir `/Volumes/genie_sdk_bakehouse/inputs/requirements/` is listable and holds `bakehouse_business_requirements.md`. No dashboard inputs configured.
5. Wrote `docs/inputs_report.md` with sections Config, Source tables, Supplemental inputs, Access problems (`none`), Notes, Date.

## Decisions and findings

* The vendored SDK copy (`ontology-metric-views/src/genie_ontology_modeled_knowledge_sdk_vendored/`) has no `config` module, and the SDK is not installed on serverless. Imported `genie_ontology_modeled_knowledge_sdk.config` from the SDK source in the same Git folder (`genie-sdk/mbp/genie-ontology-modeled-knowledge-sdk/src`, same branch). The gate's `validate_config` check will need the same `PYTHONPATH` (the gate text names `ontology-metric-views/src`, which does not contain `config`). Candidate SDK fix: vendor `config.py` or adjust the gate text.
* Target schema `genie_sdk_bakehouse.ontology_bakehouse` does not exist yet. Expected: the metric views bundle creates it on deploy (wave 3). Not created here (read-only scope).

## Checks

| Check | Result |
|---|---|
| `validate_config` returns 0 errors | pass |
| `check_access` returns 0 problems | pass |
| Report lists every expanded table (re-parsed file) | pass |
| `## Access problems` section is exactly `none` | pass |

## Files

* `docs/inputs_report.md` (created)

No Unity Catalog objects created or modified. No Git commands run.

## Requests

None blocking. Owner/SDK note: the vendored package lacks `config`; see above.
