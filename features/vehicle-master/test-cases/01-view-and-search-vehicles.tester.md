# 01 — View, search and filter vehicles — tester test cases

- Use case: [`../01-view-and-search-vehicles.md`](../01-view-and-search-vehicles.md) · Screen: [`../ui-ux.md`](../ui-ux.md) · Fixtures: [`README.md`](./README.md#test-data)

All cases: log in as A-ADMIN on HOST-A and open Setting → Vehicle Master, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `VEH-01-T01` | AC 1 | Fixtures loaded | Open Setting → Vehicle Master. | Breadcrumb "Setting › Vehicle Master", title "Vehicle Master". 10 vehicles listed: active first then by registration number. `VEH-OLD` is last. CO-B vehicle `KA01AB1111` is not shown. |
| `VEH-01-T02` | AC 2 | — | Look at row `MH14ZZ0001` (Old Van). | Status chip displays **Inactive**; active vehicles display **Active**. |
| `VEH-01-T03` | AC 3 | — | Type "mh12" in Search and set Status to "Active". | Only active vehicles whose registration or manufacturer contains "MH12" are shown (e.g. MH12AB1234, MH12CD5678). |
| `VEH-01-T04` | AC 4 | — | Type "toyota" in Search. | MH12AB1234 (Toyota HiAce) is shown because manufacturer matches, proving search covers both fields (D5). |
| `VEH-01-T05` | AC 5 | — | Type "zzz" in Search, then click **Clear filters**. | "No vehicles match your filters." appears with Clear filters; clicking it clears search and restores full vehicle list. |
| `VEH-01-T06` | AC 6 | Company with 0 vehicles | Log in to a newly onboarded company without vehicles. | Empty state: "No vehicles yet. Add your first vehicle for deliveries and stock transfers." with an **＋ Add Vehicle** button. |
| `VEH-01-T07` | AC 7 | — | Turn network Offline in browser DevTools and click Refresh. Turn Online and click **Retry**. | Inline message: "We couldn't load your vehicles. Check your connection and try again." with Retry button, plus toast "Failed to load vehicles". After Retry, vehicles load cleanly. |
| `VEH-01-T08` | AC 8 | — | Scroll to table bottom. | Footer shows "Showing 10 of 10 vehicles". No pagination dropdowns or page numbers. |
| `VEH-01-T09` | AC 9 | — | Log in as A-VIEW (user without `create_vehicle_master` / `edit_vehicle_master`). | Vehicle list is visible, but **＋ Add Vehicle** button, Edit icons, and Active switches are completely hidden. |
| `VEH-01-T10` | AC 10 | — | Look at the top header area. | Header shows breadcrumb, title "Vehicle Master", About button, Refresh icon, and Add button. The redundant one-line short description is omitted. |
