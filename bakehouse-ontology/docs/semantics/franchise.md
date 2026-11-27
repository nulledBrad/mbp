---
term: Franchise
synonyms: [franchise location, outlet, store, franchise unit, location]
domain: bakehouse_sales
subdomain: franchise_network
related_terms: [Franchisee, Franchisor, Franchise Fee, Supplier, Same-Store Sales]
sources:
  - https://en.wikipedia.org/wiki/Franchising
  - https://www.ecfr.gov/current/title-16/chapter-I/subchapter-D/part-436
---

## Definition
A business unit operated under franchising, where a franchisor licenses its know-how, procedures and intellectual property to a franchisee to sell its branded products ([Franchising — Wikipedia](https://en.wikipedia.org/wiki/Franchising)). US rules regulate franchise disclosure, including outlet status tables ([16 CFR Part 436](https://www.ecfr.gov/current/title-16/chapter-I/subchapter-D/part-436)).

## Business context
The main reporting unit of the network: sales, reviews and supplier links are analyzed per franchise and rolled up by city, country and size.

## Data usage
`samples.bakehouse.sales_franchises`, keyed by `franchiseID`. It joins `sales_transactions.franchiseID`, `media_customer_reviews.franchiseID`, and `sales_suppliers` via `supplierID`. Dimensions: `name`, `city`, `district`, `zipcode`, `country`, `size`, `longitude`, `latitude`. Franchise count = `COUNT(DISTINCT franchiseID)`.
