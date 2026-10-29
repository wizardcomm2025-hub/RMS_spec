# Project overview — intake

> Working notes for this topic, before a formal spec exists. Accumulates inbound
> information from clients/stakeholders and Q&A from interview sessions. Specs
> supersede this file when they're written; nothing here is ever deleted.

Status: collecting

<!-- Status values: collecting | spec-written | abandoned -->

---

## What we've been told

Standalone facts, business rules, constraints, and client input — anything we know about this topic that needs to land somewhere. No question needed.

- From the code (2026-10-28): RMS is multi-company. The tenant is resolved from the request `Origin` host via the Central DB site registry; company data lives in a Tenant DB that several companies may share, filtered by `CompanyId`.
- From the code: SuperAdmin and support logins skip tenant resolution and use `/SuperAdmin/*`, `/Users/SuperAdminLogin` and `/Users/SupportLoginAsCompany`.
- From the code: the admin dashboard hides actions using per-menu permissions (`PERMISSIONS.<Module>.Create/Edit/Delete`), managed through User Management → Roles.
- Decided 2026-10-28: specs and test cases cover the admin dashboard only; the customer storefront is out of scope for now.

---

## Interview Q&A

Questions and answers from grill-me / spec-intake sessions or any other clarify-before-draft conversation. Append new entries at the bottom; do not edit prior entries.

---

## Open

Things still unresolved. When an item is answered, move it into "What we've been told" or "Interview Q&A" above, and append the resolution date in italics to the closing entry. Do not delete the open item — its history matters.

- What the product is, in the client's words; who the customers are.
- The standard company roles and what each may do.
- Supported languages.
- Should menu permissions also be enforced by the API (today only the UI checks them)? _Resolved 2026-10-28 for Location (LOC-Q1): yes. Other modules decide in their own spec._

---

## Linked specs

Once formal specs are written from this material, link them here. Until then this section stays empty.

- (none yet)
