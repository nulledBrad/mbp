# Session 05 (wave 2): Metric View YAML Synthesis

Run `892325824190755`, branch `bw-bakehouse-test` (Git folder verified on branch, read-only check), mode `edit` + allow_execute, continuation 0, 2026-11-27. Skills: `ontology-mv-synthesis` plus the platform `metric-views` skill (joins-correctness, semantic-metadata, materialization, level-of-detail).

## What was done

1. Read sdk_config, PROJECT_MEMORY, the plan 05 file, the session 02/03/04 summaries, `docs/research/02` and `05`, the context requirements doc and all 5 profiles plus `data_model_summary.md`.
2. Wrote 4 metric views under `ontology-metric-views/metric_views/bakehouse_sales/`, each `version: 1.1` with a `# subdomain:` header, tokenized sources only.
3. Ran the validator, a read-only dry check (parse, `render_ddl` with dev params, column existence, EXPLAIN of the customer SQL source) and a KPI cross-check against the requirements expected answers.
4. Built `manifest.json` with `manifest.build_manifest` and `write_manifest`, then copied the output byte-for-byte into the workspace file with the file tools. The checks confirm it is identical to `build_manifest` output.

## Metric views

| view | subdomain | grain / source | joins (verified row in data_model_summary) | materialization |
|---|---|---|---|---|
| mv_bakehouse_sales_transactions | sales | one row per transaction (`sales_transactions`) | customers m:1 (0.0), franchises m:1 (0.0), nested franchises -> suppliers 1:1 (orphan 0.4375, LEFT semantics, COALESCE 'Unknown supplier') | yes: 1 aggregated entry (Product, Payment Method, Franchise Country, Franchise Size, Supplier Name x 5 additive/distinct measures), `refresh_policy: auto`, `cluster_by: {auto: true}`. Threshold: 3 joins incl. nested + headline KPIs. Window share measures are left out of the entry |
| mv_bakehouse_sales_customers | sales | one row per customer (SQL source: transactions aggregated by customerID over all data) | customers m:1 (transactions -> customers row; source is one row per customerID) | no |
| mv_bakehouse_franchise_network | franchise_network | one row per franchise (`sales_franchises`) | suppliers 1:1 (orphan 0.4375) | no |
| mv_bakehouse_customer_reviews | customer_feedback | one row per review (`media_customer_reviews`) | franchises m:1 (0.0) | no |

The fan-out risk is avoided: sales and review measures are in separate views. No reviews-to-transactions join exists.

## KPI -> metric view mapping

| KPI (research 02 / context) | metric view | measure |
|---|---|---|
| Revenue | sales_transactions | Revenue |
| Units sold | sales_transactions | Units Sold |
| Transactions | sales_transactions | Transactions |
| Average order / transaction value | sales_transactions | Average Order Value |
| Average unit / selling price (weighted) | sales_transactions | Average Unit Price |
| Active customers | sales_transactions | Active Customers |
| Revenue per franchise | sales_transactions | Revenue per Franchise (with Franchises With Sales) |
| Product revenue share / product mix | sales_transactions | Product Revenue Share (window `range: all` over Product) |
| Revenue share by payment method | sales_transactions | Payment Method Revenue Share |
| Repeat customer rate | sales_customers | Repeat Customer Rate |
| Customer lifetime value (revenue proxy) | sales_customers | Revenue per Customer (also a period-aware version in sales_transactions) |
| RFM (partial: recency/frequency/monetary dimensions) | sales_customers | Last Purchase Date, Customer Transactions, Purchase Frequency Band, Customer Revenue |
| Approved supplier share | franchise_network | Approved Supplier Share |
| Franchise count by size (Q7) | franchise_network | Franchises |
| Review count | customer_reviews | Reviews |
| Same-store sales growth, customer retention rate | not modeled | needs a prior period; data spans 17 days |
| NPS, review sentiment share, franchise fee | not modeled | no rating, sentiment or fee data; sentiment is out of scope |

## Decisions

