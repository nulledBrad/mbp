---
term: Revenue per Franchise
synonyms: [average revenue per franchise, revenue per location, revenue per store]
domain: bakehouse_sales
subdomain: unassigned
related_terms: [Revenue, Franchise Location, Franchise Size]
sources: [/Volumes/genie_sdk_bakehouse/inputs/requirements/bakehouse_business_requirements.md]
---
## Definition
Revenue per franchise is revenue divided by the number of distinct franchises with sales in the period.

## Business context
Regional leads use it to compare franchise productivity across countries, cities and franchise sizes.

## Data usage
SUM(sales_transactions.totalPrice) / COUNT(DISTINCT sales_transactions.franchiseID). Only franchises with sales in the period count in the denominator.
