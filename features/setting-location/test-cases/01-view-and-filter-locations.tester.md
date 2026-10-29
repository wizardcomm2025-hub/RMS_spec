# 01 — View, search and filter locations — tester test cases

- Use case: [`../01-view-and-filter-locations.md`](../01-view-and-filter-locations.md)
- Screen design: [`../ui-ux.md`](../ui-ux.md)
- Fixtures: [`README.md`](./README.md#test-data)

## Test cases

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `LOC-01-T01` | AC 1 | Fixtures loaded | Log in as A-ADMIN on HOST-A. Open Setting → Location. | Breadcrumb "Setting › Location", title "Location". Kitchen (with **Default** badge), Outlet One and Old Store are listed; active ones first, then by name, so Old Store is last. Closed Depot and CO-B's Central Store are **not** listed. Only one "Kitchen" row is shown. |
| `LOC-01-T02` | AC 2 | — | On the list, look at Old Store. | Status chip **Inactive** (grey). Kitchen and Outlet One show **Active** (green). |
| `LOC-01-T03` | AC 3 | — | Set Status = Inactive, Type = Warehouse, type "old" in Search. Then change Type to Outlet. | First: only Old Store. After changing Type: "No locations match your filters." |
| `LOC-01-T04` | AC 4 | — | Type "zzz" in Search. Click **Clear filters**. | "No locations match your filters." with Clear filters. After clicking it: Search empty, Type and Status reset to All, full list back; Clear filters disappears. |
| `LOC-01-T05` | AC 5 | CO-C has no locations | Log in on HOST-C as a CO-C admin; open Location. | "No locations yet. Add your first outlet, warehouse or kitchen." with an Add button. |
| `LOC-01-T06` | AC 6 | — | Open the page, switch the browser to offline (DevTools → Network → Offline), click Refresh. Switch back online, click **Retry**. | Offline: inline error with **Retry** and the toast "Failed to load locations"; the page does not go blank. After Retry online: the list loads. |
| `LOC-01-T07` | AC 8 | CO-P has 30 locations | Log in on HOST-P; open Location. Change rows per page to 10, then 50. | Footer "Showing 25 of 30" and pagination with 10 / 25 / 50. With 10: 3 pages. With 50: all 30 on one page. |
| `LOC-01-T08` | AC 9 | — | Log in as A-VIEW; open Location. | The list shows, but there is no **＋ Add Location** button, no Edit icon and no switch on any row. |
| `LOC-01-T09` | Main flow 5 | — | Type "  KITCHEN  " in Search. | Kitchen is shown (search ignores case and surrounding spaces). |
| `LOC-01-T10` | UI AC 1 | — | View the page at 375, 768, 1280 and 1920 px wide (DevTools device toolbar). | No horizontal page scroll at any width. 375: card list, filters stacked, Add button full width under the title. 768: table without the Address column. 1280 / 1920: full table. |
| `LOC-01-T11` | UI AC 4 | — | Without a mouse: Tab to Search, type, Tab to the Edit icon and the switch of a row. | Every control is reachable in visual order with a visible focus ring; icon buttons announce "Edit" and "Active status for <name>". |
| `LOC-01-T12` | UI AC 2 | — | Click ⟳ Refresh. | A thin progress bar runs on the list card; rows stay visible (no skeleton). |
| `LOC-01-T13` | UI AC 5 | — | Look at the browser tab. | Title "Location - RMS". |
| `LOC-01-T14` | UI AC 1 | — | At 375 px, look at the Kitchen card. | Name with **Default** badge, Type and Status chips, address in up to 2 lines, Requisition "Yes", Edit button and switch at the bottom. Long addresses end with "…". |

## End-to-end run (optional)

T01 → T02 → T09 → T03 → T04 → T12 → T13, then T10 at four widths, then T08 as A-VIEW.

## Reporting a failure

Record in `RMS_API/specs/setting-location/test-results.md`: the test ID, fixture names, browser and width, what the screen showed, and a screenshot.
