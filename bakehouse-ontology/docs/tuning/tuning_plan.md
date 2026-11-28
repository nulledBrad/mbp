# Tuning Plan — bakehouse_sales

Rule: metric views first, agent instructions last. Max 2 fix iterations, run only while an agent is below the gate.
Gate: accuracy >= 70% per agent (definition: see benchmark_results.md).

Build run `892325824190755` (started 2026-11-27T16:23:24Z), session 10, wave 6. Questions: `docs/tuning/benchmark_questions.yml` (19 questions, 18 with an expected answer, tagged `agent:bakehouse_sales_agent`). Expected answers were computed with read-only queries on the registered metric views and cross-checked against the `samples.bakehouse` source tables; they match the owner's requirements Q1-Q10 where they overlap.

## Agents

| Agent | Space id source | Metric views | Baseline accuracy | Final accuracy | Gate |
|---|---|---|---|---|---|
| bakehouse_sales_agent | docs/deployment/deployment_results.md | 4 | 94.4% (17/18) | 94.4% (17/18) | pass |

The baseline already passes the gate, so no fix iteration was run (plan 10 task 3 applies only while an agent is below 70%). The final run is the baseline run (2026-11-28T16:19Z, after the build run started). No metric view or agent file was changed, so nothing was re-registered or redeployed.

## Triage of failures

| Question id | Symptom | Category | Fix (first column that applies) |
|---|---|---|---|
| q06 "revenue from franchises located in the US" | Agent filtered `Franchise Country ILIKE '%US%'`, which also matches `Australia` (20,736 + 8,211 = 28,947 instead of 20,736) | Synonym gap / filter (value matching) | Metric view: list the exact `Franchise Country` values in its comment and say to filter with `=` (not yet applied, see Remaining items) |

q19 (average star rating) has no expectation by design. The agent correctly answered in text that ratings are not available.

## Phase 1 — Metric view actions

None applied (gate passed at baseline). Proposed:

| # | Question id(s) | Category | Metric view | Change | Iteration |
|---|---|---|---|---|---|
| 1 | q06 | Synonym gap (value matching) | mv_bakehouse_sales_transactions, mv_bakehouse_franchise_network, mv_bakehouse_customer_reviews | `Franchise Country` comment: list the values (Australia, Canada, France, Germany, Italy, Japan, Netherlands, Sweden, US) and say to match them exactly; add synonyms `United States`, `USA` to steer to 'US' | proposed, not run |

## Phase 2 — Agent actions (only when Phase 1 cannot fix it)

None.

| # | Question id(s) | Category | Agent | Change | Why a metric view change was not enough |
|---|---|---|---|---|---|

## Iteration log

| Iteration | Agent | Failures before | Fixes applied | Accuracy before -> after |
|---|---|---|---|---|
| baseline | bakehouse_sales_agent | 1 (q06) | — | — -> 94.4% |

## Remaining items

| Question id | Category | Recommended next fix | Owner |
|---|---|---|---|
| q06 | Synonym gap (value matching) | Phase 1 #1 above, then re-register and re-run the benchmark | next tuning pass (session 10 owner) |
| — | Overfitting check | The agent's example SQL "Which 5 franchises had the highest revenue?" is a single-view, single-measure question; the tuning skill advises against such example SQL. Consider removing it in the next agent pass | next tuning pass |
