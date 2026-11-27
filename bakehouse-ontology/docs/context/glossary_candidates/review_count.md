---
term: Review Count
synonyms: [number of reviews, customer reviews, review volume]
domain: bakehouse_sales
subdomain: unassigned
related_terms: [Franchise Location]
sources: [/Volumes/genie_sdk_bakehouse/inputs/requirements/bakehouse_business_requirements.md]
---
## Definition
Review count is the number of customer reviews recorded for a franchise.

## Business context
Shows customer feedback volume per franchise. Reviews are text only; there is no rating, and sentiment scoring is out of scope.

## Data usage
COUNT(*) of media_customer_reviews grouped by franchiseID, joined to sales_franchises on franchiseID. Questions about ratings are answered as data not available.
