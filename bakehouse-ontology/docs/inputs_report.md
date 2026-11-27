# Inputs Report: bakehouse-ontology (domain `bakehouse_sales`)

Written by build session 01 (wave 0). Later waves read the source table list from this file.

## Config

`sdk_config.json` passes `genie_ontology_modeled_knowledge_sdk.config.validate_config` with 0 errors.

| Field | Value |
|---|---|
| `target_catalog` | `genie_sdk_bakehouse` |
| `target_schema` | `ontology_bakehouse` |
| `domain_name` | `bakehouse_sales` |
| `domain_context` | Bakehouse franchise sales: customers buy bakery products at franchise locations supplied by ingredient suppliers, and leave reviews. |
| `source_tables` | 5 tables (listed below) |
| `source_catalogs` / `source_schemas` | none |
| `source_catalog_for_reads` | not set |
| `supplemental_volume_dirs` | `/Volumes/genie_sdk_bakehouse/inputs/requirements/` |
| `dashboard_inputs` | none |
| `warehouse_id` | `c86f5b750db10e6c` |
| `max_metric_views_per_agent` | 30 |

## Source tables

Expanded with `expand_tables(cfg, ws)`: 5 tables. Each resolved with `tables.get` and returned a row for `SELECT * ... LIMIT 1`.

* `samples.bakehouse.media_customer_reviews`: 4 columns (MANAGED)
* `samples.bakehouse.sales_customers`: 12 columns (MANAGED)
* `samples.bakehouse.sales_franchises`: 10 columns (MANAGED)
* `samples.bakehouse.sales_suppliers`: 10 columns (MANAGED)
* `samples.bakehouse.sales_transactions`: 10 columns (MANAGED)

## Supplemental inputs

* `/Volumes/genie_sdk_bakehouse/inputs/requirements/`: listable, 1 file: `bakehouse_business_requirements.md` (3,955 bytes).
* Dashboard inputs: none configured.

## Access problems

none

## Notes

* `check_access(cfg, ws)` returned 0 items (source tables, target catalog `genie_sdk_bakehouse`, supplemental volume dir).
* The target schema `genie_sdk_bakehouse.ontology_bakehouse` does not exist yet. This is expected: the `ontology-metric-views` bundle defines it (`resources/ontology_schema.yml`, with dev-mode name prefixing) and creates it on deploy in wave 3. This session is read-only and did not create it.

## Date

2026-11-27
