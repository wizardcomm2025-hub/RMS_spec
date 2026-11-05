# 01 — View, search and filter purchase orders

## Summary

Purchase → Purchase Order lists the company's POs page by page from the server (Q4).

## Main flow

1. Header "Purchase › Purchase Order", title "Purchase Order", About, ⟳, **＋ New Purchase Order**.
2. Filters: Date range (default: this month) · Status (All / Draft / Submitted / Cancelled) · Vendor (`SearchableSelect`) · Search (PO number or vendor) · Clear filters.
3. Columns: PO Number · Date · Vendor · Items (count) · Total (right) · Status chip (centred: Draft grey, Submitted blue, Cancelled red) · Actions (View · Edit when Draft · Delete when Draft · Cancel when Submitted and nothing received — disabled with the reason otherwise; right).
4. Footer "1–25 of N purchase orders", rows per page, page buttons.

## Alternative flows

- Empty / no-match / load-error states as the recipe.

## Acceptance criteria

1. Only the company's POs; deleted ones never show; the total is exact.
2. Filters and search go to the API; page resets to 1.
3. Cancel disabled with "Goods received against this PO — can't be cancelled." when any receipt exists.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB not touched; notifications on submit as today.

## Open questions

None.
