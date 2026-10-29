# UI / UX — Department

> Follows `RMS_API/specs/UI_UX_STANDARDS.md` and the §12 list recipe (reference: Setting → Location). Only what differs is spelled out. Text the user sees is written here **verbatim**.

Route: `RMS_NextJS/src/app/(admin-dashboard)/admin/(setting)/department/` · View: `src/pages-sections/vendor-dashboard/department/`.

## 1. Page anatomy

- `PageHeader`: breadcrumb "Setting › Department", title "Department", ⓘ About, ⟳ Refresh, **＋ Add Department** (`create_department`).
- Description: none (omitted to avoid duplicating the About panel).
- About: *"Departments group your staff for rostering and reporting. Needed before you can add a User, and a Roster Pattern is assigned to a department."* (unchanged). Links: "User" → User List, "Roster Pattern" → Roster. (Today's link keys "User List" / "Roster" — "User List" never appears in the text, so that link never showed.)
- Filter bar: Search ("Search department...") · Status ▾ (All / Active / Inactive) · "Clear filters".
- Card list; footer "Showing X of Y departments"; **no pagination** (full list, like Category Master).
- Browser tab "Department - RMS".

## 2. Columns / fields

Alignment (UI_UX_STANDARDS §7, strict): badge columns (status, type, requisition) are centred; Actions is the last column and right-aligned.

| Column | Priority | Content |
|---|:-:|---|
| Department Name | P1 | Name, weight 600 |
| Status | P1 | "Active" (`success`) / "Inactive" (`default`) chip |
| Actions | P1 | Edit icon (`edit_department`) · Active switch (`edit_department`, tooltip "Deactivate" / "Activate") |

Phone (xs): card — name, status chip, Edit and switch at the bottom.

| Field | Required | Rule / hint | Error text |
|---|:-:|---|---|
| Department Name | ✔ | Max 100, counter, live duplicate check | "Department name is required" · "Department name can only contain letters, numbers, spaces and & - ( ) ' / . ," · "Department name already exists" |

## 3. Dialog / form

- Module dialog in Location's style (dark title bar with Close): "Add Department" / "Edit Department — <name>"; `maxWidth="xs"`, `fullScreen` on xs.
- One field with a counter "0/100". No Active toggle in the dialog (status lives on the list switch).
- Buttons: **Cancel** · **Add Department** / **Save changes**; Enter submits; spinner while saving; X / Esc / backdrop disabled while saving; "Discard changes?" when closing a changed form.
- Edit loads behind `LoadingOverlay`; "Department not found" → toast, list refresh, no dialog.
- The shared `SingleFieldModal` is no longer used here (left unchanged for its other screens).

## 4. Feedback states

| State | Behaviour (exact text) |
|---|---|
| First load | Skeleton rows |
| Refresh | `LinearProgress` on the card |
| Empty | "No departments yet. Add your first department to group your staff." + Add button |
| No match | "No departments match your filters." + Clear filters |
| Load error | "We couldn't load your departments. Check your connection and try again." + **Retry**; toast "Failed to load departments" |
| Save | "Department created" / "Department updated"; failure: API message (fallback "Failed to save department") |
| Status | "Department deactivated" / "Department activated"; failure: API message, switch returns |

## 5. Confirmations

- Deactivate: title "Deactivate department"; "Deactivate **<name>**? It will be hidden from user, roster and other department lists. You can activate it again at any time." — Cancel · **Deactivate**.
- Activate: title "Activate department"; "Activate **<name>**?" — Cancel · **Activate**.

## 6. Permissions

No `create_department` → Add hidden. No `edit_department` → Edit icon and switch hidden. No delete anywhere. The API enforces the same.

## 7. Responsive

| | xs | sm | md+ |
|---|---|---|---|
| Header | Add full width | Inline | Inline |
| Filters | Stacked | One row | One row |
| List | Card list | Table | Table |
| Dialog | Full screen | xs | xs |

No horizontal page scroll; table height from `ScrollableTableContainer`.

## 8. Accessibility

Switch `aria-label="Active status for <name>"`; icon buttons with tooltips; keyboard order follows the layout; Enter submits.

## 9. Shared components

Reused: `PageHeader`, `ListSearch`, `ModalActionButton`, `LoadingOverlay`, `LinearProgress`, `BazaarSwitch`, `CustomTooltip`, `ConfirmDialog` (discard prompt), `ScrollableTableContainer`. Module-local: row, card, status chip + switch, status confirm, dialog. Dead files removed: `payment-row.tsx`, `types.ts`.

## 10. Template impact

None.

## UI acceptance criteria

1. At 375, 768, 1280, 1920 px: no horizontal page scroll; xs card list and full-screen dialog.
2. Empty, no-match, load-error (with Retry) and refresh states as in §4.
3. No hex colours; theme tokens only.
4. Search → add → save → toggle works with the keyboard.
5. Tab title "Department - RMS"; the dialog can't be closed while saving.
