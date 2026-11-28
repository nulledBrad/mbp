# Session 09 (wave 5): Genie Agent Deployment & Probe

Build run `892325824190755`, branch `bw-bakehouse-test` (Git folder checked read-only: on branch, head `1958e47`), mode `edit` with `allow_execute` and `allow_deploy`. No Git commands. Approval used: `bundle.deploy` on `ontology-genie-agents` (dev target), from the owner authorizations line.

## Work
1. Render: the README command uses `genie_ontology_modeled_knowledge_sdk.geniespace`, which is not installed on serverless. The vendored module `genie_ontology_modeled_knowledge_sdk_vendored.geniespace` (with `ontology-metric-views/src` on `PYTHONPATH`) rendered `.rendered/bakehouse_sales_agent.geniespace.json` with no unresolved tokens. The dev overrides file exists. I read its key names only, not its values.
2. `bundle validate --strict --target dev`: Validation OK.
3. `bundle deploy --target dev`: created `genie_spaces.bakehouse_sales_agent` and its permissions.
4. `bundle summary`: space id `01f1eb45bc1b10308fbcfa6b39833e18`, title `[dev bradley_williamson] Bakehouse Sales Agent`. The space `create_time` is 2026-11-28T16:06:56Z, which is after the build run start (2026-11-27T16:23:24Z).
5. Probe with the first sample question, "Which franchises have the most reviews?". Message `COMPLETED`, with a query attachment over `mv_bakehouse_customer_reviews`. The query result is `SUCCEEDED` with 20 rows, a 20-way tie at 6 reviews. A direct SQL cross-check on the metric view gives the same result (20 franchises have 6 reviews, 28 have 3).
6. Wrote `docs/deployment/deployment_results.md`.

## Checks
| Check | Result |
|---|---|
| Git folder on `bw-bakehouse-test` | pass |
| Render leaves no `${` | pass |
| `bundle validate --strict --target dev` (ontology-genie-agents) | pass |
| `bundle deploy --target dev` | pass (2 created) |
| Deployed after build run start | pass |
| `agent_probe` (SUCCEEDED query attachment) | pass (1/1) |

## Notes
* The `.rendered/` output (gitignored) is outside my owned paths. Plan 09 task 1 needs it as the documented render step. The stale template file `.rendered/ontology_agent.geniespace.json` is still there and nothing references it. I left it alone.
* README vs environment: the README render command names the installed SDK module. In build tasks only the vendored copy is importable. Possible README fix (owner or SDK PR): mention the vendored module.
* Session 10: the space id is above. The probe tie (20 franchises at 6 reviews) is a data property, not an agent defect.

## Files and assets
* Files: `docs/deployment/deployment_results.md`.
* Assets: Genie space `01f1eb45bc1b10308fbcfa6b39833e18` (bundle resource `bakehouse_sales_agent`) and its permissions.
