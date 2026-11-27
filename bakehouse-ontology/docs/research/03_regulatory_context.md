# 03 Regulatory Context — bakehouse_sales

## Summary
The source tables carry a comment saying the dataset is synthetic, so no regulator oversees this data and no reporting metric is mandatory. Four rule sets still shape how the data should be modeled if the same design were applied to a real franchise bakery: the US franchise disclosure rule, US chain menu labeling, payment card security (the transactions table has a card number column), and consumer privacy law (the customers table holds names and contact details). The main modeling consequence: exclude `cardNumber` and direct customer identifiers from metric views and Genie Agents.

## Findings

### Franchise disclosure (US)
* 16 CFR Part 436 sets disclosure requirements for franchisors. It defines franchisor and franchisee and includes sample Item 20 tables on the status of franchised and company-owned outlets (transfers, openings, projected new outlets) ([16 CFR Part 436](https://www.ecfr.gov/current/title-16/chapter-I/subchapter-D/part-436)). An outlet count by status is therefore a standard franchise reporting view. It applies to the franchisor's disclosure document, not to sales analytics.

### Menu labeling (US)
* Restaurants and similar retail food establishments that are part of a chain with 20 or more locations under the same name, regardless of ownership type (including individual franchises), and selling substantially the same menu items must post calories for each standard menu item on menus and menu boards ([21 CFR 101.11](https://www.ecfr.gov/current/title-21/chapter-I/subchapter-B/part-101/subpart-A/section-101.11)). This concerns product information, not sales metrics. The source tables hold no nutrition data.

### Payment card data
* PCI DSS is a global standard for how entities store, process and transmit cardholder data. Cardholder data includes the primary account number (PAN), cardholder name and expiration date ([PCI DSS — Wikipedia](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard); standard published by the [PCI Security Standards Council](https://www.pcisecuritystandards.org/standards/pci-dss/)).
* A card number is the PAN ([Payment card number — Wikipedia](https://en.wikipedia.org/wiki/Payment_card_number)). `sales_transactions.cardNumber` should be treated as cardholder data: never exposed as a dimension, measure or example value.

### Consumer privacy
* The GDPR regulates information privacy in the EU and EEA and governs transfers of personal data outside them ([GDPR — Wikipedia](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation)). Article 4 defines personal data and related terms ([Art. 4 GDPR](https://gdpr-info.eu/art-4-gdpr/)).
* The CCPA gives California consumers the right to know what personal information a business collects and how it is used and shared, the right to delete it (with exceptions), and the right to opt out of its sale or sharing ([CCPA — California DOJ](https://oag.ca.gov/privacy/ccpa)).
* Personal data (PII) is any information related to an identifiable person ([Personal data — Wikipedia](https://en.wikipedia.org/wiki/Personal_data)). `sales_customers` has `first_name`, `last_name`, `email_address`, `phone_number`, `address` and `postal_zip_code`. Free-text `review` may also contain personal details.

### Synthetic dataset
* The table comment on every source table says the Bakehouse dataset is synthetically curated. So none of the rules above creates an obligation for this run; they are design guidance for a production rollout.

## Relevance to the source tables
| Finding | Table / column | How it is used (measure, dimension, filter, page) |
|---|---|---|
| PCI DSS cardholder data | `sales_transactions.cardNumber` | exclude from metric views, agent instructions and sample values |
| GDPR / CCPA personal data | `sales_customers.first_name`, `last_name`, `email_address`, `phone_number`, `address`, `postal_zip_code` | exclude; use `customerID` only for distinct counts and joins |
| Coarse customer geography | `sales_customers.city`, `state`, `country`, `continent` | allowed dimensions (aggregate only) |
| Sensitive attribute | `sales_customers.gender` | dimension only with owner approval |
| Franchise outlet status reporting | `sales_franchises` | page context (outlet counts); no status column exists |
| Menu labeling | `sales_transactions.product` | page context only; no nutrition data |

## Open questions
* Owner: confirm that `cardNumber` and customer contact columns are excluded from every metric view and Genie Agent.
* Owner: is `gender` allowed as a reporting dimension?
* For a production rollout: which jurisdictions do the franchises and customers fall under (customer `country` and `continent` columns suggest more than one)?

## Sources
1. [16 CFR Part 436 — Disclosure Requirements and Prohibitions Concerning Franchising](https://www.ecfr.gov/current/title-16/chapter-I/subchapter-D/part-436) — US eCFR
2. [21 CFR 101.11 — Nutrition labeling of standard menu items in covered establishments](https://www.ecfr.gov/current/title-21/chapter-I/subchapter-B/part-101/subpart-A/section-101.11) — US eCFR
3. [PCI DSS](https://www.pcisecuritystandards.org/standards/pci-dss/) — PCI Security Standards Council
4. [Payment Card Industry Data Security Standard — Wikipedia](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) — Wikipedia
5. [Payment card number — Wikipedia](https://en.wikipedia.org/wiki/Payment_card_number) — Wikipedia
6. [General Data Protection Regulation — Wikipedia](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation) — Wikipedia
7. [Art. 4 GDPR — Definitions](https://gdpr-info.eu/art-4-gdpr/) — gdpr-info.eu
8. [California Consumer Privacy Act (CCPA)](https://oag.ca.gov/privacy/ccpa) — State of California Department of Justice
9. [Personal data — Wikipedia](https://en.wikipedia.org/wiki/Personal_data) — Wikipedia
