# 03 — Switch a raw material on or off

## Summary

A user with `edit_raw_material` switches a raw material off (it disappears from pickers on other screens, stays on existing documents) or back on (Q6).

## Main flow

1. Switch off → "Deactivate raw material": "Deactivate **<name>**? It won't be offered on new recipes, orders or requisitions. You can activate it again at any time." → "Raw material deactivated".
2. Switch on → "Activate **<name>**?" → "Raw material activated".

## Alternative flows

- "Raw material not found" · "You don't have permission to perform this action.".

## Acceptance criteria

1. Off → Inactive in the list; no longer in the recipe / PO / requisition item pickers.
2. On → back in the pickers.
3. Another company's id → not found.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
