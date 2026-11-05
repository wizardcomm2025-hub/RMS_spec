# 01 — View, search and filter recipes

## Summary

Inventory → Recipe shows the company's recipes (active and inactive, never deleted) in type tabs (Semi-Finished / Finished), page by page from the server (Q4).

## Main flow

1. Header "Inventory › Recipe", title "Recipe", About; type tabs with counts; filters Search · Category · Menu Section (Finished tab) · Status · Clear filters.
2. Columns: Name (`CellText`: name + alias, veg dot on Finished) · Category · Menu Section (Finished tab) · Cost / unit (right; "Manual" chip when overridden) · Status (centred) · Actions (Edit · Delete greyed with the reason when used · switch; right).
3. Footer "1–25 of N recipes" with rows per page and page buttons.

## Alternative flows

- Empty: "No recipes yet. Add what your kitchen makes." + Add (with `create_recipe`).
- No match: "No recipes match your filters." + Clear filters.
- Load error: "We couldn't load your recipes. Check your connection and try again." + Retry.

## Acceptance criteria

1. Only the company's non-raw items; deleted ones never show; tab counts and total exact.
2. Filters go to the API; page resets to 1.
3. Cost / unit is the calculated cost, or the manual one with a "Manual" chip.
4. Delete is disabled with "In use … Switch it off instead." on used recipes.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
