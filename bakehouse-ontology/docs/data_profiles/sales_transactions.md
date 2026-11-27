# Profile: samples.bakehouse.sales_transactions

* Rows: 3,333 | Size: 68,624 bytes | Files: 1 | Figures: exact (full table; below the 10M-row sampling threshold)
* Grain: one row per sales transaction (one product line per transaction) | Primary key: `transactionID` (verified by pattern C: 0 duplicate keys) | Role: fact
* Update cadence: batch full rewrite (`CREATE OR REPLACE TABLE AS SELECT` + `SET TBLPROPERTIES`), roughly every 1-4 days; last write 2026-09-14 (DESCRIBE HISTORY). Content is a static sample: `dateTime` covers 2024-05-01 to 2024-05-17 only.
* No partitioning or clustering columns. No table or column comments.

## Columns
| column | type | null_rate | distinct | uniqueness_ratio | role (key, fk, measure, dimension, date, text) | comment |
|---|---|---|---|---|---|---|
| transactionID | bigint | 0.0 | 3333 | 1.0 | key | |
| customerID | bigint | 0.0 | 300 | 0.09 | fk -> sales_customers.customerID | |
| franchiseID | bigint | 0.0 | 48 | 0.014 | fk -> sales_franchises.franchiseID | |
| dateTime | timestamp | 0.0 | 3333 | 1.0 | date | transaction timestamp |
| product | string | 0.0 | 6 | 0.002 | dimension | |
| quantity | bigint | 0.0 | 25 | 0.008 | measure | units in the transaction |
| unitPrice | bigint | 0.0 | 1 | 0.0003 | measure (constant) | always 3 |
| totalPrice | bigint | 0.0 | 25 | 0.008 | measure | equals quantity x unitPrice on every row |
| paymentMethod | string | 0.0 | 3 | 0.001 | dimension | |
| cardNumber | bigint | 0.0 | 3333 | 1.0 | text (sensitive) | payment card number; never expose |

## Value distributions
* `product` (6): Golden Gate Ginger 586 (17.58%), Tokyo Tidbits 583 (17.49%), Outback Oatmeal 561 (16.83%), Pearly Pies 550 (16.5%), Austin Almond Biscotti 530 (15.9%), Orchard Oasis 523 (15.69%).
* `paymentMethod` (3): mastercard 1,144 (34.32%), amex 1,106 (33.18%), visa 1,083 (32.49%). Stored lower case.
* `quantity`: min 1, max 60, avg 6.65. Values 1-10 cover 89.8% of rows; long tail of multiples (12-60).
* `unitPrice`: min 3, max 3 (single value on 100% of rows).
* `totalPrice`: min 3, max 180, avg 19.94. Sum 66,471; sum of `quantity` 22,157.
* `dateTime`: min 2024-05-01 00:03:28, max 2024-05-17 11:48:38; 17 distinct days.
* `transactionID`: 1000000 to 1003332 (contiguous).
* `cardNumber`: counts only (sensitive); 1 distinct value per row.

## Keys and joins
| column | references | orphan_rate | verified_cardinality |
|---|---|---|---|
| customerID | samples.bakehouse.sales_customers.customerID | 0.0 | many_to_one |
| franchiseID | samples.bakehouse.sales_franchises.franchiseID | 0.0 | many_to_one |

Max rows per key on this side: 22 per customer, 90 per franchise. Every customer (300/300) and every franchise (48/48) has at least one transaction.

## Lineage
No upstream or downstream tables recorded in `system.access.table_lineage` (last 90 days); only notebook read events.

## Usage
`system.query.history` (last 30 days, FINISHED, statement text matching the table name): 0 queries, 0 users. No join or filter columns observed.

## Data quality flags
* `unitPrice` is constant (3); a price dimension or average-unit-price measure carries no variation. Use the weighted definition (revenue / units).
* `totalPrice = quantity * unitPrice` on all 3,333 rows (0 mismatches): use `totalPrice` directly for revenue.
* `cardNumber` is sensitive (unique per row): exclude from metric view dimensions.
* `paymentMethod` values are lower case; display capitalisation is a presentation concern.
* No nulls in any column.

## Materialization signals
* `hot_table`: hosts the core revenue, units, transactions and AOV KPIs (requirements document). Usage history is empty, so the flag comes from KPI importance only.
* `multi_join`: supplier-level sales need transactions -> franchises -> suppliers (3 tables).
* `filter_columns` (categorical): `product`, `paymentMethod`; date grain from `dateTime`.
* `large_table`: no (3,333 rows).
