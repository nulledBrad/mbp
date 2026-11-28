# UC Pages Dry Run — bakehouse_sales

Build run `892325824190755`, session 07 (wave 4), 2026-11-28.

## Result: dry run could not run

`upsert_pages(terms, RestPagesClient(ws), dry_run=True, domain_id=<General>)` failed on its first call, `list_pages` (`GET /api/2.0/knowledge/pages`):

`PermissionDenied (403): Knowledge pages are not enabled for this account.`

The same 403 comes back from a plain `GET /api/2.0/knowledge/pages?page_size=5` with no domain filter, so the problem is the account feature, not the domain filter or permissions on a domain. `GET /api/2.0/domains` works and returns only the Databricks-managed `General` domain (`domains/system-general-domain-383a3d5c-5308-4214-8369-bb2f7b730d29`).

Because existing pages could not be listed, there is no create/update split. If the feature were on and no page had these titles, every term below would be a `create` (as DRAFT).

## Merged terms (33)

Sources: 22 files in `docs/semantics/` (session 02) and 13 in `docs/context/glossary_candidates/` (session 03), parsed with `genie_ontology_modeled_knowledge_sdk.pages.parse_glossary`. Two titles are in both folders, `Revenue` and `Units Sold`: the `docs/semantics/` version is kept and the candidate's synonyms and sources are merged into it (case-insensitive dedup). 22 + 13 - 2 = 33.

The drafted page content is not copied here: `to_page_payload(term, domain_id)` builds it from the source file every time. `description` = first sentence of Definition (max 160 chars), `body` = Definition, Business context, Data usage, Related terms, Source. Below are the source file, subdomain from front matter, the synonyms that would be sent, and the body length in characters.

| Term | Source file | Subdomain | Synonyms | Body chars |
|---|---|---|---|---|
| Active Customers | `docs/context/glossary_candidates/active_customers.md` | unassigned | buying customers, purchasing customers, customers who bought | 538 |
| Approved Supplier | `docs/context/glossary_candidates/approved_supplier.md` | unassigned | approved vendor | 535 |
| Average Order Value | `docs/context/glossary_candidates/average_order_value.md` | unassigned | AOV, average transaction value, average basket value | 510 |
| Average Selling Price | `docs/semantics/average_selling_price.md` | sales | ASP, average unit price, average price per unit | 856 |
| Average Transaction Value | `docs/semantics/average_transaction_value.md` | sales | ATV, average order value, AOV, average ticket, average basket value | 859 |
| Average Unit Price | `docs/context/glossary_candidates/average_unit_price.md` | unassigned | average price, weighted average price, price per unit | 456 |
| Bakery Product | `docs/semantics/bakery_product.md` | sales | product, baked good, item, SKU | 663 |
| Customer | `docs/semantics/customer.md` | sales | buyer, shopper, guest, consumer | 1041 |
| Customer Lifetime Value | `docs/semantics/customer_lifetime_value.md` | sales | CLV, LTV, lifetime customer value, LCV | 839 |
| Customer Retention Rate | `docs/semantics/customer_retention_rate.md` | sales | retention rate, customer retention | 962 |
| Customer Review | `docs/semantics/customer_review.md` | customer_feedback | review, customer feedback, media review | 858 |
| Franchise | `docs/semantics/franchise.md` | franchise_network | franchise location, outlet, store, franchise unit, location | 1144 |
| Franchise Fee | `docs/semantics/franchise_fee.md` | franchise_network | initial franchise fee, royalty, franchise royalty | 896 |
| Franchise Location | `docs/context/glossary_candidates/franchise_location.md` | unassigned | franchise country, franchise city, store location, sales location | 618 |
| Franchise Size | `docs/context/glossary_candidates/franchise_size.md` | unassigned | store size, location size | 519 |
| Franchisee | `docs/semantics/franchisee.md` | franchise_network | franchise owner, franchise operator, licensee | 730 |
| Franchisor | `docs/semantics/franchisor.md` | franchise_network | brand owner, franchise company, licensor | 830 |
| Net Promoter Score | `docs/semantics/net_promoter_score.md` | customer_feedback | NPS | 739 |
| Payment Method | `docs/semantics/payment_method.md` | sales | payment type, tender type, payment channel | 817 |
| Personal Data | `docs/semantics/personal_data.md` | sales | PII, personally identifiable information, personal information | 1008 |
| Product Revenue Share | `docs/context/glossary_candidates/product_revenue_share.md` | unassigned | product mix, share of revenue, revenue share | 556 |
| Repeat Customer Rate | `docs/semantics/repeat_customer_rate.md` | sales | repeat purchase rate, returning customer rate | 765 |
| Revenue | `docs/semantics/revenue.md` | sales | sales, gross sales, turnover, total sales, total revenue, sales amount | 1056 |
| Revenue per Franchise | `docs/context/glossary_candidates/revenue_per_franchise.md` | unassigned | average revenue per franchise, revenue per location, revenue per store | 576 |
| Review Count | `docs/context/glossary_candidates/review_count.md` | unassigned | number of reviews, customer reviews, review volume | 554 |
| Review Sentiment | `docs/semantics/review_sentiment.md` | customer_feedback | sentiment, sentiment score, positive review share, opinion | 874 |
| RFM Segmentation | `docs/semantics/rfm_segmentation.md` | sales | RFM, recency frequency monetary, RFM analysis | 758 |
| Same-Store Sales | `docs/semantics/same_store_sales.md` | sales | like-for-like sales, comparable store sales, comp sales, SSS | 883 |
| Supplier | `docs/semantics/supplier.md` | franchise_network | ingredient supplier, vendor, approved supplier | 869 |
| Transaction | `docs/semantics/transaction.md` | sales | sale, POS transaction, order, ticket, purchase | 895 |
| Transactions | `docs/context/glossary_candidates/transactions.md` | unassigned | orders, transaction count, number of sales | 502 |
| Units Sold | `docs/semantics/units_sold.md` | sales | quantity sold, unit sales, items sold, volume, units | 795 |
| Unknown Supplier | `docs/context/glossary_candidates/unknown_supplier.md` | unassigned | missing supplier, unmatched supplier | 615 |

## Overlaps to review before the live run

The merge matches exact titles only, so near-duplicates from the two folders become separate pages. A synonym of one page is the title of another in these cases:

* `Average Transaction Value` (synonym "average order value") and `Average Order Value` (synonym "average transaction value").
* `Average Selling Price` (synonym "average unit price") and `Average Unit Price`.
* `Franchise` (synonym "franchise location") and `Franchise Location`.
* `Supplier` (synonym "approved supplier") and `Approved Supplier`.
* `Transaction` and `Transactions` (entity vs count).

This session did not change the source files (they belong to sessions 02 and 03). The owner should decide whether to fold the candidate terms into the semantics terms before pages are created.
