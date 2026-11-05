# 03 — Switch a vendor on or off

## Summary

A user with `edit_vendor` switches a vendor off (no longer offered on new purchase orders or raw-material links; stays on existing documents) or back on.

## Main flow

1. Off → "Deactivate vendor": "Deactivate **<name>**? It won't be offered on new purchase orders. You can activate it again at any time." → "Vendor deactivated".
2. On → "Activate **<name>**?" → "Vendor activated".

## Alternative flows

- "Vendor not found" · "You don't have permission to perform this action.".

## Acceptance criteria

1. Off → Inactive; not in the PO vendor picker (an existing PO keeps showing its vendor).
2. Another company's id → not found.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB not touched; Tally per Q4.

## Open questions

None.
