---
term: Units Sold
synonyms: [quantity sold, unit sales, items sold, volume]
domain: bakehouse_sales
subdomain: sales
related_terms: [Average Selling Price, Bakery Product, Revenue]
sources:
  - https://en.wikipedia.org/wiki/Unit_price
---

## Definition
The number of product units sold in a period. With revenue, it gives a product's average price: total sales revenue divided by total units sold ([Unit price — Wikipedia](https://en.wikipedia.org/wiki/Unit_price)).

## Business context
Used for product mix, production planning and ingredient demand at each franchise. Unit-sales mix also weights average prices across product variants ([Unit price — Wikipedia](https://en.wikipedia.org/wiki/Unit_price)).

## Data usage
`SUM(quantity)` over `samples.bakehouse.sales_transactions`, by `product`, `franchiseID` or day.
