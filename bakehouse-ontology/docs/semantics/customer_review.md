---
term: Customer Review
synonyms: [review, customer feedback, media review]
domain: bakehouse_sales
subdomain: customer_feedback
related_terms: [Review Sentiment, Net Promoter Score, Franchise]
sources:
  - https://en.wikipedia.org/wiki/Customer_satisfaction
---

## Definition
A customer's written account of their experience with a franchise. It is an input to customer satisfaction: how well products and services meet or exceed customer expectation ([Customer satisfaction — Wikipedia](https://en.wikipedia.org/wiki/Customer_satisfaction)).

## Business context
Review volume and content show where outlet experience falls short and are compared across franchises.

## Data usage
`samples.bakehouse.media_customer_reviews`: `review` (text), `franchiseID` (join to `sales_franchises`), `review_date`, `new_id` (row id). Review count = `COUNT(*)`. Reviews are not linked to customers. Review text may contain personal details, so do not expose it as sample values without review.
