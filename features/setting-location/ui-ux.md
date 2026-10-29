# UI / UX — Setting → Location

> ⭐ **This page is the approved reference design** (2026-10-28) for every list page. Its layout and values are recorded in `RMS_API/specs/UI_UX_STANDARDS.md` §12; other modules copy them. Final review tweaks: outlined 40 px `ListSearch` matching the filter selects; title 20 px; header buttons 40 px; equal 16 px gaps (header → filters → list).

Route: `RMS_NextJS/src/app/(admin-dashboard)/admin/(setting)/warehouse/` · View: `src/pages-sections/vendor-dashboard/warehouse/`.

## 1. Page anatomy

- `PageHeader` (see `RMS_API/specs/global-page-header/spec.md`): breadcrumb "Setting › Location", title "Location", ⓘ About, ⟳ Refresh, **＋ Add Location** on the right (needs `Location.Create`).
- Description: none (omitted to avoid duplicating the About panel).
- About: *"A Location is one outlet, warehouse or central kitchen. Set these up first — Users, stock, requisitions and POS tables are all tracked per location."* "Users" links to User List. Never repeats the description; open on first visit and remembered once dismissed. (Text changed 2026-10-29 at the user's request.)
- Filter bar: Search · Type ▾ · Status ▾ (All / Active / Inactive) · "Clear filters" (only when a filter is set).
- List inside a Card. Footer "Showing X of Y"; client-side pagination when there are more than 25 rows (10 / 25 / 50 per page).
- Browser tab title "Location - RMS".

## 2. Columns / fields

Alignment (UI_UX_STANDARDS §7, strict): badge columns (status, type, requisition) are centred; Actions is the last column and right-aligned.

**List**

| Column | Priority | Content |
|---|:-:|---|
| Location Name | P1 | Bold name + small "Default" badge when set (no POS badge) |
| Type | P1 | Chip (theme `info` for Warehouse, `success` for Outlet, `grey` for others) |
| Address | P2 | One line, ellipsis, full text in a tooltip |
| Requisition | P1 | "Yes" (green check) / "No" (grey dash) |
| Status | P1 | Chip "Active" (`success`) / "Inactive" (`grey`) |
| Actions | P1 | Edit icon (tooltip "Edit") · Active switch (tooltip "Deactivate" / "Activate") |

Phone (xs): card list — Name with badge, Type and Status chips, Address in 2 lines, Requisition, Edit button and switch at the bottom.

**Form fields**

| Field | Required | Rule / hint | Error text |
|---|:-:|---|---|
| Location Name | ✔ | Max 100, character counter, live duplicate check on add and edit | "Location Name is required" · "Location name already exists" |
| Location Type | ✔ | Company's types; starts empty | "Location Type is required" |
| Employees | — | Multi-select with chips; active staff | — |
| Address Line 1 / Line 2 | — | Free text | — |
| City · State · Pincode | — | Pincode numeric, 6 digits | "Pincode must be 6 digits" |
| Default Location | — | Hint under the checkbox; disabled on an inactive location with "Activate the location to make it default" | — |
| Can Take Requisition | — | Hint under the checkbox | — |

POS Outlet is **not** on the form (always sent as true).

## 3. Dialog / form

- Title "Add Location" / "Edit Location — <name>". `fullScreen` on xs.
- Sections: **Basic** (Name + Type side by side on md+), **Staff**, **Address** (Line 1 + 2 side by side; City · State · Pincode in one row on md+), **Settings** (2 columns on sm+).
- Only the content scrolls; header and buttons stay fixed.
- Buttons: **Cancel** · **Add Location** / **Save changes**. Enter submits.
- While submitting: spinner on the primary button; X, Esc and backdrop disabled.
- Closing a changed form: "Discard changes?" — "You have unsaved changes. Discard them" — Cancel · Discard.
- Edit data loads behind `LoadingOverlay`.
- Assigned users with no resolvable name show no chip, and their mapping is kept on save.

## 4. Feedback states

| State | Behaviour (exact text) |
|---|---|
| First load | Skeleton rows |
| Refresh / after save | `LinearProgress` on the card; rows stay visible |
| Empty | Icon + "No locations yet. Add your first outlet, warehouse or kitchen." + Add button |
| No match | "No locations match your filters." + Clear filters |
| Load error | Inline error with **Retry** + toast "Failed to load locations" |
| Other load errors | Toasts "Failed to load location details", "Failed to load employees", "Failed to load location types" |
| Save success | Toast "Location created" / "Location updated" |
| Save failure | API message as a toast (fallback "Failed to save location" when the server can't be reached); inline on Name if duplicate; dialog stays open. Messages include "This location was changed by someone else. Reload and try again." and "Something went wrong. Please try again." |
| Status change | Toast "Location deactivated" / "Location activated"; on failure the API message as a toast and the switch returns to its previous position |

## 5. Confirmations

- Deactivate: "Deactivate **<name>**? It will be hidden from stock, requisition, POS and user screens. You can activate it again at any time." — Cancel · **Deactivate**.
- Activate: "Activate **<name>**?" — Cancel · **Activate**.

## 6. Permissions

- No `Location.Create` → Add button hidden (header and empty state).
- No `Location.Edit` → Edit icon and switch hidden.
- No delete action anywhere.
- The API enforces the same permissions (LOC-Q1); hiding is a convenience, not the protection.

## 7. Responsive

| | xs | sm | md+ |
|---|---|---|---|
| Header | Add full-width below the title; Refresh as icon | Inline | Inline |
| Filters | Stacked, full width | 2 per row | One row |
| List | Card list | Table without Address | Full table |
| Dialog | Full screen | `md` | `md` |

No fixed `minWidth` on the table; no horizontal page scroll at any width.

## 8. Accessibility

- Every icon button has a tooltip and an `aria-label`.
- The switch has `aria-label="Active status for <name>"`.
- Tab order follows the visual order; Enter submits the dialog; focus is visible.

## 9. Shared components

Reused: `PageHeader`, `ListSearch`, `TablePagination`, `ConfirmDialog`, `ModalActionButton`, `LoadingOverlay`, `LinearProgress`, `form-hook`. Local to this module: `location-card.tsx`, `location-badges.tsx`, `location-display.ts`, `location-status.tsx` (status chip + switch), `location-status-confirm.tsx` (the §5 confirmations; the shared `ConfirmDialog` always ends with "?" and can't show the second sentence).

## 10. Template impact

None. No theme or shared-component changes; changes stay inside `pages-sections/vendor-dashboard/warehouse/`.

## UI acceptance criteria

1. At 375, 768, 1280 and 1920 px the page has no horizontal page scroll; the list is a card list on xs and the dialog is full screen on xs (old AC-15).
2. Empty, no-match, load-error (with Retry) and refresh states display as in §4 (old AC-16).
3. No hex colours in the module's files; only theme palette tokens (old AC-17).
4. Search → add → save → toggle works with the keyboard only, with visible focus (old AC-18).
5. The browser tab title is "Location - RMS"; the dialog cannot be closed while saving (D9).
