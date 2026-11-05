# 01 — View, search and filter raw materials

## Summary

Inventory → Raw Material shows the company's raw materials (active and inactive, never deleted ones) page by page from the server (Q4).

## Main flow

1. Header "Inventory › Raw Material", title "Raw Material", About; filters Search (name, alias, SKU, reference no — sent after a short pause, from the first character) · Category · Status (All / Active / Inactive) · Clear filters.
2. Columns: Name (`CellText`: name + alias) · Category · Main unit · Cost (right) · Status (centred) · Actions (Edit · Delete when unused · switch; right).
3. Footer "1–25 of N raw materials" with rows per page (10 / 25 / 50) and page buttons.

## Alternative flows

- Empty: "No raw materials yet. Add the things you buy and stock." + Add (with `create_raw_material`).
- No match: "No raw materials match your filters." + Clear filters.
- Load error: "We couldn't load your raw materials. Check your connection and try again." + Retry; toast "Failed to load raw materials".

## Acceptance criteria

1. Only the company's Raw-type items; deleted ones never show.
2. Search, category and status go to the API; page resets to 1 when they change; the total is exact.
3. Delete shows only on rows that have never been used.
4. Empty / no-match / error states; phone cards below `sm`.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
