---
term: Average Selling Price
synonyms: [ASP, average unit price, average price per unit]
domain: bakehouse_sales
subdomain: sales
related_terms: [Units Sold, Revenue, Bakery Product]
sources:
  - https://en.wikipedia.org/wiki/Average_selling_price
  - https://en.wikipedia.org/wiki/Unit_price
---

## Definition
The average price at which a product is sold across channels or markets ([Average selling price — Wikipedia](https://en.wikipedia.org/wiki/Average_selling_price)). It is calculated as total sales revenue divided by total units sold ([Unit price — Wikipedia](https://en.wikipedia.org/wiki/Unit_price)).

## Business context
Tracks realized pricing per product and per franchise. Differences from list price (`unitPrice`) can point to discounting or data issues.

## Data usage
`SUM(totalPrice) / SUM(quantity)` over `samples.bakehouse.sales_transactions`, usually by `product`. Ratio measure: recompute it per group and never average it. `unitPrice` is the per-row price.
