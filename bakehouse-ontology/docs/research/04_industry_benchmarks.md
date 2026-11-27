# 04 Industry Benchmarks — bakehouse_sales

## Summary
We found few published, verifiable benchmarks for franchised bakery sales KPIs. Government industry statistics pages (US Census NAICS, BLS) blocked automated access, and paywalled business references could not be read. The one numeric benchmark found is an ecommerce average order value for food and beverage. It is not directly comparable to in-store bakery transactions. Every other row is `not found`. The KPI scales come from cited definitions (for example, NPS bands). Because the dataset is synthetic, benchmarks are for orientation only, not for gating.

## Findings
* Dynamic Yield ecommerce benchmarks, as reported by Shopify for August 2026, give a food and beverage average order value of $81 (luxury and jewelry $569, beauty and personal care $71) ([Shopify — Average Order Value](https://www.shopify.com/blog/average-order-value)). Shopify warns that comparisons need the same currency, reporting period and treatment of discounts, taxes, shipping and returns ([Shopify — Average Order Value](https://www.shopify.com/blog/average-order-value)). This is online orders, not bakery POS tickets.
* NPS bands: promoters rate 9-10, passives 7-8, detractors 6 or lower ([Net promoter score — Wikipedia](https://en.wikipedia.org/wiki/Net_promoter_score)). No bakery-specific NPS benchmark was found.
* Same-store sales are reported as a like-for-like comparison with an identical past period, usually the previous year ([Same-store sales — Wikipedia](https://en.wikipedia.org/wiki/Same-store_sales)). No bakery franchise benchmark value was found.
* Franchise royalty and fees: payments typically include a trademark royalty and a percentage of unit sales ([Franchising — Wikipedia](https://en.wikipedia.org/wiki/Franchising)). No verified typical royalty rate for bakery franchises was found.

| kpi | value_or_range | tier | as_of | source |
|---|---|---|---|---|
| Average order value (food and beverage, ecommerce) | $81 | industry average (Dynamic Yield dataset) | 2026-08 | [Shopify — Average Order Value](https://www.shopify.com/blog/average-order-value) |
| Average transaction value (in-store bakery) | not found | — | — | — |
| Revenue per franchise outlet (bakery) | not found | — | — | — |
| Same-store sales growth (bakery franchise) | not found | — | — | [Same-store sales — Wikipedia](https://en.wikipedia.org/wiki/Same-store_sales) (definition only) |
| Repeat customer rate (bakery) | not found | — | — | — |
| Customer retention rate (bakery) | not found | — | — | — |
| Net promoter score (bakery) | not found (scale: promoters 9-10, detractors <=6) | — | — | [Net promoter score — Wikipedia](https://en.wikipedia.org/wiki/Net_promoter_score) |
| Positive review sentiment share | not found | — | — | — |
| Franchise royalty rate (bakery) | not found | — | — | [Franchising — Wikipedia](https://en.wikipedia.org/wiki/Franchising) (definition only) |

## Relevance to the source tables
| Finding | Table / column | How it is used (measure, dimension, filter, page) |
|---|---|---|
| AOV benchmark (orientation only) | `sales_transactions.totalPrice`, `transactionID` | page context for the average transaction value term; not a threshold |
| NPS scale | none | page context; NPS is not mapped |
| Same-store sales definition | `sales_transactions.dateTime`, `franchiseID` | measure needs a prior period that the data lacks (see 02) |

## Open questions
* Owner: supply internal targets (average ticket, revenue per outlet, sentiment share) if benchmarks are wanted on UC Pages.
* `unitPrice` / `totalPrice` are integers with no stated currency, so a dollar benchmark may not be comparable at all.

## Sources
1. [Average Order Value (AOV): Formula & Tips](https://www.shopify.com/blog/average-order-value) — Shopify, 2026-11-08 (cites Dynamic Yield, August 2026)
2. [Net promoter score — Wikipedia](https://en.wikipedia.org/wiki/Net_promoter_score) — Wikipedia
3. [Same-store sales — Wikipedia](https://en.wikipedia.org/wiki/Same-store_sales) — Wikipedia
4. [Franchising — Wikipedia](https://en.wikipedia.org/wiki/Franchising) — Wikipedia
