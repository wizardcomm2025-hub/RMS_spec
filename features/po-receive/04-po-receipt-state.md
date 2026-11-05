# 04 — Receipt state on the purchase order list

## Summary
The PO list shows how much of each submitted PO has arrived, from posted receives.

## Main flow
Under the status chip of a Submitted PO: "Partly received" or "Fully received" (caption). Nothing for Draft / Cancelled / nothing posted.

## Acceptance criteria
1. Fully received = every line's posted received ≥ ordered (main-unit aware); partly = some posted but not all.
2. Fully received POs no longer appear in the receive PO picker.
3. Cancel stays blocked once any receive exists (unchanged PO rule).

## Open questions
None.
