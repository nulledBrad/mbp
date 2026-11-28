# Session 08 (wave 4): Genie Agent Curation

Build run `892325824190755`, branch `bw-bakehouse-test`, mode `edit`. Skill: ontology-agent-curation. No deploys, no Genie API writes, no Git commands.

## Inputs used
* `sdk_config.json`: domain `bakehouse_sales`, `max_metric_views_per_agent` = 30.
* `ontology-metric-views/metric_views/manifest.json`: 4 metric views.
* `docs/registration/registration_results.md`: all 4 registered (run 598581036511952 SUCCESS). In this session `DESCRIBE TABLE EXTENDED` also showed all 4 with Type `METRIC_VIEW` in the dev target schema.
* `docs/context/bakehouse_business_requirements_md_context.md`, `docs/research/05_domain_subdomain_proposal.md`, `docs/data_profiles/data_model_summary.md`, the 4 metric view YAML files.

## Agent plan
`geniespace.plan_agents(mvs, max_per_agent=30)` returned one agent, `bakehouse_sales_agent` (4 views, which is not more than 30), with no subdomain and no related agents. I did not change the plan by hand.

## Agent content (`ontology-genie-agents/agents/bakehouse_sales_agent.geniespace.json`)
* Data sources: the 4 metric views, tokenized as `${target_catalog}.${target_schema}.<view>`, each with a one-line description.
* Sample questions (9), taken from the requirements KPIs and expected questions: total revenue; product with highest revenue; revenue by franchise country; transactions by payment method; franchises by size; average order value by week; repeat customer rate; franchises with most reviews; share of franchises with an approved supplier.
* Text instructions (2), each for a rule that no metric view can encode:
  * No period given: use all available data and state the date range. Reason: requirements rule (Business rules, bullet 7). A metric view cannot tell Genie how to phrase its answer.
  * Data not held (costs, margins, staffing, inventory, review ratings or sentiment): say it is not available. Reason: requirements rule (Privacy and exclusions, bullet 2). This is about data that is not in any view.
* Example SQL (2, multi-step only, `MEASURE()` syntax): top 5 franchises by revenue (ranking), and week-over-week revenue change (`LAG`, period comparison).
* Join specs: none. Inference: the verified joins in `data_model_summary.md` are between source tables, and each one is already inside a metric view's YAML. Joining the views across grains (transaction, customer, franchise, review) would risk the fan-out that the EDA flagged. Reviews and sales are also from different periods.

## Other changes
* `ontology-genie-agents/resources/genie_agents.yml`: replaced the template `ontology_agent` entry with `bakehouse_sales_agent` (file_path `../.rendered/bakehouse_sales_agent.geniespace.json`; same warehouse_id, parent_path and permissions).
* Deleted the template placeholder `ontology-genie-agents/agents/ontology_agent.geniespace.json` (it pointed at `mv_placeholder`), as plan 08 task 3 says. Conflict noted: the skill says "no file deletion" but the plan says to remove the placeholder. I removed it because the wave 4 gate needs every referenced view to be registered.
* `docs/domain_manifest.yml`: written by `geniespace.write_domain_manifest`. This is a **proposal** only: one domain and one subdomain (`bakehouse_sales`) holding the 4 views, served by `bakehouse_sales_agent`. Session 02 suggested 3 subdomains (sales, franchise_network, customer_feedback). They are not used because the plan has one agent. Creating UC domains is opt-in for the owner.
* README render step: the render wrote `.rendered/bakehouse_sales_agent.geniespace.json`. That folder is gitignored and not committed. A stale `.rendered/ontology_agent.geniespace.json` from the template is still there; nothing references it, and it is outside my owned paths.

## Checks
| Check | Result |
|---|---|
| `validate_space` on the file as written to disk | pass (0 errors) |
| No literal catalog, schema or warehouse id in the agent file | pass |
| Render with dev overrides leaves no `${` | pass |
| `EXPLAIN` on both rendered example SQLs | pass (both plan without error) |
| Agent references at most 30 registered views | pass (4, all METRIC_VIEW) |
| `bundle validate --strict --target dev` (ontology-genie-agents) | pass (Validation OK) |
| `docs/domain_manifest.yml` exists | pass |

## For later sessions
* Session 09: deploy resource key `bakehouse_sales_agent`. Re-run the render step before deploying. The first sample question in the file's sorted order is "Which franchises have the most reviews?".
* Session 10: the Q1-Q10 expected answers in the requirements context are the benchmark baseline. Fix metric views before adding instructions.
