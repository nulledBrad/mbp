---
term: Supplier
synonyms: [ingredient supplier, vendor, approved supplier]
domain: bakehouse_sales
subdomain: franchise_network
related_terms: [Franchise, Franchisor]
sources:
  - https://en.wikipedia.org/wiki/Supply_chain_management
---

## Definition
An upstream party in the supply chain that provides raw materials (here, ingredients) that are turned into finished products for end customers. The supply chain spans procurement, operations, logistics and marketing channels ([Supply chain management — Wikipedia](https://en.wikipedia.org/wiki/Supply_chain_management)).

## Business context
Supplier approval, location and ingredient coverage affect product availability and quality across franchises.

## Data usage
`samples.bakehouse.sales_suppliers`, keyed by `supplierID` and joined from `sales_franchises.supplierID`. Dimensions: `name`, `ingredient`, `continent`, `city`, `district`, `size`, `approved` (values to be confirmed by EDA). Supplier count = `COUNT(DISTINCT supplierID)`.
