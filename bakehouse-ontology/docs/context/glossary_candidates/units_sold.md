---
term: Units Sold
synonyms: [quantity sold, items sold, units]
domain: bakehouse_sales
subdomain: unassigned
related_terms: [Revenue, Average Unit Price]
sources: [/Volumes/genie_sdk_bakehouse/inputs/requirements/bakehouse_business_requirements.md]
---
## Definition
Units sold is the total quantity of products sold across sales transactions.

## Business context
Units sold measures sales volume independent of price and is the denominator of average unit price.

## Data usage
SUM(sales_transactions.quantity).
