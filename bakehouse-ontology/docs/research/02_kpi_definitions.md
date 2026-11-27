# 02 KPI Definitions — bakehouse_sales

## Summary
The KPIs for a franchised bakery fall into three groups: sales (revenue, transactions, average transaction value, units, average selling price), network and outlet performance (revenue per franchise, same-store sales), and customer and voice-of-customer (repeat purchase, retention, lifetime value, review volume and sentiment, NPS). Formulas follow the cited definitions. Where a source gives only a definition, the column-level formula is our mapping and is marked *mapping*. NPS and the franchise fee cannot be computed from the source tables.

## Findings

### KPI table
| kpi | formula | numerator | denominator | grain | time_period | source | candidate_tables |
|---|---|---|---|---|---|---|---|
| Revenue (gross sales) | `SUM(totalPrice)` | sales amount | — | transaction row, rolled up by franchise / product / day | any period | [Revenue — Wikipedia](https://en.wikipedia.org/wiki/Revenue) | `sales_transactions` |
| Transaction count | `COUNT(DISTINCT transactionID)` (*mapping*) | completed POS transactions | — | transaction | any period | [Point of sale — Wikipedia](https://en.wikipedia.org/wiki/Point_of_sale) | `sales_transactions` |
| Average transaction value (average order value) | revenue / number of transactions | `SUM(totalPrice)` | `COUNT(DISTINCT transactionID)` | franchise, day, payment method | same period for both | [Shopify — Average Order Value](https://www.shopify.com/blog/average-order-value) | `sales_transactions` |
| Units sold | `SUM(quantity)` (*mapping*) | units | — | transaction row | any period | [Unit price — Wikipedia](https://en.wikipedia.org/wiki/Unit_price) | `sales_transactions` |
| Average selling price | total sales revenue / total units sold | `SUM(totalPrice)` | `SUM(quantity)` | product | any period | [Average selling price — Wikipedia](https://en.wikipedia.org/wiki/Average_selling_price), [Unit price — Wikipedia](https://en.wikipedia.org/wiki/Unit_price) | `sales_transactions` |
| Revenue per franchise | revenue / number of franchises with sales (*mapping*) | `SUM(totalPrice)` | `COUNT(DISTINCT franchiseID)` | network, country, size | same period | [Revenue — Wikipedia](https://en.wikipedia.org/wiki/Revenue), [Franchising — Wikipedia](https://en.wikipedia.org/wiki/Franchising) | `sales_transactions`, `sales_franchises` |
| Same-store sales growth | (revenue of existing outlets this period − same outlets' revenue in the identical prior period) / prior-period revenue; new and closed outlets excluded | current-period revenue minus prior | prior-period revenue | franchise -> network | usually year over year | [Same-store sales — Wikipedia](https://en.wikipedia.org/wiki/Same-store_sales) | `sales_transactions`, `sales_franchises` |
| Revenue share by payment method | revenue for method / total revenue (*mapping*) | `SUM(totalPrice)` for `paymentMethod` | `SUM(totalPrice)` | payment method | any period | [Point of sale — Wikipedia](https://en.wikipedia.org/wiki/Point_of_sale) | `sales_transactions` |
| Product mix | units (or revenue) of a product / total units (or revenue) | `SUM(quantity)` for product | `SUM(quantity)` | product | any period | [Unit price — Wikipedia](https://en.wikipedia.org/wiki/Unit_price) (mix weighting) | `sales_transactions` |
| Repeat customer rate | customers with more than one transaction / customers with at least one (*mapping*) | `COUNT(DISTINCT customerID)` with >1 transaction | `COUNT(DISTINCT customerID)` | network, franchise | fixed window | [Shopify — Repeat customers](https://www.shopify.com/blog/repeat-customers) (definition only) | `sales_transactions`, `sales_customers` |
| Customer retention rate | share of customers retained over a period | retained customers | customers at risk at start | network | period over period | [Customer retention — Wikipedia](https://en.wikipedia.org/wiki/Customer_retention) | `sales_transactions` |
| Customer lifetime value | estimated net profit a customer contributes over the whole future relationship; a historical proxy is revenue per customer (*mapping*) | `SUM(totalPrice)` | `COUNT(DISTINCT customerID)` | customer | full history | [Customer lifetime value — Wikipedia](https://en.wikipedia.org/wiki/Customer_lifetime_value) | `sales_transactions`, `sales_customers` |
| RFM score | recency, frequency and monetary value per customer, for segmentation | — | — | customer | as-of date | [RFM — Wikipedia](https://en.wikipedia.org/wiki/RFM_(market_research)) | `sales_transactions` |
| Review count | `COUNT(*)` reviews (*mapping*) | reviews | — | franchise, day | any period | [Customer satisfaction — Wikipedia](https://en.wikipedia.org/wiki/Customer_satisfaction) | `media_customer_reviews` |
| Review sentiment share | positive (negative) reviews / all reviews, where sentiment is derived by NLP from `review` | classified reviews | all reviews | franchise | any period | [Sentiment analysis — Wikipedia](https://en.wikipedia.org/wiki/Sentiment_analysis) | `media_customer_reviews` |
| Net promoter score | % promoters (rating 9-10) − % detractors (rating 6 or lower) | — | — | network | survey wave | [Net promoter score — Wikipedia](https://en.wikipedia.org/wiki/Net_promoter_score) | not yet mapped (no rating column) |
| Approved supplier share | franchises supplied by an approved supplier / all franchises (*mapping*; depends on `approved` values) | franchises with `approved` supplier | `COUNT(DISTINCT franchiseID)` | network | snapshot | [Supply chain management — Wikipedia](https://en.wikipedia.org/wiki/Supply_chain_management) | `sales_franchises`, `sales_suppliers` |

### Notes on formulas
* Average order value is "revenue divided by the number of orders" for a specific period. The reporting period and what counts as revenue should be agreed before comparing ([Shopify — Average Order Value](https://www.shopify.com/blog/average-order-value)).
* A product's average price is total sales revenue divided by total units sold, and can be weighted by unit-sales mix across variants ([Unit price — Wikipedia](https://en.wikipedia.org/wiki/Unit_price)).
* Same-store sales compares existing outlets with an identical past period and excludes new and closed outlets, so the comparison is like for like ([Same-store sales — Wikipedia](https://en.wikipedia.org/wiki/Same-store_sales)).
* NPS sorts respondents into promoters (9-10), passives (7-8) and detractors (6 or lower) and subtracts the detractor share from the promoter share ([Net promoter score — Wikipedia](https://en.wikipedia.org/wiki/Net_promoter_score)).

## Relevance to the source tables
| Finding | Table / column | How it is used (measure, dimension, filter, page) |
|---|---|---|
| Revenue, ATV, ASP, units | `sales_transactions.totalPrice`, `quantity`, `transactionID` | measures |
| Time grain | `sales_transactions.dateTime`, `media_customer_reviews.review_date` | time dimension |
| Franchise rollups | `sales_franchises.country`, `city`, `district`, `size` | dimensions |
| Customer KPIs | `sales_transactions.customerID` -> `sales_customers.customerID` | join; distinct-count measures |
| Sentiment | `media_customer_reviews.review` | text input; needs an AI-derived label before it can become a measure |
| Supplier KPIs | `sales_franchises.supplierID`, `sales_suppliers.approved`, `ingredient` | dimension; filter |

## Open questions
* Same-store sales and retention need a prior period. Table statistics show transactions only from 2024-05-01 to 2024-05-17 and reviews from 2024-05-20 to 2024-05-31, so year-over-year comparisons cannot be computed. Week over week is possible but short.
* Is `totalPrice` always equal to `quantity * unitPrice` (no discounts or tax)? Session 04 should check this.
* There is no cost column, so margin KPIs (gross margin, food cost %) are out of scope.
* Sentiment needs a derivation step (for example an AI classification) that the owner must approve; reviews have no numeric rating.

## Sources
1. [Revenue — Wikipedia](https://en.wikipedia.org/wiki/Revenue) — Wikipedia
2. [Point of sale — Wikipedia](https://en.wikipedia.org/wiki/Point_of_sale) — Wikipedia
3. [Average Order Value (AOV): Formula & Tips](https://www.shopify.com/blog/average-order-value) — Shopify, 2026-11-08
4. [Unit price — Wikipedia](https://en.wikipedia.org/wiki/Unit_price) — Wikipedia
5. [Average selling price — Wikipedia](https://en.wikipedia.org/wiki/Average_selling_price) — Wikipedia
6. [Same-store sales — Wikipedia](https://en.wikipedia.org/wiki/Same-store_sales) — Wikipedia
7. [What are Repeat Customers and How to Increase Them](https://www.shopify.com/blog/repeat-customers) — Shopify
8. [Customer retention — Wikipedia](https://en.wikipedia.org/wiki/Customer_retention) — Wikipedia
9. [Customer lifetime value — Wikipedia](https://en.wikipedia.org/wiki/Customer_lifetime_value) — Wikipedia
10. [RFM (market research) — Wikipedia](https://en.wikipedia.org/wiki/RFM_(market_research)) — Wikipedia
11. [Customer satisfaction — Wikipedia](https://en.wikipedia.org/wiki/Customer_satisfaction) — Wikipedia
12. [Sentiment analysis — Wikipedia](https://en.wikipedia.org/wiki/Sentiment_analysis) — Wikipedia
13. [Net promoter score — Wikipedia](https://en.wikipedia.org/wiki/Net_promoter_score) — Wikipedia
14. [Franchising — Wikipedia](https://en.wikipedia.org/wiki/Franchising) — Wikipedia
15. [Supply chain management — Wikipedia](https://en.wikipedia.org/wiki/Supply_chain_management) — Wikipedia
