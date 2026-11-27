# Profile: samples.bakehouse.sales_suppliers

* Rows: 27 | Size: 4,565 bytes | Files: 1 | Figures: exact (full table)
* Grain: one row per ingredient supplier | Primary key: `supplierID` (verified by pattern C: 0 duplicate keys) | Role: dimension
* Update cadence: batch full rewrite (`CREATE OR REPLACE TABLE AS SELECT`), roughly every 1-4 days; last write 2026-09-14 (DESCRIBE HISTORY). Content is static sample data.
* No partitioning or clustering columns. No table or column comments.

## Columns
| column | type | null_rate | distinct | uniqueness_ratio | role (key, fk, measure, dimension, date, text) | comment |
|---|---|---|---|---|---|---|
| supplierID | bigint | 0.0 | 27 | 1.0 | key | |
| name | string | 0.0 | 27 | 1.0 | dimension | supplier business name |
| ingredient | string | 0.0 | 27 | 1.0 | dimension | one ingredient per supplier |
| continent | string | 0.0 | 6 | 0.22 | dimension | supplier location |
| city | string | 0.0 | 27 | 1.0 | dimension | supplier location |
| district | string | 0.0 | 27 | 1.0 | dimension | |
| size | string | 0.0 | 5 | 0.19 | dimension | ordinal S < M < L < XL < XXL |
| longitude | double | 0.0 | 27 | 1.0 | dimension (geo) | |
| latitude | double | 0.0 | 27 | 1.0 | dimension (geo) | |
| approved | string | 0.0 | 1 | 0.04 | dimension (flag) | always `Y` |

## Value distributions
* `continent` (6): Asia 11 (40.74%), Europe 5 (18.52%), South America 4 (14.81%), Africa 3 (11.11%), North America 3 (11.11%), Central America 1 (3.7%).
* `size` (5): M 7, S 7, XL 5, L 5, XXL 3.
* `approved` (1): Y 27 (100%).
* `ingredient`: 27 distinct (for example raisins, ginger, sesame seeds, cacao, vanilla); one per supplier.
* `longitude`: -99.13 to 128.18; `latitude`: -23.55 to 55.95.
* `supplierID`: 4000000 to 4000026 (contiguous).

## Keys and joins
| column | references | orphan_rate | verified_cardinality |
|---|---|---|---|
| supplierID | referenced by samples.bakehouse.sales_franchises.supplierID | 0.4375 (franchises side) | one_to_one (franchises -> suppliers) |

Every supplier (27/27) is referenced by exactly one franchise. There is no `country` column (only continent, city, district).

## Lineage
No upstream or downstream tables recorded in `system.access.table_lineage` (last 90 days); only notebook read events.

## Usage
`system.query.history` (last 30 days): 0 queries, 0 users.

## Data quality flags
* Coverage gap: franchises reference supplierIDs 4000027-4000047 that are missing here (21 franchises, 50.5% of revenue). Join from franchises with LEFT JOIN and label missing rows "Unknown supplier".
* `approved` is constant `Y`: an approved-supplier filter is a no-op on this data, but keep the rule (requirements) for future data.
* `size` is ordinal; alphabetical sort is wrong.

## Materialization signals
* `multi_join`: supplier sales KPIs need transactions -> franchises -> suppliers (3 tables).
* `filter_columns` (categorical): `continent`, `ingredient`, `approved`.
* `large_table`: no.
