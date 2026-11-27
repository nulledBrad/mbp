---
term: Customer Lifetime Value
synonyms: [CLV, LTV, lifetime customer value, LCV]
domain: bakehouse_sales
subdomain: sales
related_terms: [Customer, Revenue, Customer Retention Rate, RFM Segmentation]
sources:
  - https://en.wikipedia.org/wiki/Customer_lifetime_value
---

## Definition
An estimation and prediction of the net profit a customer contributes during the entire future relationship with a business. Models range from crude heuristics to complex predictive analytics ([Customer lifetime value — Wikipedia](https://en.wikipedia.org/wiki/Customer_lifetime_value)).

## Business context
Guides how much to spend on acquiring and keeping customers, and identifies high-value customers for loyalty programs.

## Data usage
Not computable as defined: there is no cost or profit column. A historical revenue proxy is `SUM(totalPrice) / COUNT(DISTINCT customerID)` over `samples.bakehouse.sales_transactions`. Label it "revenue per customer", not CLV.
