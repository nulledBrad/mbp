# Session 06 (wave 3): Metric View Registration & Smoke Tests

Run: 892325824190755. Status: **blocked**.

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
* Owner: remove (or guard) `dbutils.jobs.taskValues.set` in `ontology-metric-views/notebooks/register_single_mv.ipynb` (also in the SDK template), keep `dbutils.notebook.exit(json.dumps(summary))`. Then re-run wave 3.

## Checks
* bundle validate --strict (dev): pass
* bundle deploy (dev): pass
* registration_job (run SUCCESS after build start): fail
* mv_smoke (all registered + smoke/cardinality SUCCESS): fail (views present; tests skipped)

## Not done
* `ontology-genie-agents` bundle validate was not run in this session; the gate checks it.
