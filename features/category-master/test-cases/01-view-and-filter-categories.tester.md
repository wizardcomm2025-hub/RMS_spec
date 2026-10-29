# 01 — View, search and filter categories — tester test cases

- Use case: [`../01-view-and-filter-categories.md`](../01-view-and-filter-categories.md) · Screen: [`../ui-ux.md`](../ui-ux.md) · Fixtures: [`README.md`](./README.md#test-data)

All cases: log in as A-ADMIN on HOST-A and open Setting → Category Master, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `CAT-01-T01` | AC 1 | Fixtures loaded | Open the page. | Breadcrumb "Setting › Category Master", title "Category Master". Main rows: Dairy, Vegetables (RM), Vegetables (FG), Old Stock — active first, then by name, so Old Stock is last. "Laptops", "Closed Group" and CO-B's "Spices" are not listed. |
| `CAT-01-T02` | AC 2 | — | Look at Old Stock. | Status chip **Inactive**; Vegetables and Dairy show **Active**. |
| `CAT-01-T03` | AC 3 | — | Type "leafy" in Search. | Vegetables (RM) shown expanded with Leafy Greens under it; nothing else. |
| `CAT-01-T04` | AC 3 | — | Search "veg", Category type "Raw Material", Status "Active". Then change Category type to "Finished Goods". | First: Vegetables (RM) only, expanded with its active sub-categories (Leafy Greens, Root Vegetables). Then: Vegetables (FG) only. |
| `CAT-01-T05` | AC 4 | — | Search "zzz", then click **Clear filters**. | "No categories match your filters." with Clear filters; after clicking, the full list returns and Clear filters disappears. |
| `CAT-01-T06` | AC 5 | A company with no item categories | Open the page there. | "No categories yet. Add your first category to organise your items." with an Add button. |
| `CAT-01-T07` | AC 6 | — | DevTools → Network → Offline; click Refresh. Go online; click **Retry**. | Inline "We couldn't load your categories. Check your connection and try again." with Retry, toast "Failed to load categories"; after Retry the list loads. |
| `CAT-01-T08` | AC 7 | — | Look at Vegetables (RM); click its expand control; click again. | "2 sub" chip; expanding shows Leafy Greens and Root Vegetables indented with ↳; collapsing hides them. |
| `CAT-01-T09` | — | — | Removed 2026-10-28: no pagination on this page (see CAT-01-T16). | — |
| `CAT-01-T10` | AC 9 | — | Log in as A-VIEW; open the page. | List shows; no **＋ Add Category**, no Edit icon, no switch. |
| `CAT-01-T11` | UI AC 1 | Rename script run | Look at the sidebar, breadcrumb, title, buttons and browser tab. | "Category Master" everywhere; tab "Category Master - RMS"; no "Item Category" or "Stock group" text. |
| `CAT-01-T12` | UI AC 2 | — | View at 375, 768, 1280, 1920 px. | No horizontal page scroll. 375: card list, stacked filters, full-width Add. 768: table without Parent. |
| `CAT-01-T13` | UI AC 3 | — | Click Refresh. | Thin progress bar on the card; rows stay visible. |
| `CAT-01-T14` | UI AC 5 | — | Keyboard only: Tab to Search, the expand control (Space), Edit and the switch. | All reachable in visual order with visible focus; the expand control announces Expand / Collapse <name>. |
| `CAT-01-T15` | D12 | — | Open Setting → Menu Section; open About; click "Category Master". | Opens this page (no 404). |
| `CAT-01-T16` | AC 8 | A company with more than 25 categories | Open the page; scroll to the bottom. | Every category is on one page; no rows-per-page or page controls; footer "Showing X of Y categories" with X = Y. |
| `CAT-01-T17` | AC 11 | — | Type "veg" (matches the main category Vegetables, not its subs' names). Then type "VEG" (the code). | Both times Vegetables (RM) is shown expanded with Leafy Greens and Root Vegetables under it; Vegetables (FG) is shown too. |
