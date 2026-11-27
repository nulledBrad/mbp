---
term: Active Customers
synonyms: [buying customers, purchasing customers, customers who bought]
domain: bakehouse_sales
subdomain: unassigned
related_terms: [Transactions, Revenue]
sources: [/Volumes/genie_sdk_bakehouse/inputs/requirements/bakehouse_business_requirements.md]
---
## Definition
Active customers is the number of distinct customers with at least one transaction in the period.

## Business context
Measures the size of the buying customer base. Customers are always reported in aggregate; individual customers and their personal details are never listed.

## Data usage
COUNT(DISTINCT sales_transactions.customerID) over transactions in the filtered period.
