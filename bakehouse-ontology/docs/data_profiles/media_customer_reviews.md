# Profile: samples.bakehouse.media_customer_reviews

* Rows: 204 | Size: 26,724 bytes | Files: 1 | Figures: exact (full table)
* Grain: one row per customer review of a franchise | Primary key: `new_id` (verified by pattern C: 0 duplicate keys) | Role: fact (event, no numeric measures)
* Update cadence: batch full rewrite (`CREATE OR REPLACE TABLE AS SELECT`), roughly every 1-4 days; last write 2026-09-14 (DESCRIBE HISTORY). Content is static sample data.
* No partitioning or clustering columns. No table or column comments.

## Columns
| column | type | null_rate | distinct | uniqueness_ratio | role (key, fk, measure, dimension, date, text) | comment |
|---|---|---|---|---|---|---|
| review | string | 0.0 | 192 | 0.94 | text (free text) | counts only |
| franchiseID | bigint | 0.0 | 48 | 0.24 | fk -> sales_franchises.franchiseID | |
| review_date | timestamp | 0.0 | 6 | 0.03 | date | only 6 distinct timestamps (batch load times) |
| new_id | int | 0.0 | 204 | 1.0 | key | surrogate id 1-204 |

## Value distributions
* `review_date` (6 distinct): 2024-05-20 17:17:03 (48 rows), 2024-05-20 17:24:06 (48), 2024-05-31 15:13:36 (48), 2024-05-31 15:23:53 (20), 2024-05-31 18:42:07 (20), 2024-05-31 18:45:07 (20). Range 2024-05-20 to 2024-05-31.
* `franchiseID`: 48 distinct (all franchises), 3000000 to 3000047; 1 to 6 reviews per franchise.
* `new_id`: 1 to 204 (contiguous).
* `review`: free text, 192 distinct values for 204 rows (some repeated texts across franchises); not profiled beyond counts. 0 rows are exact duplicates on (franchiseID, review_date, review).

## Keys and joins
| column | references | orphan_rate | verified_cardinality |
|---|---|---|---|
| franchiseID | samples.bakehouse.sales_franchises.franchiseID | 0.0 | many_to_one |

No `customerID` or `transactionID`: reviews cannot be linked to customers or transactions.

## Lineage
No upstream or downstream tables recorded in `system.access.table_lineage` (last 90 days); only notebook read events.

## Usage
`system.query.history` (last 30 days): 0 queries, 0 users.

## Data quality flags
* `review_date` looks like a batch load timestamp (6 values shared by 20-48 rows each), not the date each review was written. Day-level review trends are unreliable; treat it as a coarse period only.
* `review_date` (2024-05-20 to 05-31) does not overlap the transaction period (2024-05-01 to 05-17): a shared date filter across sales and reviews returns empty sides.
* No rating or sentiment column (requirements: review count only; sentiment scoring out of scope).
* `review` is free text: never surface row values in metric view samples.

## Materialization signals
* `fan_out`: joins many_to_one into `sales_franchises`, which `sales_transactions` also joins; keep review measures in their own metric view (or aggregate to franchise before combining) to avoid double counting.
* `filter_columns`: none categorical in this table (franchise attributes come from the join).
* `large_table`: no.
