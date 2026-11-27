---
term: Customer
synonyms: [buyer, shopper, guest, consumer]
domain: bakehouse_sales
subdomain: sales
related_terms: [Transaction, Repeat Customer Rate, Customer Retention Rate, Customer Lifetime Value, Personal Data]
sources:
  - https://en.wikipedia.org/wiki/Point_of_sale
  - https://en.wikipedia.org/wiki/Personal_data
---

## Definition
The person who pays the merchant at the point of sale in exchange for goods ([Point of sale — Wikipedia](https://en.wikipedia.org/wiki/Point_of_sale)).

## Business context
Customer counts, repeat behavior and geography drive loyalty and marketing decisions. Customer records are personal data about an identifiable person ([Personal data — Wikipedia](https://en.wikipedia.org/wiki/Personal_data)), so only aggregates should be exposed.

## Data usage
`samples.bakehouse.sales_customers`, keyed by `customerID`. It joins `sales_transactions.customerID`. Customer count = `COUNT(DISTINCT customerID)`. Allowed dimensions: `city`, `state`, `country`, `continent` (`gender` only with owner approval). Exclude `first_name`, `last_name`, `email_address`, `phone_number`, `address`, `postal_zip_code`.
