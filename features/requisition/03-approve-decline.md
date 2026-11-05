# 03 — Approve or decline

## Summary
A user with `approve_requisition_for_stock_transfer` decides on a **pending** requisition from its view page.

## Main flow
1. View page of a pending requisition: **Approve** and **Decline** (approvers only).
2. Approve → confirm "Approve REQ/…? It goes to Inbound Requisition at Central Store." → status Approved, approved by / at = caller → "Requisition approved".
3. Decline → dialog with a required note (≤ 500) → status Declined, declined by / at / note → "Requisition declined".

## Alternative flows
Not pending → "Only pending requisitions can be approved or declined." · stale → reload message · no permission → permission message.

## Acceptance criteria
1. `ApprovedBy` / `DeclinedBy` always the caller (R3).
2. Approved ones appear in Inbound Requisition; declined don't.

## Open questions
None.
