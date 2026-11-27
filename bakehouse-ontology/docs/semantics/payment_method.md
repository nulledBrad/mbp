---
term: Payment Method
synonyms: [payment type, tender type, payment channel]
domain: bakehouse_sales
subdomain: sales
related_terms: [Transaction, Revenue]
sources:
  - https://en.wikipedia.org/wiki/Point_of_sale
  - https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard
---

## Definition
The means by which the customer pays at the point of sale, where the merchant indicates the payment options available ([Point of sale — Wikipedia](https://en.wikipedia.org/wiki/Point_of_sale)).

## Business context
Revenue share by payment method informs payment processing costs and POS setup. Card data is cardholder data under PCI DSS ([PCI DSS — Wikipedia](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)).

## Data usage
Dimension `paymentMethod` in `samples.bakehouse.sales_transactions`. Revenue share = `SUM(totalPrice)` per method / total `SUM(totalPrice)`. Never expose `cardNumber`.
