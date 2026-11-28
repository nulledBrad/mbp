# Genie Agent Deployment Results (bakehouse_sales)

Build run `892325824190755` (started 2026-11-27T16:23:24Z), session 09, bundle `ontology-genie-agents`, target `dev`, branch `bw-bakehouse-test` @ `1958e47`.

## Deploy

* Render: `python -m genie_ontology_modeled_knowledge_sdk_vendored.geniespace render agents .rendered --params <dev overrides>` (vendored SDK on `PYTHONPATH`): rendered `bakehouse_sales_agent.geniespace.json`, no unresolved `${` tokens.
* `bundle validate --strict --target dev`: Validation OK.
* `bundle deploy --target dev`: `Created genie_spaces.bakehouse_sales_agent`, `Created genie_spaces.bakehouse_sales_agent.permissions` (2 created, 0 changed, 0 deleted).
* Space ids read from `bundle summary --target dev --output json`.

## Agents

| Resource key | Space id | Title | Deploy time (space create_time, UTC) | Probe question | Attachment type | Query state | Error |
|---|---|---|---|---|---|---|---|
| `bakehouse_sales_agent` | `01f1eb45bc1b10308fbcfa6b39833e18` | [dev bradley_williamson] Bakehouse Sales Agent | 2026-11-28T16:06:56.635Z | Which franchises have the most reviews? | query (+ text) | SUCCEEDED | none |

Every agent was deployed after the build run started (2026-11-28T16:06:56Z > 2026-11-27T16:23:24Z).

## Probe detail

* Probe: `w.genie.start_conversation_and_wait(space_id, "Which franchises have the most reviews?")` (first sample question in the space file's sorted order). Message status `COMPLETED`, no error.
* Attachments: one query attachment, one text attachment, one attachment with neither (suggested-questions style).
* Query: `MEASURE(Reviews)` from `mv_bakehouse_customer_reviews`, grouped by franchise, `RANK()` and filter to rank 1. `get_message_attachment_query_result`: `SUCCEEDED`, 20 rows (each franchise 6 reviews).
* Text answer: reports a 20-way tie at 6 reviews.
* Independent cross-check (direct SQL on the metric view): review counts per franchise are 6 for 20 franchises and 3 for 28 franchises, so the 20-way tie at 6 is correct.

## agent_probe

pass: 1 of 1 agents answered the probe with a `SUCCEEDED` query attachment.

## Notes for session 10

* The probe answer is correct. Not yet checked: the other 8 sample questions and the Q1-Q10 benchmark baseline.
* The tie means "the most reviews" has no single answer in this data. A benchmark question about it should expect the tie.
