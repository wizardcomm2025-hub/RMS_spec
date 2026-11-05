# UI / UX — Vendor

> §12 recipe, client-side search; badges centred, Actions right; desktop density.

- `PageHeader`: "Purchase › Vendor", title "Vendor", About, ⟳, **＋ Add Vendor** (`create_vendor`). Tab "Vendor - RMS".
- Filters: Search ("Search name, GSTIN, phone, email or city...") · Status · Clear filters.
- Columns: Vendor (`CellText`: name + GSTIN) · Phone · Email · City · Status chip (centred) · Actions (Edit · Delete disabled with reason when used · switch; right). Footer "Showing X of Y vendors". Phone cards.
- Dialog (layout kept): required marks only on name and phone; GSTIN upper-cased as typed with the format hint; live duplicate check on name and GSTIN; no status switch in the dialog; every refusal shows the API message and keeps the dialog open; a Tally warning shows as a warning toast after a successful save.
- Confirmations: use cases 03 and 04. States: skeleton, refresh bar, empty / no-match / error.
