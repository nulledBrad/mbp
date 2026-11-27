---
term: Unknown Supplier
synonyms: [missing supplier, unmatched supplier]
domain: bakehouse_sales
subdomain: unassigned
related_terms: [Approved Supplier]
sources: [/Volumes/genie_sdk_bakehouse/inputs/requirements/bakehouse_business_requirements.md]
---
## Definition
Unknown supplier is the label for franchises whose supplierID has no matching row in the supplier table.

## Business context
Some franchises reference a supplier that is not in the supplier list. Their sales must still be counted and reported under Unknown supplier.

## Data usage
LEFT JOIN sales_franchises to sales_suppliers on supplierID; when no supplier row matches, label the supplier "Unknown supplier". Never use an inner join that drops these sales.
