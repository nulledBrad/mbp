# 05 Domain / Subdomain Proposal — bakehouse_sales

## Proposed domain
* Name: bakehouse_sales
* Description: Sales, franchise network, supplier and customer-review knowledge for a franchised bakery chain.

## Subdomains
| subdomain | description | source tables | candidate metric views | glossary terms |
|---|---|---|---|---|
| sales | POS transactions and the customers who make them: revenue, transactions, units, average transaction value, product and payment mix, customer behavior | samples.bakehouse.sales_transactions, samples.bakehouse.sales_customers | mv_bakehouse_sales_transactions, mv_bakehouse_sales_customers | revenue, transaction, average_transaction_value, units_sold, average_selling_price, bakery_product, payment_method, customer, repeat_customer_rate, customer_retention_rate, customer_lifetime_value, rfm_segmentation, same_store_sales, personal_data |
| franchise_network | Franchise outlets and their ingredient suppliers: outlet footprint, revenue per franchise, supplier approval | samples.bakehouse.sales_franchises, samples.bakehouse.sales_suppliers | mv_bakehouse_franchise_network | franchise, franchisee, franchisor, franchise_fee, supplier |
| customer_feedback | Customer reviews of franchise outlets: review volume and sentiment | samples.bakehouse.media_customer_reviews | mv_bakehouse_customer_reviews | customer_review, review_sentiment, net_promoter_score |

## Rationale
* The three subdomains follow the stakeholders and entities in [01_domain_overview.md](01_domain_overview.md): customers buying at the POS, the franchisor-franchisee-supplier network, and the customer voice ([Franchising — Wikipedia](https://en.wikipedia.org/wiki/Franchising), [Point of sale — Wikipedia](https://en.wikipedia.org/wiki/Point_of_sale), [Customer satisfaction — Wikipedia](https://en.wikipedia.org/wiki/Customer_satisfaction)).
* The KPI groups in [02_kpi_definitions.md](02_kpi_definitions.md) line up with these subdomains. Sales KPIs read `sales_transactions` (customer KPIs join `sales_customers`). Network KPIs roll sales up by `sales_franchises` attributes and join `sales_suppliers`. Review KPIs read `media_customer_reviews` only.
* `sales_customers` sits under `sales` because every customer KPI is computed from transactions. It carries PII that must stay out of metric views ([03_regulatory_context.md](03_regulatory_context.md)).
* The estimate is 3-5 metric views in total, far below `max_metric_views_per_agent` (30). Per project decision 1, a single Genie Agent covering all subdomains is expected. No subdomain is at risk of exceeding the limit.
* Cross-subdomain joins: `sales_transactions.franchiseID` -> `sales_franchises`, `media_customer_reviews.franchiseID` -> `sales_franchises`, `sales_franchises.supplierID` -> `sales_suppliers`. Session 04 verifies the cardinalities.

## Alternatives considered
* Two subdomains (`sales` and `operations`, with reviews folded into operations): rejected because review text needs its own treatment (sentiment derivation, possible personal details) and has no rollup path through transactions.
* A separate `customers` subdomain: rejected because customer KPIs are all computed from transactions, and a standalone customer view would mostly expose PII columns.

## Owner decisions needed
* [ ] Create the domain and subdomains in UC (opt-in)
* [ ] Confirm the exclusion of `cardNumber` and customer contact columns from all metric views
* [ ] Approve or reject an AI-derived sentiment label for `customer_feedback`

## Sources
1. [Franchising — Wikipedia](https://en.wikipedia.org/wiki/Franchising) — Wikipedia
2. [Point of sale — Wikipedia](https://en.wikipedia.org/wiki/Point_of_sale) — Wikipedia
3. [Customer satisfaction — Wikipedia](https://en.wikipedia.org/wiki/Customer_satisfaction) — Wikipedia
