---
term: RFM Segmentation
synonyms: [RFM, recency frequency monetary, RFM analysis]
domain: bakehouse_sales
subdomain: sales
related_terms: [Customer, Customer Lifetime Value, Repeat Customer Rate]
sources:
  - https://en.wikipedia.org/wiki/RFM_(market_research)
---

## Definition
RFM is a method for analyzing customer value and segmenting customers, commonly used in database and direct marketing, especially in retail ([RFM — Wikipedia](https://en.wikipedia.org/wiki/RFM_(market_research))).

## Business context
Segments customers by how recently, how often and how much they buy, to target promotions and loyalty offers.

## Data usage
Per `customerID` in `samples.bakehouse.sales_transactions`: recency = as-of date minus `MAX(dateTime)`, frequency = `COUNT(DISTINCT transactionID)`, monetary = `SUM(totalPrice)`. Segment thresholds are not yet defined (owner decision).
