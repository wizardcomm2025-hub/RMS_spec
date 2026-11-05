# 05 — Delete a recipe that was never used

## Summary

A user with `delete_recipe` deletes a recipe that has never been used (Q2). Its own ingredient lines go with it.

## Main flow

1. Delete (enabled only when unused) → "Delete recipe": "Delete **<name>**? This can't be undone." → "Recipe deleted".
2. The recipe is marked deleted with its ingredient lines, location links, POS mapping and zero opening-stock rows.

## Alternative flows

- "This recipe is in use and can't be deleted. Deactivate it instead." · "Recipe not found" · "You don't have permission to perform this action.".

## Acceptance criteria

1. Unused recipe → gone.
2. Recipe used as an ingredient, mapped to a POS item, on an order / KOT, in production, on a requisition / transfer / wastage, or with non-zero stock → refused.
3. Another company's id → not found.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
