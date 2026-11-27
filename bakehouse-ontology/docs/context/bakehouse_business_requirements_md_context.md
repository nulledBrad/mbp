# Context: bakehouse_business_requirements.md

* Source: /Volumes/genie_sdk_bakehouse/inputs/requirements/bakehouse_business_requirements.md
* Type: md (Version 0.1, 2026-11-27, owner: Bakehouse franchise operations) | Subdomain: unassigned (`docs/research/05_domain_subdomain_proposal.md` did not exist when this session ran)
* Users named in the file: franchise operations managers (daily/weekly sales by franchise, product mix), regional leads (country/city and franchise size comparisons), supply chain analyst (suppliers per franchise). Source location: section "Primary users".

All source locations below refer to the file above.

## Business rules
| rule | source location |
|---|---|
| `totalPrice` already equals quantity x unitPrice; never recompute revenue from quantity and unitPrice | KPI definitions, row Revenue |
| Average unit price is weighted (Revenue / Units sold), not `AVG(unitPrice)` | KPI definitions, row Average unit price |
| Franchise location (city, country) comes from `sales_franchises`, not the customer's address; "Sales in Japan" means franchises in Japan | Business rules, bullet 2 |
| Customer geography (`sales_customers.city`, `state`, `country`, `continent`) is used only when the question says "customers from" | Business rules, bullet 3 |
| Supplier joins are optional: some franchises reference a `supplierID` not in `sales_suppliers`; use LEFT JOIN, label as "Unknown supplier", never drop their sales | Business rules, bullet 4 |
| Only suppliers with `approved = 'Y'` count as approved suppliers | Business rules, bullet 5 |
| Franchise size order is S, M, L, XL, XXL (sort by this order, not alphabetically) | Business rules, bullet 6 |
| `dateTime` is the transaction timestamp; default grain is day; weeks start on Monday | Business rules, bullet 7 |
| If no period is given, use all available data and state the date range covered | Business rules, bullet 7 |
| Payment method values are amex, mastercard, visa; display capitalised (Amex, Mastercard, Visa) | Business rules, bullet 8 |
| Never expose `cardNumber`, `email_address`, `phone_number`, `address` or full customer names; aggregate customers, do not list individuals | Privacy and exclusions, bullet 1 |
| Do not answer questions needing data the tables do not hold (costs, margins, staffing, inventory, ratings); say the data is not available | Privacy and exclusions, bullet 2 |
| Reviews are text only; no rating column exists | KPI definitions, row Review count |
| Out of scope: forecasting, what-if scenarios, sentiment scoring of review text, writing back to any table | Out of scope for this run |

## KPI formulas (candidate measures)
| kpi | original expression | sql sketch | grain | source location |
|---|---|---|---|---|
| Revenue | SUM(sales_transactions.totalPrice) | `SUM(totalPrice)` | transaction | KPI definitions |
| Units sold | SUM(sales_transactions.quantity) | `SUM(quantity)` | transaction | KPI definitions |
| Transactions | COUNT(DISTINCT sales_transactions.transactionID) | `COUNT(DISTINCT transactionID)` | transaction | KPI definitions |
| Average order value (AOV) | Revenue / Transactions | `SUM(totalPrice) / COUNT(DISTINCT transactionID)` | transaction | KPI definitions |
| Average unit price | Revenue / Units sold | `SUM(totalPrice) / SUM(quantity)` | transaction | KPI definitions |
| Active customers | COUNT(DISTINCT customerID) with at least one transaction in the period | `COUNT(DISTINCT customerID)` over `sales_transactions` in the filtered period | transaction | KPI definitions |
| Revenue per franchise | Revenue / COUNT(DISTINCT franchiseID) with sales in the period | `SUM(totalPrice) / COUNT(DISTINCT franchiseID)` over `sales_transactions` | transaction | KPI definitions |
| Product revenue share | Product revenue / total revenue in the same filter context | needs review: ratio of `SUM(totalPrice)` per product to `SUM(totalPrice)` over all products in the same filters (window or metric-view level total) | transaction by product | KPI definitions |
| Review count | COUNT(*) of media_customer_reviews per franchise | `COUNT(*)` on `media_customer_reviews` grouped by `franchiseID` | review | KPI definitions |

