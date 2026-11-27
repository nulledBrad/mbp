---
term: Average Order Value
synonyms: [AOV, average transaction value, average basket value]
domain: bakehouse_sales
subdomain: unassigned
related_terms: [Revenue, Transactions]
sources: [/Volumes/genie_sdk_bakehouse/inputs/requirements/bakehouse_business_requirements.md]
---
## Definition
Average order value is revenue divided by the number of transactions.

## Business context
AOV shows how much a customer spends per visit and is compared across franchises and periods.

## Data usage
SUM(sales_transactions.totalPrice) / COUNT(DISTINCT sales_transactions.transactionID). Compute as a ratio of totals, not an average of per-row values.
