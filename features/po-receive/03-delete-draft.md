# 03 — Delete a draft receive

## Summary
A user with `receive_receive_asset` deletes a **draft** receive (it never touched stock).

## Main flow
Delete on a Draft row → "Delete receipt GRN/…?" → Delete → `IsActive = 0` on the head and lines → "Receipt deleted".

## Alternative flows
Posted → button not shown; via API "Posted receipts can't be changed." · Already deleted / other company → "Receipt not found".

## Acceptance criteria
1. Only drafts; stock untouched; the PO's remaining quantities unchanged.
2. A PO whose only receive was a deleted draft can be cancelled again.

## Open questions
None.
