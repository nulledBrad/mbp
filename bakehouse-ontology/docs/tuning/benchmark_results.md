# Benchmark Results — bakehouse_sales

Build run `892325824190755` (started 2026-11-27T16:23:24Z), session 10, wave 6. Agent `bakehouse_sales_agent`, space id `01f1eb45bc1b10308fbcfa6b39833e18` (from docs/deployment/deployment_results.md). Questions: `docs/tuning/benchmark_questions.yml`. Run with `benchmark.run_benchmark(space_id, questions, ws, timeout_s=180)` (no `warehouse_id`, so scalar `expected_answer` values are used) and scored with `benchmark.score`.

Final run: baseline run, started 2026-11-28T16:19:29Z (after the build run started). No fix iteration was needed.

## Accuracy definition

Quoted from the `genie_ontology_modeled_knowledge_sdk/benchmark.py` docstring:

> Scoring definition
> ------------------
>
> Each question is asked once, in its own Genie conversation
> (``ws.genie.start_conversation_and_wait``). Per result:
>
> * **sql_generated**: the answer has a query attachment with non-empty SQL.
> * **sql_succeeded**: SQL was generated and its query result
>   (``ws.genie.get_message_attachment_query_result``) has statement state
>   ``SUCCEEDED``.
> * **matches_expected**: ``None`` when the question has no expectation, or has
>   only ``expected_sql`` and no ``warehouse_id`` was given (not evaluated).
>   Otherwise ``False`` unless the SQL succeeded and:
>
>   * ``expected_answer`` is a scalar: some cell of the first result row equals
>     it;
>   * ``expected_answer`` is a list: the result rows equal the expected rows as a
>     multiset (row order ignored; a scalar list item is a one-column row);
>   * ``expected_sql`` (evaluated on ``warehouse_id``): the result rows equal the
>     expected query's rows as a multiset.
>
>   When both are given, ``expected_answer`` is used. Values are compared after
>   normalization: numbers with relative tolerance 1e-4 (absolute 1e-6), other
>   values as trimmed, case-insensitive strings, ``NULL`` as ``None``.
>
> Summary counts: ``total``, ``sql_generated``, ``sql_succeeded``,
> ``with_expectations`` (results whose ``matches_expected`` is not ``None``),
> ``matches_expected``, ``errors``.
>
> **accuracy** = ``matches_expected / with_expectations`` when
> ``with_expectations > 0`` (``accuracy_basis = "expected"``), else
> ``sql_succeeded / total`` (``accuracy_basis = "succeeded"``); 0.0 for an empty
> run. The tuning gate is ``accuracy >= 0.70`` (``ACCURACY_GATE``).

## Summary

| Agent | Questions | With expectation | SQL generated | SQL succeeded | Matches expected | Accuracy (baseline) | Accuracy (final) |
|---|---|---|---|---|---|---|---|
| bakehouse_sales_agent | 19 | 18 | 18 | 18 | 17 | 94.4% | 94.4% |

benchmark_results: pass (every agent >= 70%).

## Remaining failures

