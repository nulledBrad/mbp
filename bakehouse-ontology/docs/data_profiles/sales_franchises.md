# Profile: samples.bakehouse.sales_franchises

* Rows: 48 | Size: 5,472 bytes | Files: 1 | Figures: exact (full table)
* Grain: one row per franchise location | Primary key: `franchiseID` (verified by pattern C: 0 duplicate keys) | Role: dimension
* Update cadence: batch full rewrite (`CREATE OR REPLACE TABLE AS SELECT`), roughly every 1-4 days; last write 2026-09-14 (DESCRIBE HISTORY). Content is static sample data.
* No partitioning or clustering columns. No table or column comments.

## Columns
| column | type | null_rate | distinct | uniqueness_ratio | role (key, fk, measure, dimension, date, text) | comment |
|---|---|---|---|---|---|---|
| franchiseID | bigint | 0.0 | 48 | 1.0 | key | |
| name | string | 0.0 | 46 | 0.96 | dimension | not unique (2 names used twice) |
| city | string | 0.0 | 47 | 0.98 | dimension | franchise location |
| district | string | 0.0 | 48 | 1.0 | dimension | |
| zipcode | string | 0.0 | 48 | 1.0 | dimension | mixed formats by country |
| country | string | 0.0 | 9 | 0.19 | dimension | franchise location |
| size | string | 0.0 | 5 | 0.10 | dimension | ordinal S < M < L < XL < XXL |
| longitude | double | 0.0 | 48 | 1.0 | dimension (geo) | |
| latitude | double | 0.0 | 48 | 1.0 | dimension (geo) | |
| supplierID | bigint | 0.0 | 48 | 1.0 | fk -> sales_suppliers.supplierID | 21 of 48 have no match |

## Value distributions
* `country` (9): Japan 20 (41.67%), US 16 (33.33%), Australia 6 (12.5%), France 1, Germany 1, Netherlands 1, Italy 1, Canada 1, Sweden 1.
* `size` (5): S 10, M 10, XL 10, L 9, XXL 9.
* `name` (46 distinct): `Sugar Rush` and `Sweet Temptations` each appear on 2 franchises; all other names unique.
* `city`: 47 distinct for 48 franchises (one city has two franchises).
* `longitude`: -157.86 to 153.43; `latitude`: -37.80 to 59.31.
* `franchiseID`: 3000000 to 3000047; `supplierID`: 4000000 to 4000047 (one distinct supplierID per franchise).

## Keys and joins
| column | references | orphan_rate | verified_cardinality |
|---|---|---|---|
| franchiseID | referenced by samples.bakehouse.sales_transactions.franchiseID | 0.0 (transactions side) | many_to_one (transactions -> franchises) |
| franchiseID | referenced by samples.bakehouse.media_customer_reviews.franchiseID | 0.0 (reviews side) | many_to_one (reviews -> franchises) |
| supplierID | samples.bakehouse.sales_suppliers.supplierID | 0.4375 | one_to_one |

* Every franchise has transactions (max 90 per franchise) and reviews (max 6 per franchise).
* `supplierID` is unique on this table, so franchise -> supplier is one_to_one on matched keys (27 matched). 21 franchises (43.75%) reference supplierIDs 4000027-4000047, which do not exist in `sales_suppliers`.

## Lineage
No upstream or downstream tables recorded in `system.access.table_lineage` (last 90 days); only notebook read events.

## Usage
`system.query.history` (last 30 days): 0 queries, 0 users.

## Data quality flags
* Supplier orphans: 21/48 franchises have no supplier row. Those franchises carry 1,480 of 3,333 transactions and 33,537 of 66,471 revenue (50.5%). Supplier join must be LEFT and orphans labelled "Unknown supplier" (requirements rule); an inner join would drop half of revenue.
* `name` is not unique: group by `franchiseID` (with name as a label) to avoid merging two franchises.
* `country` uses `US`, while `sales_customers.country` uses `USA`.
* `size` is ordinal; alphabetical sort is wrong (needs an explicit order S, M, L, XL, XXL).

## Materialization signals
* `fan_out`: two facts (`sales_transactions`, `media_customer_reviews`) join many_to_one into this table; combining sales and review measures at franchise level in one view risks double counting.
* `filter_columns` (categorical): `country`, `size`, `city`.
* `large_table`: no.
