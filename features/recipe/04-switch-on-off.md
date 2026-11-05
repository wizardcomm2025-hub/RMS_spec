# 04 — Switch a recipe on or off

## Summary

A user with `edit_recipe` switches a recipe off (no longer offered as an ingredient, for production or on new documents; stays on existing ones) or back on.

## Main flow

1. Off → "Deactivate recipe": "Deactivate **<name>**? It won't be offered for new recipes, production or orders. You can activate it again at any time." → "Recipe deactivated".
2. On → "Activate **<name>**?" → "Recipe activated".

## Alternative flows

- "Recipe not found" · "You don't have permission to perform this action.".

## Acceptance criteria

1. Off → Inactive; not in the ingredient search.
2. On → back.
3. Another company's id → not found.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
