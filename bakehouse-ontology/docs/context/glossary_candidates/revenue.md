---
term: Revenue
synonyms: [sales, total sales, total revenue, sales amount]
domain: bakehouse_sales
subdomain: unassigned
related_terms: [Units Sold, Transactions, Average Order Value, Average Unit Price, Revenue per Franchise, Product Revenue Share]
sources: [/Volumes/genie_sdk_bakehouse/inputs/requirements/bakehouse_business_requirements.md]
---
## Definition
Revenue is the sum of the total price of all sales transactions in the selected period and filters.

## Business context
Revenue is the headline sales KPI for franchise operations managers and regional leads, used for daily and weekly sales by franchise, product mix and country comparisons.

## Data usage
SUM(sales_transactions.totalPrice). totalPrice already equals quantity x unitPrice, so never recompute it from those columns.
