# 01 — View, search and filter brands

## Summary

Inventory → Brand lists every brand of the company, active first then by name, with its sub-brands (as chips) and the number of active raw materials.

## Actors & preconditions

- **Actor:** a logged-in company user; permissions per step below.

## Main flow

1. The page shows the header ("Inventory › Brand", title "Brand", About), filter bar, skeleton rows.
2. Columns: Brand Name · Sub Brands (chips of its active sub-brands, left) · Raw Materials (active raw materials, centred) · Status (centred) · Actions (Edit, switch; right).
3. Search from the first character (trimmed, case-insensitive) on brand name; Status filter; Clear filters.
4. Footer "Showing X of Y brands"; no pagination.

## Alternative flows

- **None yet.** "No brands yet. Add the manufacturer brands of what you buy." + Add (with `create_brand`).
- **No match.** "No brands match your filters." + Clear filters.
- **Load error.** "We couldn't load your brands. Check your connection and try again." + Retry; toast "Failed to load brands".

## Acceptance criteria

1. Given CO-A's brands (active and inactive), then all are listed, active first, none of CO-B's.
2. Given a brand with 2 active sub-brands and 3 active raw materials, then 2 chips and Raw Materials "3".
3. Search from the first letter; Status filter; Clear filters.
4. Empty / no-match / load-error states as above.
5. No pagination; footer count.
6. No `create_brand` → no Add.

## Platform notes

### RMS API

- `POST /Setting/GetStockTypeWithPagination` — `[CompanyAuthorize]`; paging / search ignored; returns `activeSubBrandCount`, `activeItemCount`, `updatedAt`. `POST /Setting/GetStockSubTypeWithPagination` for the chips.

### Data — Tenant DB

- `SP_GetStockTypeWithPagination` reads `StockType`, `StockSubType`, `StockItem`.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

See [`ui-ux.md`](./ui-ux.md).

## Configuration

None.

## Out of scope

None.

## Open questions

None.
