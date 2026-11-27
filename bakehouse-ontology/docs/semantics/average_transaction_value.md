---
term: Average Transaction Value
synonyms: [ATV, average order value, AOV, average ticket, average basket value]
domain: bakehouse_sales
subdomain: sales
related_terms: [Revenue, Transaction]
sources:
  - https://www.shopify.com/blog/average-order-value
---

## Definition
The average amount spent per transaction in a period: revenue divided by the number of orders (transactions) in that period ([Shopify — Average Order Value](https://www.shopify.com/blog/average-order-value)).

## Business context
Shows how much each visit is worth. It helps evaluate upselling, pricing and product mix by franchise. Comparisons need the same period and the same definition of revenue ([Shopify — Average Order Value](https://www.shopify.com/blog/average-order-value)).

## Data usage
`SUM(totalPrice) / COUNT(DISTINCT transactionID)` over `samples.bakehouse.sales_transactions`, by franchise, day or payment method. Ratio measure: never sum or average it across groups. Recompute it from the numerator and denominator.
