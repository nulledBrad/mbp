---
term: Approved Supplier
synonyms: [approved vendor]
domain: bakehouse_sales
subdomain: unassigned
related_terms: [Unknown Supplier]
sources: [/Volumes/genie_sdk_bakehouse/inputs/requirements/bakehouse_business_requirements.md]
---
## Definition
An approved supplier is an ingredient supplier whose approved flag is 'Y'.

## Business context
The supply chain analyst tracks which suppliers serve which franchises; only approved suppliers count when a question asks about approved suppliers.

## Data usage
sales_suppliers.approved = 'Y'. Suppliers join to franchises on sales_franchises.supplierID = sales_suppliers.supplierID.
