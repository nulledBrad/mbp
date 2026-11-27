---
term: Customer Retention Rate
synonyms: [retention rate, customer retention]
domain: bakehouse_sales
subdomain: sales
related_terms: [Repeat Customer Rate, Customer, Customer Lifetime Value]
sources:
  - https://en.wikipedia.org/wiki/Customer_retention
---

## Definition
The share of customers a business keeps. Wikipedia describes retention rate as the percentage of customers retained relative to the customers who approached the business to cancel ([Customer retention — Wikipedia](https://en.wikipedia.org/wiki/Customer_retention)). A bakery has no subscriptions to cancel, so retention here means customers who keep buying across periods.

## Business context
A loyalty measure franchisors can compare across outlets and over time, alongside the repeat customer rate.

## Data usage
Mapping: customers who bought in period P and again in period P+1 / customers who bought in period P, using `customerID` and `dateTime` in `samples.bakehouse.sales_transactions`. With about 17 days of data, only week-over-week periods are possible. The owner should confirm the period.
