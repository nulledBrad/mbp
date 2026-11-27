---
term: Transactions
synonyms: [orders, transaction count, number of sales]
domain: bakehouse_sales
subdomain: unassigned
related_terms: [Revenue, Average Order Value]
sources: [/Volumes/genie_sdk_bakehouse/inputs/requirements/bakehouse_business_requirements.md]
---
## Definition
Transactions is the number of distinct sales transactions.

## Business context
Transaction counts show customer traffic at franchises and are broken down by payment method (Amex, Mastercard, Visa).

## Data usage
COUNT(DISTINCT sales_transactions.transactionID). Payment method values amex, mastercard, visa are displayed capitalised.
