# Session 10 (wave 6): Benchmark & Tuning

Build run `892325824190755`, branch `bw-bakehouse-test` (Git folder checked read-only: on branch, head `6fbfc6f`), mode `edit` with `allow_execute` and `allow_deploy`, continuation 0. No Git commands. Skill: `ontology-tuning`.

## Work
1. Computed expected answers with read-only queries on the 4 registered metric views (`${target_catalog}.${target_schema}`, dev schema) and cross-checked the key values (revenue 66,471, transactions 3,333, units 22,157, customers 300, reviews 204, unknown-supplier franchises 21, Japan franchises 20, Golden Gate Ginger share 0.1744) directly against `samples.bakehouse` source tables. They agree, and match the owner's requirements Q1-Q9 (Q10 is the review tie noted by session 09).
2. Wrote `docs/tuning/benchmark_questions.yml`: 19 questions for `agent:bakehouse_sales_agent` (18 with a scalar `expected_answer` plus tokenized `expected_sql`; q19 is `no_expectation`). Tags cover simple, breakdown, time, filter, ratio, synonym and edge. Wording differs from the agent's sample questions. `benchmark.load_questions` parses it (19 items).
3. Baseline run with `benchmark.run_benchmark` on space `01f1eb45bc1b10308fbcfa6b39833e18` (started 2026-11-28T16:19:29Z, after the build run start). Score: 17/18 matches, accuracy 94.4% (basis `expected`), 0 errors. Gate (70%) passes.
4. One failure, q06: the agent filtered `Franchise Country ILIKE '%US%'`, which also matches Australia (28,947 vs 20,736). Category: synonym gap / value matching. Proposed metric view fix (list exact country values in the `Franchise Country` comments, add `United States` / `USA` synonyms) is recorded but not applied: plan 10 runs fix iterations only while an agent is below 70%.
5. No metric view or agent file changed, so no re-registration and no redeploy (no `bundle deploy` or `jobs.run_now` used).
6. Wrote `docs/tuning/tuning_plan.md` and `docs/tuning/benchmark_results.md`.

## Checks
| Check | Result |
|---|---|
| Git folder on `bw-bakehouse-test` | pass |
| 10-20 questions per agent (19) | pass |
| `load_questions` parses the file | pass |
| benchmark_results: final run after build start, every agent >= 70% (94.4%) | pass |
| `mv_yaml validate ontology-metric-views/metric_views` (vendored) | pass (4 files, 0 errors, exit 0) |
| `bundle validate --strict --target dev` ontology-metric-views | pass |
| `bundle validate --strict --target dev` ontology-genie-agents (existing render from session 09; agent file unchanged) | pass |
| No literal catalog, schema or warehouse id in docs/tuning | pass |
| No instruction restates metric view metadata (agent unchanged) | pass |

## Notes
* `benchmark_results.md`: the summary and per-question table are the `write_results_md` output. Its SQL section is abridged to a per-question table plus the full q06 SQL, with the catalog and schema tokenized.
* The vendored SDK copy under `ontology-metric-views/src` has no `benchmark` module. I imported it from `genie-ontology-modeled-knowledge-sdk/src` in the same Git folder. Possible SDK/run-kit fix: vendor `benchmark.py` too, or document the import path in plan 10.
* Recommendation for a later pass: the agent's example SQL "Which 5 franchises had the highest revenue?" is single-view, single-measure, which the tuning skill calls overfitting. Consider removing it.
* The benchmark matches a scalar answer against any cell of the first row. This makes it robust to extra columns, but a ranked list in the wrong order would fail.

## Files and assets
* Files: `docs/tuning/benchmark_questions.yml`, `docs/tuning/tuning_plan.md`, `docs/tuning/benchmark_results.md`.
* Assets: none changed. 19 Genie conversations were created in space `01f1eb45bc1b10308fbcfa6b39833e18` by the benchmark.
