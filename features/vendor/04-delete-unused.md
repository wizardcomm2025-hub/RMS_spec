# 04 — Delete a vendor that was never used

## Summary

A user with `delete_vendor` deletes a vendor that has never been used (Q1).

## Main flow

1. Delete (enabled only when unused) → "Delete vendor": "Delete **<name>**? This can't be undone." → "Vendor deleted".
2. Marked deleted (`IsDelete = 1`) with its contacts and raw-material links switched off.

## Alternative flows

- "This vendor is in use and can't be deleted. Deactivate it instead." · "Vendor not found" · "You don't have permission to perform this action.".

## Acceptance criteria

1. Unused → gone.
2. On any PO, PO receive, raw-material link, barcode or asset disposal → refused.
3. Another company's id → not found.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB not touched; Tally per Q4.

## Open questions

None.
