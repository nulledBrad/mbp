# 01 Domain Overview — bakehouse_sales

## Summary
The `bakehouse_sales` domain covers a franchised bakery chain: customers buy baked goods at franchise outlets, outlets source ingredients from suppliers, and customers leave free-text reviews about outlets. The core entities are the franchise, the point-of-sale transaction, the customer, the supplier and the review. Standard franchise vocabulary comes from the US franchise disclosure rule ([16 CFR Part 436](https://www.ecfr.gov/current/title-16/chapter-I/subchapter-D/part-436)). The retail vocabulary (transaction, revenue, average selling price) comes from general retail and accounting references. The source tables carry a comment saying the dataset is synthetic. No published standard data model exists for it, so this document maps the entities to general definitions.

## Findings

### Business model: franchising
* In franchising, a franchisor licenses its know-how, procedures, intellectual property and other rights to sell its branded products to a franchisee, who in return pays fees and agrees to comply with obligations ([Franchising — Wikipedia](https://en.wikipedia.org/wiki/Franchising)).
* Payments to a franchisor typically include a royalty for the trademark, reimbursement for training and advisory services, and a percentage of the unit's sales ([Franchising — Wikipedia](https://en.wikipedia.org/wiki/Franchising)). A franchise fee is what the franchisee pays the franchisor for the right to enter the franchise agreement ([Franchise fee — Wikipedia](https://en.wikipedia.org/wiki/Franchise_fee)).
* US regulation defines a *franchisee* as "any person who is granted a franchise" and a *franchisor* as "any person who grants a franchise and participates in the franchise relationship" ([16 CFR 436.1](https://www.ecfr.gov/current/title-16/chapter-I/subchapter-D/part-436)). The same rule requires disclosure tables on the status of franchised and company-owned outlets (Item 20 sample tables) ([16 CFR Part 436](https://www.ecfr.gov/current/title-16/chapter-I/subchapter-D/part-436)).

### Retail bakery
* A bakery produces and sells flour-based baked goods such as bread, cookies, cakes, doughnuts, bagels, pastries and pies. Some retail bakeries also act as cafés that serve coffee and tea ([Bakery — Wikipedia](https://en.wikipedia.org/wiki/Bakery)).

### Point-of-sale transaction
* The point of sale (POS) is the time and place a retail transaction is completed: the merchant calculates the amount owed, and the customer makes a payment in exchange for goods ([Point of sale — Wikipedia](https://en.wikipedia.org/wiki/Point_of_sale)).
* A payment card number (primary account number, PAN) identifies the card used, not necessarily the bank account ([Payment card number — Wikipedia](https://en.wikipedia.org/wiki/Payment_card_number)).

### Supply side
* Supply chain management covers procurement, operations management, logistics and marketing channels that turn raw materials into finished products for end customers ([Supply chain management — Wikipedia](https://en.wikipedia.org/wiki/Supply_chain_management)). Here, suppliers provide ingredients to franchise outlets.

### Customer voice
* Customer satisfaction measures how well products and services meet or exceed customer expectation ([Customer satisfaction — Wikipedia](https://en.wikipedia.org/wiki/Customer_satisfaction)). Free-text reviews are usually quantified with sentiment analysis, which uses natural language processing to identify and quantify affective states ([Sentiment analysis — Wikipedia](https://en.wikipedia.org/wiki/Sentiment_analysis)).

### Core entities
| Entity | General definition | Source table |
|---|---|---|
| Franchise (outlet) | Business unit operated by a franchisee under the franchisor's brand ([16 CFR 436.1](https://www.ecfr.gov/current/title-16/chapter-I/subchapter-D/part-436)) | `samples.bakehouse.sales_franchises` |
| Transaction | Completed POS sale ([Point of sale](https://en.wikipedia.org/wiki/Point_of_sale)) | `samples.bakehouse.sales_transactions` |
| Customer | Person who pays at the POS ([Point of sale](https://en.wikipedia.org/wiki/Point_of_sale)) | `samples.bakehouse.sales_customers` |
| Supplier | Upstream provider of ingredients ([Supply chain management](https://en.wikipedia.org/wiki/Supply_chain_management)) | `samples.bakehouse.sales_suppliers` |
| Review | Customer-authored text about an outlet, input to satisfaction and sentiment ([Customer satisfaction](https://en.wikipedia.org/wiki/Customer_satisfaction)) | `samples.bakehouse.media_customer_reviews` |

### Stakeholders
* Franchisor (brand, network performance, supplier approval), franchisees (outlet sales), customers (purchases, reviews), and suppliers (ingredients), per the franchise relationship described in [Franchising — Wikipedia](https://en.wikipedia.org/wiki/Franchising).

## Relevance to the source tables
| Finding | Table / column | How it is used (measure, dimension, filter, page) |
|---|---|---|
| Franchise / franchisee | `sales_franchises.franchiseID`, `name`, `city`, `district`, `country`, `size` | dimension; page |
| Outlet-to-supplier link | `sales_franchises.supplierID` -> `sales_suppliers.supplierID` | join |
| POS transaction | `sales_transactions.transactionID`, `dateTime`, `quantity`, `unitPrice`, `totalPrice` | measures (revenue, units, transaction count) |
| Payment | `sales_transactions.paymentMethod` (dimension), `cardNumber` (sensitive: exclude, see 03) | dimension; filter |
| Product | `sales_transactions.product` | dimension |
| Customer | `sales_customers.customerID`, `city`, `state`, `country`, `continent`, `gender` | dimension; name and contact columns are PII (see 03) |
| Supplier | `sales_suppliers.name`, `ingredient`, `continent`, `size`, `approved` | dimension; filter |
| Review / sentiment | `media_customer_reviews.review`, `franchiseID`, `review_date` | measure (review count), text input for sentiment |

## Open questions
* Is `sales_transactions` one row per line item or per basket? The answer changes the transaction count (distinct `transactionID` vs rows). Session 04 (EDA) should confirm the grain.
* Does a franchise have exactly one supplier (`sales_franchises.supplierID`)? Session 04 should confirm the cardinality.
* What does `sales_suppliers.approved` mean (approved vendor flag) and what are its values?
* Currency and unit of `unitPrice` / `totalPrice` (stored as integers).

## Sources
1. [Franchising — Wikipedia](https://en.wikipedia.org/wiki/Franchising) — Wikipedia
2. [16 CFR Part 436 — Disclosure Requirements and Prohibitions Concerning Franchising](https://www.ecfr.gov/current/title-16/chapter-I/subchapter-D/part-436) — US eCFR
3. [Franchise fee — Wikipedia](https://en.wikipedia.org/wiki/Franchise_fee) — Wikipedia
4. [Bakery — Wikipedia](https://en.wikipedia.org/wiki/Bakery) — Wikipedia
5. [Point of sale — Wikipedia](https://en.wikipedia.org/wiki/Point_of_sale) — Wikipedia
6. [Payment card number — Wikipedia](https://en.wikipedia.org/wiki/Payment_card_number) — Wikipedia
7. [Supply chain management — Wikipedia](https://en.wikipedia.org/wiki/Supply_chain_management) — Wikipedia
8. [Customer satisfaction — Wikipedia](https://en.wikipedia.org/wiki/Customer_satisfaction) — Wikipedia
9. [Sentiment analysis — Wikipedia](https://en.wikipedia.org/wiki/Sentiment_analysis) — Wikipedia
