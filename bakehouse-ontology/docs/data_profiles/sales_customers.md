# Profile: samples.bakehouse.sales_customers

* Rows: 300 | Size: 20,054 bytes | Files: 1 | Figures: exact (full table)
* Grain: one row per customer | Primary key: `customerID` (verified by pattern C: 0 duplicate keys) | Role: dimension
* Update cadence: batch full rewrite (`CREATE OR REPLACE TABLE AS SELECT`), roughly every 1-4 days; last write 2026-09-14 (DESCRIBE HISTORY). Content is static sample data.
* No partitioning or clustering columns. No table or column comments.

## Columns
| column | type | null_rate | distinct | uniqueness_ratio | role (key, fk, measure, dimension, date, text) | comment |
|---|---|---|---|---|---|---|
| customerID | bigint | 0.0 | 300 | 1.0 | key | |
| first_name | string | 0.0 | 180 | 0.6 | text (sensitive) | never expose |
| last_name | string | 0.0 | 216 | 0.72 | text (sensitive) | never expose |
| email_address | string | 0.0 | 300 | 1.0 | text (sensitive) | never expose |
| phone_number | string | 0.0 | 300 | 1.0 | text (sensitive) | never expose |
| address | string | 0.0 | 300 | 1.0 | text (sensitive) | never expose |
| city | string | 0.0 | 297 | 0.99 | dimension (customer geography) | near-unique; counts only |
| state | string | 0.0 | 98 | 0.33 | dimension (customer geography) | |
| country | string | 0.0 | 3 | 0.01 | dimension (customer geography) | |
| continent | string | 0.0 | 3 | 0.01 | dimension (customer geography) | |
| postal_zip_code | bigint | 0.0 | 300 | 1.0 | text (sensitive) | counts only |
| gender | string | 0.0 | 2 | 0.007 | dimension | |

## Value distributions
* `country` (3): Australia 108 (36.0%), USA 99 (33.0%), Japan 93 (31.0%).
* `continent` (3): Oceania 108 (36.0%), North America 99 (33.0%), Asia 93 (31.0%). Maps one-to-one onto `country`.
* `gender` (2): female 151 (50.33%), male 149 (49.67%).
* `state` (98, top values): Washington 10, Arizona 8, Hawaii 7, Oregon 7, Nevada 7, Kentucky 7; mostly 2-6 customers per state. Mix of full names and abbreviations (for example both `Mississippi` and `MS`).
* `customerID`: 2000000 to 2000299 (contiguous).
* Sensitive columns (`first_name`, `last_name`, `email_address`, `phone_number`, `address`, `postal_zip_code`, `city`): counts only.

## Keys and joins
| column | references | orphan_rate | verified_cardinality |
|---|---|---|---|
| customerID | referenced by samples.bakehouse.sales_transactions.customerID | 0.0 (transactions side) | many_to_one (transactions -> customers) |

All 300 customers have at least one transaction (max 22 per customer).

## Lineage
No upstream or downstream tables recorded in `system.access.table_lineage` (last 90 days); only notebook read events.

## Usage
`system.query.history` (last 30 days): 0 queries, 0 users.

## Data quality flags
* `state` mixes full names and two-letter abbreviations (`MS` alongside `Mississippi`): state-level customer counts split. Normalise or avoid `state` as a dimension.
* Customer `country` uses `USA`; `sales_franchises.country` uses `US`. Do not compare the two columns as equal strings.
* Five PII columns plus `postal_zip_code`: exclude from metric view dimensions (requirements privacy rule).
* No nulls.

## Materialization signals
* none (300 rows; dimension). Categorical candidate dimensions for "customers from" questions: `country`, `continent`, `gender`.
