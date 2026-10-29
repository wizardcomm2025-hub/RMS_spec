# UI / UX — Driver Master

> Follows `RMS_API/specs/UI_UX_STANDARDS.md` and the §12 list recipe (reference: Setting → Location). Only what differs is spelled out. Text the user sees is written here **verbatim**.

Route: `RMS_NextJS/src/app/(admin-dashboard)/admin/(setting)/driver/` · View: `src/pages-sections/vendor-dashboard/driver-master/`.

---

## 1. Page anatomy

- `PageHeader`: breadcrumb "Setting › Driver Master", title "Driver Master", ⓘ About, ⟳ Refresh, **＋ Add Driver** (`create_driver_master`).
- Description: none (omitted from `PageHeader` to avoid duplicating the About panel).
- About: *"Drivers who deliver orders and move stock between locations. Register your drivers with their name, license number, and profile picture, and manage active drivers available for dispatch."*
- Filter bar: Search ("Search by driver name or license number...") · Status ▾ (All / Active / Inactive) · "Clear filters".
- Card list; footer "Showing X of Y drivers"; **no pagination** (returns full company list).
- Browser tab "Driver Master - RMS".

---

## 2. Columns / fields

Alignment (UI_UX_STANDARDS §7, strict): badge columns (status, type, requisition) are centred; Actions is the last column and right-aligned.

| Column | Priority | Content |
|---|:-:|---|
| Driver Name | P1 | Avatar (profile picture or fallback person icon) + Driver Name (weight 600) |
| License Number | P1 | Monospace/letter-spaced text, secondary color |
| Status | P1 | "Active" (`success`) / "Inactive" (`default`) chip |
| Actions | P1 | Edit icon (`edit_driver_master`) · Active switch (`edit_driver_master`, tooltip "Deactivate" / "Activate") |

Phone (xs): card layout — Avatar + Driver Name, Status chip, License Number row, Edit icon and Active switch.

| Field | Required | Rule / hint | Error text |
|---|:-:|---|---|
| Profile Picture | ✕ | Compact row: 64 px avatar (photo or person icon) + "Profile picture (optional)", hint "JPG or PNG, up to 1 MB", **Upload photo** / **Change photo** and **Remove** buttons — no large drop zone (2026-10-29) | "Profile picture must be a JPG or PNG image of 1 MB or less" |
| Driver Name | ✔ | Max 100, counter, letters/numbers/spaces and `& - ( ) ' / . ,` | "Driver name is required" · "Driver name can only contain letters, numbers, spaces and & - ( ) ' / . ," |
| License Number | ✔ | Max 50, letters, numbers, hyphens and spaces, uppercase, live duplicate check (active and inactive drivers) | "License number is required" · "License number can only contain letters, numbers, hyphens and spaces" · "License number already exists" |

---

## 3. Dialog / form

- Module dialog in Location's style (dark title bar with Close): "Add Driver" / "Edit Driver — <name>" (truncates); `maxWidth="xs"`, `fullScreen` on xs. Only the content scrolls; the title bar and the Cancel / Save buttons always stay visible.
- Form fields:
  1. Profile picture row (avatar, hint, Upload / Change / Remove); a refused file shows the photo error under the row.
  2. Driver Name: full width, char limit 100; helper shows the counter "n/100".
  3. License Number: full width, char limit 50, typed in capitals; helper "Letters, numbers, spaces and hyphens · n/50", "Checking availability..." while checking.
- Buttons: **Cancel** · **Add Driver** / **Save changes**; Enter submits; spinner while saving; backdrop disabled while saving; "Discard changes?" confirmation on unsaved changes.
- Edit loads behind `LoadingOverlay`; "Driver not found" → toast, list refresh, dialog does not open.

---

## 4. Feedback states

| State | Behaviour (exact text) |
|---|---|
| First load | Skeleton rows |
| Refresh | `LinearProgress` on the card |
| Empty | "No drivers yet. Add your first driver for deliveries and stock transfers." + Add Driver button |
| No match | "No drivers match your filters." + Clear filters |
| Load error | "We couldn't load your drivers. Check your connection and try again." + **Retry**; toast "Failed to load drivers" |
| Save | "Driver created successfully" / "Driver updated successfully"; failure: API message |
| Status | "Driver deactivated" / "Driver activated"; failure: API message, switch returns |

---

## 5. Confirmations

- Deactivate: title "Deactivate driver"; "Deactivate **<driverName>**? Staff won't be able to assign this driver to delivery or stock transfers. Past transfers keep this driver. You can activate them again at any time." — Cancel · **Deactivate**.
- Activate: title "Activate driver"; "Activate **<driverName>**?" — Cancel · **Activate**.

---

## 6. Permissions

No `create_driver_master` → Add button hidden. No `edit_driver_master` → Edit icon and switch hidden. No delete anywhere. The API enforces the same permissions.

---

## 7. Responsive

| | xs | sm | md+ |
|---|---|---|---|
| Header | Add full width | Inline | Inline |
| Filters | Stacked | One row | One row |
| List | Card list | Table | Table |
| Dialog | Full screen | xs | xs |

No horizontal page scroll; table height from `ScrollableTableContainer`.
