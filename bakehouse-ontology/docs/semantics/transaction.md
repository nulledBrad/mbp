---
term: Transaction
synonyms: [sale, POS transaction, order, ticket, purchase]
domain: bakehouse_sales
subdomain: sales
related_terms: [Revenue, Average Transaction Value, Payment Method, Customer]
sources:
  - https://en.wikipedia.org/wiki/Point_of_sale
---

## Definition
A retail transaction completed at the point of sale: the merchant calculates the amount owed and the customer pays in exchange for goods ([Point of sale — Wikipedia](https://en.wikipedia.org/wiki/Point_of_sale)).

## Business context
Transaction counts measure customer traffic at each franchise. Together with revenue, they give the average transaction value.

## Data usage
One row of `samples.bakehouse.sales_transactions`, identified by `transactionID`. Transaction count = `COUNT(DISTINCT transactionID)`. Each row carries `customerID`, `franchiseID`, `dateTime`, `product`, `quantity`, `unitPrice`, `totalPrice` and `paymentMethod`. Whether one transaction can span several rows (products) is to be confirmed by the EDA session.
