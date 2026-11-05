# UI / UX — Stock Report

Follows `UI_UX_STANDARDS.md` §12.

`PageHeader` "Report › Stock Report" (no description; About links: Stock Check Report, Opening Stock Report) with **Export CSV** (shows a spinner while exporting). Type tabs with counts (Recipe list pattern) above the filter bar; filter bar in one row from `lg`: search · Location · Show. Summary line: "128 items · total value ₹4,52,310.50".

Table (fits without sideways scroll at 1280 px): ▸ · Item (name bold, alias caption) · Type · Quantity (right, "12.5 kg") · Reorder level (right) · Status (centred chip: *Low* warning, *Out* error) · Unit cost ₹ (right) · Value ₹ (right, bold). Expand row shows indented location lines (Location · Quantity · Status · Value) on a subtle background. Paging footer (25 / page default); skeletons; empty / no match / error / no location states.

Phone (< sm): card per item — name + status chip, type · quantity, value; location lines listed inside the card.

Theme tokens only; ₹ with 2 decimals; quantities up to 3 decimals.
