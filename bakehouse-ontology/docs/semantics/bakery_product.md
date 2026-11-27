---
term: Bakery Product
synonyms: [product, baked good, item, SKU]
domain: bakehouse_sales
subdomain: sales
related_terms: [Units Sold, Average Selling Price, Revenue]
sources:
  - https://en.wikipedia.org/wiki/Bakery
---

## Definition
A flour-based baked good made in an oven and sold by a bakery, such as bread, cookies, cakes, doughnuts, bagels, pastries or pies ([Bakery — Wikipedia](https://en.wikipedia.org/wiki/Bakery)).

## Business context
The unit of the menu. Product mix (share of units or revenue per product) drives production planning and promotions at each franchise.

## Data usage
Dimension `product` (string) in `samples.bakehouse.sales_transactions`. There is no separate product table, so product attributes (category, cost) are not available.
