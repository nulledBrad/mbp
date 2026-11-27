---
term: Personal Data
synonyms: [PII, personally identifiable information, personal information]
domain: bakehouse_sales
subdomain: sales
related_terms: [Customer, Payment Method]
sources:
  - https://en.wikipedia.org/wiki/Personal_data
  - https://gdpr-info.eu/art-4-gdpr/
  - https://oag.ca.gov/privacy/ccpa
---

## Definition
Any information related to an identifiable person ([Personal data — Wikipedia](https://en.wikipedia.org/wiki/Personal_data)). The GDPR defines personal data in Article 4 ([Art. 4 GDPR](https://gdpr-info.eu/art-4-gdpr/)).

## Business context
Privacy laws give consumers rights over their personal information, such as the CCPA rights to know, delete and opt out of sale or sharing ([CCPA — California DOJ](https://oag.ca.gov/privacy/ccpa)). Analytics should use aggregates and surrogate keys.

## Data usage
In `samples.bakehouse.sales_customers`: `first_name`, `last_name`, `email_address`, `phone_number`, `address`, `postal_zip_code`. In `samples.bakehouse.sales_transactions`: `cardNumber` (cardholder data). Exclude all of these from metric views and agents. Use `customerID` only for joins and distinct counts.
