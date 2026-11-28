# Metric View Registration Results

Build run: `892325824190755` (started 2026-11-27 16:23:24 UTC). Session 06, wave 3.

## register_metric_views run (current result)

| Field | Value |
|---|---|
| Job | `register_metric_views` (dev), job id 967660018526725 |
| Run id | 598581036511952 |
| Start | 2026-11-27 22:26:54 UTC (after the build run started) |
| End | 2026-11-27 22:28:41 UTC |
| Result state | **SUCCESS** |
| Code | branch `bw-bakehouse-test` at ebb59e0 (register_single_mv fix) |

| Task | Result | Output |
|---|---|---|
| generate_manifest | SUCCESS | 4 metric views, 0 warnings |
| register_each_mv (for_each) | SUCCESS | 4 iterations, each SUCCESS on attempt 0 |
| smoke_test | SUCCESS | 4 checked, 4 passed, failed: none |
| cardinality_test | SUCCESS | 4 checked, failed: none |

## Per-view registration

| Metric view | Registered | Columns |
|---|---|---|
| `${target_catalog}.${target_schema}.mv_bakehouse_customer_reviews` | yes | 10 |
| `${target_catalog}.${target_schema}.mv_bakehouse_franchise_network` | yes | 17 |
| `${target_catalog}.${target_schema}.mv_bakehouse_sales_customers` | yes | 14 |
| `${target_catalog}.${target_schema}.mv_bakehouse_sales_transactions` | yes | 30 |

All 4 `manifest.json` entries are registered (iteration exit values report `status: registered`) and all 4 are present in Unity Catalog as METRIC_VIEW. Note: `mv_bakehouse_sales_transactions` shows a later `last_altered` (2026-11-28 13:09 UTC) than the run; something changed it after registration (likely its materialization). This does not affect the run result.

## History: first run (failed)

Run 830843314683487 (2026-11-27 17:08:54 to 17:11:19 UTC) FAILED. All 8 `register_single_mv` iteration runs failed (4 iterations x 2 attempts), and smoke_test and cardinality_test were SKIPPED.

Iteration error (read for 5 of the 8 iteration runs; the other 3 were seen as FAILED but their error text was not read):

`INVALID_PARAMETER_VALUE: Run <id> is an iteration of a For each run; setting task values is not supported for iterations.`

Root cause: `notebooks/register_single_mv` (last cell) calls `dbutils.jobs.taskValues.set(key="summary", ...)`. Jobs does not allow this inside a for_each iteration. The `CREATE OR REPLACE VIEW` cell runs before it, so the views were created, but each iteration then failed. This is a defect in the bundle template notebook, not in the metric view YAML.

Resolved by the owner in commit ebb59e0, which fixed `register_single_mv`. Run 598581036511952 above is the result on the fixed code.