| Question id | Got | Expected | Category | Proposed fix |
|---|---|---|---|---|
| q06 | 28,947 (`Franchise Country ILIKE '%US%'` also matched Australia) | 20,736 | Synonym gap (value matching) | List exact `Franchise Country` values in the metric view comments and add `United States` / `USA` synonyms (tuning_plan.md Phase 1 #1) |

q19 (no expectation): no SQL; the agent answered in text that review ratings are not available, which is the intended behaviour.

## Results (final run, bakehouse_sales_agent)

Output of `benchmark.write_results_md` (catalog and schema tokenized as `${target_catalog}.${target_schema}`):

| Metric | Value |
|---|---|
| Questions | 19 |
| SQL generated | 18 |
| SQL succeeded | 18 |
| With expectations | 18 |
| Matches expected | 17 |
| Errors | 0 |
| Accuracy | 94.4% (expected) |
| Gate (>= 70%) | pass |

### Questions

| ID | Question | SQL generated | SQL succeeded | Matches expected | Interpretation | Tags | Error |
|---|---|---|---|---|---|---|---|
| q01 | What were total gross sales across all available data? | yes | yes | yes |  | agent:bakehouse_sales_agent, simple, synonym |  |
| q02 | How many orders were placed overall? | yes | yes | yes |  | agent:bakehouse_sales_agent, simple, synonym |  |
| q03 | How many items were sold in total? | yes | yes | yes |  | agent:bakehouse_sales_agent, simple, synonym |  |
| q04 | Which bakery product generated the most sales? | yes | yes | yes |  | agent:bakehouse_sales_agent, breakdown, synonym |  |
| q05 | Which country's franchises brought in the highest revenue? | yes | yes | yes |  | agent:bakehouse_sales_agent, breakdown |  |
| q06 | What was revenue from franchises located in the US? | yes | yes | no |  | agent:bakehouse_sales_agent, filter |  |
| q07 | Which card type was used for the most transactions? | yes | yes | yes |  | agent:bakehouse_sales_agent, breakdown, synonym |  |
| q08 | What is the average ticket size across all transactions? | yes | yes | yes |  | agent:bakehouse_sales_agent, ratio, synonym |  |
| q09 | How many unique customers made at least one purchase? | yes | yes | yes |  | agent:bakehouse_sales_agent, simple |  |
| q10 | Which week had the highest revenue, and how much was it? | yes | yes | yes |  | agent:bakehouse_sales_agent, time |  |
| q11 | How much revenue did we make on our single best sales day? | yes | yes | yes |  | agent:bakehouse_sales_agent, time |  |
| q12 | What fraction of total revenue came from Golden Gate Ginger? | yes | yes | yes |  | agent:bakehouse_sales_agent, ratio, filter |  |
| q13 | What is the average selling price per unit? | yes | yes | yes |  | agent:bakehouse_sales_agent, ratio, synonym |  |
| q14 | How many franchise locations are in Japan? | yes | yes | yes |  | agent:bakehouse_sales_agent, filter |  |
| q15 | How many franchises have no matching supplier record? | yes | yes | yes |  | agent:bakehouse_sales_agent, edge |  |
| q16 | What is the average customer lifetime value? | yes | yes | yes |  | agent:bakehouse_sales_agent, ratio, synonym |  |
| q17 | How many customer reviews do we have in total? | yes | yes | yes |  | agent:bakehouse_sales_agent, simple |  |
| q18 | Which store size class generated the most revenue? | yes | yes | yes |  | agent:bakehouse_sales_agent, breakdown, synonym |  |
| q19 | What is the average star rating of franchise reviews? | no | no | n/a |  | agent:bakehouse_sales_agent, edge, no_expectation |  |

### SQL

All queries returned 1 row. Generated SQL by question (abridged to the view and logic used):

| ID | Metric view | Generated logic |
|---|---|---|
| q01 | mv_bakehouse_sales_transactions | `MEASURE(Revenue)` |
| q02 | mv_bakehouse_sales_transactions | `MEASURE(Transactions)` |
| q03 | mv_bakehouse_sales_transactions | `MEASURE(Units Sold)` |
| q04 | mv_bakehouse_sales_transactions | `Product`, `MEASURE(Revenue)`, `RANK()` = 1 |
| q05 | mv_bakehouse_sales_transactions | `Franchise Country`, `MEASURE(Revenue)`, `RANK()` = 1 |
| q06 | mv_bakehouse_sales_transactions | `MEASURE(Revenue)` WHERE `Franchise Country ILIKE '%US%'` (wrong: also matches Australia) |
| q07 | mv_bakehouse_sales_transactions | `Payment Method`, `MEASURE(Transactions)`, `RANK()` = 1 |
| q08 | mv_bakehouse_sales_transactions | `MEASURE(Average Order Value)` |
| q09 | mv_bakehouse_sales_customers | `MEASURE(Customers)` |
| q10 | mv_bakehouse_sales_transactions | `Transaction Week`, `MEASURE(Revenue)`, `RANK()` = 1 |
| q11 | mv_bakehouse_sales_transactions | `Transaction Date`, `MEASURE(Revenue)` ORDER BY revenue DESC LIMIT 1 |
| q12 | mv_bakehouse_sales_transactions | `MEASURE(Revenue)`, `MEASURE(Revenue All Products)`, `try_divide` WHERE `Product ILIKE '%Golden Gate Ginger%'` |
| q13 | mv_bakehouse_sales_transactions | `MEASURE(Average Unit Price)` |
| q14 | mv_bakehouse_franchise_network | `MEASURE(Franchises)` WHERE `Franchise Country ILIKE '%Japan%'` |
| q15 | mv_bakehouse_franchise_network | `MEASURE(Franchises With Unknown Supplier)` |
| q16 | mv_bakehouse_sales_customers | `MEASURE(Revenue per Customer)` |
| q17 | mv_bakehouse_customer_reviews | `MEASURE(Reviews)` |
| q18 | mv_bakehouse_sales_transactions | `Franchise Size`, `Franchise Size Rank`, `MEASURE(Revenue)`, `RANK()` = 1 |
| q19 | — | _No SQL generated._ Text: review ratings are not available |

q06 full SQL:

```sql
SELECT MEASURE(`Revenue`) AS `Revenue`
FROM ${target_catalog}.${target_schema}.`mv_bakehouse_sales_transactions`
WHERE `Franchise Country` ILIKE '%US%'
GROUP BY ALL
```

q14 also used `ILIKE '%Japan%'`; it is correct here only because no other country contains 'Japan'. The same Phase 1 fix covers it.

## Iteration history

| Run | Date | Agent | Accuracy |
|---|---|---|---|
| baseline (final) | 2026-11-28 | bakehouse_sales_agent | 94.4% |
