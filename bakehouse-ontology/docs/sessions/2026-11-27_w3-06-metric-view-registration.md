# Session 06 (wave 3): Metric View Registration & Smoke Tests

Run: 892325824190755. Status: **pass** (after the owner fix ebb59e0; was blocked on the first run).

## Resolution
* The owner fixed `register_single_mv` (commit ebb59e0) and ran `register_metric_views`: run 598581036511952 (2026-11-27 22:26:54 to 22:28:41 UTC) SUCCESS.
* All 4 iterations SUCCESS (status registered). smoke_test: 4/4 passed. cardinality_test: 4 checked, none failed. All 4 views are METRIC_VIEW in UC.
* I re-checked this run read-only (run state, task outputs, information_schema); I did not deploy or run anything again.

## Done
* Branch check: Git folder on `bw-bakehouse-test` (head 2985e608).
* `bundle validate --strict --target dev` (ontology-metric-views): Validation OK.
* `bundle deploy --target dev`: created schema `genie_sdk_bakehouse.dev_bradley_williamson_ontology_bakehouse` (with grants) and job `register_metric_views` (id 967660018526725).
* `bundle run register_metric_views --target dev`: run 830843314683487, FAILED.
* Wrote `docs/registration/registration_results.md`.

## Problem and root cause
* All 8 `register_single_mv` iteration runs failed (4 iterations x 2 attempts). The error, read for 5 of them: `INVALID_PARAMETER_VALUE: ... is an iteration of a For each run; setting task values is not supported for iterations.`
* Cause: the last cell of `ontology-metric-views/notebooks/register_single_mv` calls `dbutils.jobs.taskValues.set`, which Jobs rejects inside for_each iterations. The DDL cell runs first, so all 4 metric views exist in UC as METRIC_VIEW. `smoke_test` and `cardinality_test` were skipped.
* This is a bundle template defect (SDK `templates/` ontology-metric-views), not a YAML defect from session 05. The notebook is outside this session's owned paths, so I did not edit it.

## Requests
* Resolved: the project copy of `register_single_mv` was fixed in ebb59e0.
* Still open (normal): apply the same fix to the SDK `ontology-metric-views` bundle template, so future scaffolds don't hit this. Not checked in this session.

## Checks
* bundle validate --strict (dev): pass
* bundle deploy (dev): pass
* registration_job: pass (run 598581036511952 SUCCESS; first run 830843314683487 failed)
* mv_smoke: pass (4/4 registered, smoke and cardinality SUCCESS)

## Not done
* `ontology-genie-agents` bundle validate was not run in this session; the gate checks it.
