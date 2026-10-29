# 01 — View and Search Drivers

## User story

As an operations manager or dispatcher,
I want to view and search all company drivers,
So that I know who is registered and available for deliveries and stock transfers.

---

## Acceptance criteria

1. **Company Scoped**: Only drivers belonging to the current tenant company are shown.
2. **All Rows Loaded**: Full list is loaded once (`GetDriverList`); no client or server pagination chunks needed. Footer displays "Showing X of Y drivers".
3. **Search**: Search field filters immediately by `driverName` (case-insensitive substring) or `licNo` (case-insensitive substring).
4. **Status Filter**: Dropdown filters by status: **All**, **Active**, **Inactive**.
5. **Clear Filters**: When filters are active, "Clear filters" button resets search to empty and status to All.
6. **Card & Table Layout**:
   - Desktop/tablet: Sticky-header table showing Avatar + Driver Name, License Number, Status Chip ("Active" / "Inactive"), and Actions (Edit icon, Active switch).
   - Mobile: Responsive card layout showing Avatar, Driver Name, Status Chip, License Number, and Action controls.
7. **Empty & No Match States**:
   - If company has 0 drivers: "No drivers yet. Add your first driver for deliveries and stock transfers." with Add Driver button.
   - If search/filter returns 0 results: "No drivers match your filters." with Clear filters button.
8. **Network Error & Retry**: If the network request fails, displays inline message "We couldn't load your drivers. Check your connection and try again." with a **Retry** button and toast notification "Failed to load drivers".
9. **Permissions**: If user lacks `create_driver_master`, the Add button is hidden. If user lacks `edit_driver_master`, the Edit button and Status toggle switch are hidden.
