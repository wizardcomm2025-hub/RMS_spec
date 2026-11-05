# 04 — Delete a raw material that was never used

## Summary

A user with `delete_raw_material` deletes a raw material that has never been used (Q1). Used ones can only be switched off.

## Main flow

1. Delete (only shown when unused) → "Delete raw material": "Delete **<name>**? This can't be undone." → "Raw material deleted".
2. The item is marked deleted (`IsDeleted = 1`, `IsActive = 0`) with its location and vendor links; zero opening-stock rows are removed with it.

## Alternative flows

- "This raw material is in use and can't be deleted. Deactivate it instead." (used meanwhile, or direct API call) · "Raw material not found" · "You don't have permission to perform this action.".

## Acceptance criteria

1. Unused item → gone from the list.
2. Item on any recipe, PO, PO receive, requisition, transfer, wastage, production or consumption line, or with non-zero stock → refused.
3. Another company's id → not found.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
