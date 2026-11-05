# 01 — View, search and filter vendors

## Summary

Purchase → Vendor lists the company's vendors (active and inactive, never deleted), one row each.

## Main flow

1. Header "Purchase › Vendor", title "Vendor", About; Search (name, GSTIN, phone, email, city) · Status · Clear filters.
2. Columns: Vendor (`CellText`: name + GSTIN) · Phone · Email · City · Status (centred) · Actions (Edit · Delete disabled with the reason when used · switch; right).
3. Footer "Showing X of Y vendors".

## Alternative flows

- Empty: "No vendors yet. Add the suppliers you buy from." + Add.
- No match: "No vendors match your filters." + Clear filters.
- Load error: "We couldn't load your vendors. Check your connection and try again." + Retry.

## Acceptance criteria

1. Each vendor appears once (primary active contact) (D5).
2. Only the company's vendors; deleted ones never show.
3. Delete disabled with "In use on purchase orders, receipts or raw materials — can't be deleted. Switch it off instead." on used vendors.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB not touched; Tally per Q4.

## Open questions

None.
