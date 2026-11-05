# UI / UX — Raw Material

> §12 recipe with **server paging** (Q4), like User List; badges centred, Actions right; desktop density.

- `PageHeader`: "Inventory › Raw Material", title "Raw Material", About, ⟳, **＋ Add Raw Material** (`create_raw_material`). Tab "Raw Material - RMS".
- Filters: Search ("Search name, alias, SKU or reference...") · Category (`SearchableSelect`) · Status · Clear filters — sent to the API (300 ms, from the first character); page resets to 1.
- Columns: Name (`CellText`: name + alias) · Category · Unit · Cost (right, 2 decimals) · Status chip (centred) · Actions (Edit · Delete only when unused · switch; right).
- Footer: "1–25 of N raw materials", rows per page 10 / 25 / 50, page buttons.
- Dialog: keeps today's layout (Raw material + Vendors tabs, preview, cost history). Category, Brand, Sub-brand and Parent use `SearchableSelect`; Used by uses `SearchableMultiSelect`. Main unit read-only with "Can't change: already in use" when `isUsed`. Every refusal shows the API message and keeps the dialog open. No status switch in the dialog.
- Confirmations: use cases 03 and 04. States: skeleton, refresh bar, empty / no-match / load-error. Phone: cards.
