# Session 02 (wave 1): Domain Research & Glossary

Run `892325824190755`, branch `bw-bakehouse-test` (Git folder verified on branch at `6427cf38`), mode `edit`, continuation 0, 2026-11-27.

## What was done

1. Read `sdk_config.json`, `PROJECT_MEMORY.md`, `docs/inputs_report.md`, the plan index, plan 02 and the session 01 summary. Loaded the `ontology-domain-research` skill.
2. Read metadata for the 5 source tables with `readTable` (columns, table comment, date-range statistics). No data scans.
3. Gathered sources. No web-search tool was attached to this session, so research used direct HTTP from serverless compute: **3 web searches** in total (1 DuckDuckGo query, which was bot-blocked and returned nothing, plus 2 Wikipedia search API queries), and direct fetches of known reference pages. Every one of the 26 cited URLs was fetched and checked for the cited content before use. Pages that failed (FTC and FDA pages 404, Census and BLS 403, Investopedia 402, several Shopify and Square 404s) are not cited.
4. Wrote `docs/research/01`-`05` and 22 glossary term files in `docs/semantics/`.
5. Ran `parse_glossary('docs/semantics')` and the skill output checklist.

## Decisions and findings

* **File names:** plan 02 names `01_terminology.md` and `04_benchmarks.md`. The skill output contract (and the gate's "01-04 research docs") uses `01_domain_overview.md` and `04_industry_benchmarks.md`. Followed the skill. Conflict noted in the status file.
* **Subdomains (proposal only, nothing created in UC):** `sales` (sales_transactions, sales_customers), `franchise_network` (sales_franchises, sales_suppliers), `customer_feedback` (media_customer_reviews). Estimated 3-5 metric views, so one agent under the limit of 30.
* **Sensitive columns:** `sales_transactions.cardNumber` (PCI DSS cardholder data) and the customer name and contact columns (personal data) should be excluded from metric views and agents. `gender` needs owner approval.
* **Data limits found from metadata:** transactions span about 17 days (2024-05-01 to 2024-05-17 per table statistics) and reviews 2024-05-20 to 2024-05-31. Year-over-year same-store sales and retention are not computable. There are no cost, rating, sentiment, fee or franchisee columns, so CLV (beyond a revenue proxy), NPS, sentiment, franchise fee and franchisee are marked "not yet mapped".
* **Benchmarks:** only one verifiable value was found (ecommerce food and beverage AOV $81, Dynamic Yield via Shopify, August 2026), and it is flagged as not comparable to bakery POS. Everything else is `not found`.
* The SDK `parse_glossary` was imported from the SDK source in the same Git folder (`genie-ontology-modeled-knowledge-sdk/src`), as session 01 did for `config`.

## Checks

| Check | Result |
|---|---|
| Research docs 01-05 exist, each `## Sources` has at least one https link (01: 9, 02: 15, 03: 9, 04: 4, 05: 3) | pass |
| `02` KPI table has a `formula` column | pass |
| `04` uses `not found` where no source exists | pass |
| `05` assigns each of the 5 source tables to exactly one snake_case subdomain | pass |
| `parse_glossary('docs/semantics')` parses all 22 files (22 `.md` files, 22 terms) without error | pass |
| Every term has domain `bakehouse_sales`, a `05` subdomain, an https source, non-empty Definition, Business context and Data usage, and related_terms naming existing terms | pass |
| No workspace identifiers (warehouse id, target catalog, user names, job or run ids, host) in research or semantics files | pass |
| All cited URLs were fetched and verified this session | pass |
| Web searches <= 10 (used 3) | pass |

## Files

* `docs/research/01_domain_overview.md`, `02_kpi_definitions.md`, `03_regulatory_context.md`, `04_industry_benchmarks.md`, `05_domain_subdomain_proposal.md` (created)
* `docs/semantics/*.md`, 22 files (created): revenue, transaction, average_transaction_value, units_sold, average_selling_price, bakery_product, payment_method, customer, repeat_customer_rate, customer_retention_rate, customer_lifetime_value, rfm_segmentation, same_store_sales, personal_data, franchise, franchisee, franchisor, franchise_fee, supplier, customer_review, review_sentiment, net_promoter_score

No Unity Catalog objects created or modified. No Git commands run. No files deleted.

## Requests and notes for later sessions

* Session 04 (EDA): confirm the `sales_transactions` grain (one row per transaction or per line), whether `totalPrice = quantity * unitPrice`, `sales_franchises -> sales_suppliers` cardinality, and the values of `sales_suppliers.approved`.
* Session 05 (MV synthesis): use the KPI table in `02`. Keep ATV and ASP as ratio measures, and exclude `cardNumber` and customer PII columns.
* Owner: decide on an AI-derived review sentiment label, on `gender` as a dimension, and on creating UC domains (opt-in).
* SDK/owner: the session runner has no web-search tool in this build. Research relied on direct HTTP fetches, which worked but cannot reach bot-protected sites.
