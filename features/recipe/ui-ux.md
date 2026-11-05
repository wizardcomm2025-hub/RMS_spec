# UI / UX — Recipe

> §12 recipe with **server paging** and type tabs; badges centred, numbers and Actions right.

- `PageHeader`: "Inventory › Recipe", title "Recipe", About, ⟳, **＋ Add Recipe** (`create_recipe`). Tab "Recipe - RMS".
- Type tabs (Semi-Finished Items / Finished Items) with counts; filters Search · Category (`SearchableSelect`) · Menu Section (Finished tab, `SearchableSelect`) · Status · Clear filters.
- Columns: Name (`CellText`; veg / non-veg dot on Finished) · Category · Menu Section (Finished) · Cost / unit ("48.44 / Kg"; "Manual" chip) · Status (centred) · Actions (Edit · Delete disabled with reason when used · switch; right). Footer "1–25 of N recipes".
- Dialog (layout kept): category, sub-category, menu section use `SearchableSelect`; Used by uses `SearchableMultiSelect`. Ingredient lines show unit cost and line cost; a **Cost** card shows batch cost, cost per unit, "Calculated" / "Manual" and a **Manual cost** switch + field. Unit read-only with "Can't change: already in use" when used. Every refusal shows the API message and keeps the dialog open. No status switch in the dialog.
- Confirmations: use cases 04 and 05. States: skeleton, refresh bar, empty / no-match / error. Phone: cards.
