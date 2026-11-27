---
term: Repeat Customer Rate
synonyms: [repeat purchase rate, returning customer rate]
domain: bakehouse_sales
subdomain: sales
related_terms: [Customer, Customer Retention Rate, Customer Lifetime Value]
sources:
  - https://www.shopify.com/blog/repeat-customers
---

## Definition
The share of customers who buy again. Repeat customers are consumers who use a business's services again and again ([Shopify — Repeat customers](https://www.shopify.com/blog/repeat-customers)).

## Business context
A loyalty indicator for franchises: repeat buyers cost less to serve than new ones and stabilize outlet sales.

## Data usage
Mapping (not from the source): customers with more than one distinct `transactionID` in the window / customers with at least one, over `samples.bakehouse.sales_transactions` by `customerID`. The data window is short (about 17 days), so state the window explicitly.
