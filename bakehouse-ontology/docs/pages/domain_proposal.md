# Domain Proposal for UC Pages — bakehouse_sales

Build run `892325824190755`, session 07 (wave 4), 2026-11-28. Proposal only: no domain was created (creating domains is opt-in, design decision 2, and was not approved for this session).

## Existing domains

`GET /api/2.0/domains` returns one domain: `General` (`domains/system-general-domain-383a3d5c-5308-4214-8369-bb2f7b730d29`, tag `system.general_domain`), managed by Databricks. There is no `bakehouse_sales` domain.

## Proposed structure

From [docs/research/05_domain_subdomain_proposal.md](../research/05_domain_subdomain_proposal.md):

| Level | Name | Description | Terms (semantics) |
|---|---|---|---|
| Domain | bakehouse_sales | Sales, franchise network, supplier and customer-review knowledge for a franchised bakery chain | all 33 |
| Subdomain | sales | POS transactions and the customers who make them | 14 |
| Subdomain | franchise_network | Franchise outlets and their ingredient suppliers | 5 |
| Subdomain | customer_feedback | Customer reviews of franchise outlets | 3 |

The 11 terms from `docs/context/glossary_candidates/` that are not in `docs/semantics/` are tagged `unassigned` (session 03 ran before the proposal existed). Suggested mapping, for the owner to confirm: Active Customers, Average Order Value, Average Unit Price, Product Revenue Share, Transactions -> sales; Approved Supplier, Franchise Location, Franchise Size, Revenue per Franchise, Unknown Supplier -> franchise_network; Review Count -> customer_feedback.

## Owner decision

The page API needs a domain to create a page in. When knowledge pages are enabled, choose one for the re-run:

* [ ] Create a `bakehouse_sales` domain (and subdomains if wanted) and approve session 07 to use it, or
* [ ] Approve creating the pages as DRAFT in the existing `General` domain.