* Excluded `cardNumber`, customer names, email, phone, address, postal code, city and `state` (mixed vocabularies). `gender` is excluded until the owner approves it (session 02).
* Franchise country (`US`) and customer country (`USA`) are separate dimensions. The bare synonym `country` is on franchise country only (requirements default geography rule).
* Payment method is displayed with `INITCAP`. Franchise Size Rank gives the S..XXL sort order. Weeks use `DATE_TRUNC('WEEK')` (Monday start).
* Revenue has no currency `format`: the source does not state a currency, so I didn't assume USD. Averages use a 2-decimal number format and shares use percentage.
* The approved-supplier rule is a filtered measure and a Supplier Status dimension, not a top-level filter. A top-level filter would drop unknown-supplier franchises (50.5% of revenue).
* Product Revenue Share uses an Exclude-LOD window measure (`order: Product, range: all`), so other filters apply to the denominator. The context doc marks this KPI `needs review`. Registration (session 06) is the first runtime test of the window measures.

## Checks

| Check | Result |
|---|---|
| `PYTHONPATH=ontology-metric-views/src python -m genie_ontology_modeled_knowledge_sdk_vendored.mv_yaml validate ontology-metric-views/metric_views` (gate form) | pass: 4 files, 0 errors, 0 warnings, exit 0 |
| Same with SDK src `genie_ontology_modeled_knowledge_sdk.mv_yaml` | pass: 0 errors, 0 warnings, exit 0 |
| `render_ddl` with dev params leaves no `${`; no literal `samples.` in any YAML | pass (4/4) |
| Referenced columns exist in source tables | pass. The checker flagged `suppliers.supplierID` on the nested join, but that is my checker not resolving the nested alias; the column exists per the suppliers profile |
| EXPLAIN of the customer view SQL source (rendered) | pass |
| KPI cross-check (read-only SQL of the measure formulas) | pass: revenue 66,471; units 22,157; transactions 3,333; AOV 19.94; active customers 300; payment Mastercard 1,144 / Amex 1,106 / Visa 1,083; sizes S 10, M 10, L 9, XL 10, XXL 9; unknown-supplier revenue 33,537; approved-supplier franchises 27; reviews 204 |
| `manifest.json`: 4 entries for 4 YAMLs, sorted, `validate_manifest_json` returns no problems, equals `build_manifest` output | pass |
| `bundle validate --strict --target dev` ontology-metric-views | pass |
| `bundle validate --strict --target dev` ontology-genie-agents | pass (run without a render step; I did not check the README for one) |
| Metric view DDL executed | skipped (not in scope; registration is session 06) |

## Findings for later sessions and the owner

* **SDK bug (vendored copy):** `ontology-metric-views/src/genie_ontology_modeled_knowledge_sdk_vendored/manifest.py` imports `genie_ontology_modeled_knowledge_sdk.mv_yaml` (the non-vendored name). With only `PYTHONPATH=ontology-metric-views/src`, `import ..._vendored.manifest` fails with ModuleNotFoundError. The vendored `mv_yaml` CLI works. I built the manifest with the SDK src in the Git folder. This matters to anything that imports the vendored manifest (e.g. the bundle's generate_manifest notebook). Logged as an owner request.
* Repeat customers = 300 of 300, so Repeat Customer Rate is 100% on this sample.
* Session 06: the transactions view has a materialization block. Registration has to support materialized metric views (needs a serverless pipeline). If it fails there, drop the block, which is optional for performance at 3,333 rows. Window measures (two share measures) are also first exercised at registration.
* Session 10: Q1-Q9 map directly to measures above. For Q10, group mv_bakehouse_customer_reviews by Franchise ID + Franchise Name.

## Files

* `ontology-metric-views/metric_views/bakehouse_sales/mv_bakehouse_sales_transactions.yml` (created)
* `ontology-metric-views/metric_views/bakehouse_sales/mv_bakehouse_sales_customers.yml` (created)
* `ontology-metric-views/metric_views/bakehouse_sales/mv_bakehouse_franchise_network.yml` (created)
* `ontology-metric-views/metric_views/bakehouse_sales/mv_bakehouse_customer_reviews.yml` (created)
* `ontology-metric-views/metric_views/manifest.json` (created)

Read-only SQL only (DESCRIBE, SELECT, EXPLAIN). No Unity Catalog objects created, no DDL, no Git commands, no files deleted.