Expected results on the sample data (for benchmarking; claims to verify, source location: "Questions the agent must answer"):

| # | question | expected answer |
|---|---|---|
| Q1 | What was total revenue? | 66,471 (all data, 2024-05-01 to 2024-05-17) |
| Q2 | How many transactions were there? | 3,333 |
| Q3 | Which product had the highest revenue? | Golden Gate Ginger, 11,595 |
| Q4 | Revenue by franchise country, top 3 | Japan 21,591; US 20,736; Australia 8,211 |
| Q5 | How many units were sold? | 22,157 |
| Q6 | Transactions by payment method | Mastercard 1,144; Amex 1,106; Visa 1,083 |
| Q7 | How many franchises are there by size? | S 10, M 10, L 9, XL 10, XXL 9 |
| Q8 | What is the average order value? | about 19.94 (66,471 / 3,333) |
| Q9 | How many customers bought something? | 300 |
| Q10 | Which franchises have the most reviews? | Ranked list from media_customer_reviews (204 reviews across 48 franchises) |

## Join criteria (candidate joins)
| left | right | keys | stated cardinality | source location |
|---|---|---|---|---|
| sales_transactions | sales_franchises | `sales_transactions.franchiseID = sales_franchises.franchiseID` | not stated (implied many-to-one) | Business rules, bullet 1 |
| sales_transactions | sales_customers | `sales_transactions.customerID = sales_customers.customerID` | not stated (implied many-to-one) | Business rules, bullet 1 |
| sales_franchises | sales_suppliers | `sales_franchises.supplierID = sales_suppliers.supplierID` | not stated; optional (LEFT JOIN, some supplierIDs missing from sales_suppliers) | Business rules, bullets 1 and 4 |
| media_customer_reviews | sales_franchises | `media_customer_reviews.franchiseID = sales_franchises.franchiseID` | not stated (implied many-to-one) | Business rules, bullet 1 |

Stated cardinalities are claims for deep EDA (session 04) to verify.

## Filters and parameters (candidate filters)
| name | logic | source location |
|---|---|---|
| Approved supplier | `sales_suppliers.approved = 'Y'` | Business rules, bullet 5 |
| Unknown supplier | franchise `supplierID` with no match in `sales_suppliers` (LEFT JOIN, supplier attributes NULL) -> label "Unknown supplier" | Business rules, bullet 4 |
| Franchise country / city | `sales_franchises.country`, `sales_franchises.city` (default location filter) | Business rules, bullet 2 |
| Customers from (geography) | `sales_customers.city` / `state` / `country` / `continent`, only when the question says "customers from" | Business rules, bullet 3 |
| Period | filter on `sales_transactions.dateTime`; default all available data | Business rules, bullet 7 |
| Payment method | values amex, mastercard, visa; display labels Amex, Mastercard, Visa | Business rules, bullet 8 |
| Franchise size | values S, M, L, XL, XXL with explicit sort order | Business rules, bullet 6 |

## Dimension hierarchies
| hierarchy | levels (top -> bottom) | source location |
|---|---|---|
| Franchise location | country -> city -> franchise | Business rules, bullet 2 (fields from sales_franchises) |
| Customer geography | continent -> country -> state -> city | Business rules, bullet 3 (fields from sales_customers) |
| Time | week (Monday start) -> day -> transaction timestamp (`dateTime`) | Business rules, bullet 7 |
| Franchise size (ordinal) | S < M < L < XL < XXL | Business rules, bullet 6 |

## Open questions

* Product revenue share: confirm whether the total should respect all active filters (file says "same filter context") when implemented as a metric view measure; SQL sketch marked needs review.
* Column names for product, payment method and franchise size are not given in the file; session 04 profiles should confirm them (for example `product`, `paymentMethod`, `size`).
* Weeks starting Monday: confirm metric view time dimensions use ISO weeks (`date_trunc('WEEK', dateTime)` is Monday-based in Databricks SQL).
* Expected answers Q1-Q10 are owner claims; session 10 benchmarks should check them against the data.
