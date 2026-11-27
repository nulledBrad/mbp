---
term: Review Sentiment
synonyms: [sentiment, sentiment score, positive review share, opinion]
domain: bakehouse_sales
subdomain: customer_feedback
related_terms: [Customer Review, Net Promoter Score]
sources:
  - https://en.wikipedia.org/wiki/Sentiment_analysis
---

## Definition
The attitude (positive, negative, neutral) expressed in a review, identified with sentiment analysis: the use of natural language processing and text analysis to identify, extract and quantify affective states and subjective information ([Sentiment analysis — Wikipedia](https://en.wikipedia.org/wiki/Sentiment_analysis)).

## Business context
Positive and negative review shares by franchise give a satisfaction signal when no numeric rating exists.

## Data usage
Not yet mapped: `samples.bakehouse.media_customer_reviews.review` has no sentiment label. A label would have to be derived (for example by an AI classification function), which needs owner approval. Then positive share = positive reviews / `COUNT(*)` per `franchiseID`.
