# Tester Test Cases — 01 View and Search Drivers

---

## Tests

| ID | AC | Persona | Action / Steps | Expected UI Result |
|---|---|---|---|---|
| `DRV-01-T01` | AC 1, 2 | `A-FULL` | Navigate to Setting › Driver Master in sidebar. | Page title shows "Driver Master", breadcrumb "Setting › Driver Master", About button, Refresh icon, and "＋ Add Driver". Table loads company drivers. |
| `DRV-01-T02` | AC 3 | `A-FULL` | Type "Rajesh" in the search box. | Table filters immediately to show drivers with "Rajesh" in their name. |
| `DRV-01-T03` | AC 3 | `A-FULL` | Type "142011" in the search box. | Table filters to show driver whose license number contains "142011". |
| `DRV-01-T04` | AC 4 | `A-FULL` | Select Status filter: "Active". | Only active drivers are displayed in the table. |
| `DRV-01-T05` | AC 4 | `A-FULL` | Select Status filter: "Inactive". | Only inactive drivers are displayed in the table. |
| `DRV-01-T06` | AC 5 | `A-FULL` | Click "Clear filters" when search or status filter is applied. | Search text clears, status resets to "All", and full driver list is restored. |
| `DRV-01-T07` | AC 7 | `A-FULL` | Search for "NonExistentDriverXYZ". | Empty state displays: "No drivers match your filters." with a "Clear filters" button. |
| `DRV-01-T08` | AC 7 | New tenant | Log in to a fresh company without drivers. | Empty state displays: "No drivers yet. Add your first driver for deliveries and stock transfers." with "＋ Add Driver" button. |
| `DRV-01-T09` | AC 8 | `A-FULL` | Disable network in DevTools and click Refresh; then re-enable network and click **Retry**. | Offline message displays: "We couldn't load your drivers. Check your connection and try again." with **Retry** button, and toast "Failed to load drivers". Clicking Retry successfully reloads the drivers. |
| `DRV-01-T10` | AC 9 | `A-VIEW` | Log in as read-only user `A-VIEW` and open Driver Master. | Driver table is visible. "＋ Add Driver" button, Edit action icons, and Active switches are completely hidden. |
