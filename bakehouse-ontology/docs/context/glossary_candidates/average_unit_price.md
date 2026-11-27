---
term: Average Unit Price
synonyms: [average price, weighted average price, price per unit]
domain: bakehouse_sales
subdomain: unassigned
related_terms: [Revenue, Units Sold]
sources: [/Volumes/genie_sdk_bakehouse/inputs/requirements/bakehouse_business_requirements.md]
---
## Definition
Average unit price is revenue divided by units sold, a quantity-weighted price.

## Business context
Used to compare realised prices across products and franchises.

## Data usage
SUM(sales_transactions.totalPrice) / SUM(sales_transactions.quantity). Do not use AVG(unitPrice), which is unweighted.
