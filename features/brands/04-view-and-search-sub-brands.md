# 04 — View, search and filter sub-brands

## Summary

Inventory → Sub Brand lists **every** sub-brand of the company — active and inactive, under active and inactive brands (D3) — with its brand and the number of active raw materials (from the list query, D6).

## Actors & preconditions

- **Actor:** a logged-in company user; permissions per step below.

## Main flow

1. Header ("Inventory › Sub Brand", title "Sub Brand", About), filters, skeleton rows.
2. Columns: Sub Brand Name · Brand (name; "(inactive)" when the brand is off) · Raw Materials (centred) · Status (centred) · Actions (right).
3. Search on sub-brand or brand name from the first character; **Brand** filter (all brands); Status filter; Clear filters.
4. Footer "Showing X of Y sub-brands"; no pagination.

## Alternative flows

- **None yet.** "No sub-brands yet. Add product lines under your brands." + Add (with `create_sub_brand`).
- **No match.** "No sub-brands match your filters." + Clear filters.
- **Load error.** "We couldn't load your sub-brands. Check your connection and try again." + Retry; toast "Failed to load sub-brands".

## Acceptance criteria

1. Every CO-A sub-brand is listed, including inactive ones and those under an inactive brand (D3); none of CO-B's.
2. Raw Materials count comes with the list: opening the page makes **one** list call, not one call per raw material (D6).
3. Brand + Status + Search combine.
4. Empty / no-match / error states.
5. No pagination.

## Platform notes

### RMS API

- `POST /Setting/GetStockSubTypeWithPagination` — `[CompanyAuthorize]`; full list with `stockTypeName`, `stockTypeIsActive`, `activeItemCount`, `updatedAt`.

### Data — Tenant DB

- `SP_GetStockSubTypeWithPagination` reads `StockSubType`, `StockType`, `StockItem`.

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
