# 04 — Cancel and duplicate

## Summary
`delete_…` cancels a pending requisition with a reason; `create_…` duplicates any requisition into a **new** pending one.

## Main flow
- Cancel (list row or view page) → dialog with required reason → status Cancelled (stays visible, read-only) → "Requisition cancelled".
- Duplicate (view page) → dialog: date, From, To (prefilled) → new pending requisition with the same lines (items still at From) → opens its edit page → "Requisition REQ/… created from REQ/…".

## Alternative flows
Cancel not pending → "Only pending requisitions can be cancelled." · duplicate source of another company → "Requisition not found".

## Acceptance criteria
1. Duplicate never deletes or changes any existing requisition (R4).
2. Cancelled requisitions never reach Inbound Requisition.

## Open questions
None.
