---
term: Franchise Size
synonyms: [store size, location size]
domain: bakehouse_sales
subdomain: unassigned
related_terms: [Revenue per Franchise, Franchise Location]
sources: [/Volumes/genie_sdk_bakehouse/inputs/requirements/bakehouse_business_requirements.md]
---
## Definition
Franchise size is the size class of a franchise location, one of S, M, L, XL or XXL.

## Business context
Regional leads compare sales across franchise sizes. Sizes are ordinal and are always sorted S, M, L, XL, XXL, not alphabetically.

## Data usage
Attribute of sales_franchises. Use an explicit sort order (S=1, M=2, L=3, XL=4, XXL=5).
