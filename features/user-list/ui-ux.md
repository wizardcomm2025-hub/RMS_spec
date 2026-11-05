# UI / UX — User List

> §12 recipe with **server paging** (Q4); badges centred, Actions right; desktop density.

- `PageHeader`: "User Management › User List", title "User List", About (kept), ⟳, **＋ Add User** (`create_userlist`). Tab "User List - RMS".
- Filters: Search ("Search name, email or phone...") · Status · Clear filters — sent to the API (debounced 300 ms, from the first character); page resets to 1.
- Columns: Name (`CellText`: full name + user name in the detail line; "You" chip on your own row) · Email · Phone · Roles (chips, centred; "System" roles outlined) · Status chip (centred) · Actions (Edit · switch, right; no switch on your own row).
- Footer: "1–25 of N users", rows per page 10 / 25 / 50, page buttons.
- Form fields: Department, Reporting Head and Alt Reporting Head use `SearchableSelect`; Location and Roles use `SearchableMultiSelect` (type to filter, checkboxes, chips) — UI_UX_STANDARDS §4. Alt Reporting Head can be cleared.
- Form: keeps today's two steps (Details → Roles). Roles step: **searchable multi-select** of active roles (system roles show a "System" badge); your own account shows the roles read-only with "You can't change your own roles or status." Every refusal shows the API message and keeps the step open.
- Status is not in the dialog (the switch was removed; use the list). Confirmations: use case 04. States: skeleton, refresh bar, empty / no-match / load-error (use case 01). Phone: cards.
