---
term: Franchise Location
synonyms: [franchise country, franchise city, store location, sales location]
domain: bakehouse_sales
subdomain: unassigned
related_terms: [Revenue, Franchise Size, Revenue per Franchise]
sources: [/Volumes/genie_sdk_bakehouse/inputs/requirements/bakehouse_business_requirements.md]
---
## Definition
Franchise location is the city and country of the franchise where a sale took place.

## Business context
Geographic sales questions such as "sales in Japan" refer to franchise location. Customer geography is used only when a question says "customers from".

## Data usage
sales_franchises.city and sales_franchises.country, joined to sales_transactions on franchiseID. Do not use sales_customers address fields for sales geography.
