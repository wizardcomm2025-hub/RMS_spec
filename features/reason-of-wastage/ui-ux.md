# UI / UX — Reason of Wastage

> Follows `RMS_API/specs/UI_UX_STANDARDS.md` and the §12 list recipe (reference: Setting → Location; closest sibling: Setting → Department). Only what differs is spelled out. Text the user sees is written here **verbatim**.

Route: `RMS_NextJS/src/app/(admin-dashboard)/admin/(setting)/wastage-reason/` (rewrite `/setting/reason_of_wastage`, unchanged) · View: `src/pages-sections/vendor-dashboard/reason-of-wastage/`.

## 1. Page anatomy

- `PageHeader`: breadcrumb "Setting › Reason of Wastage", title "Reason of Wastage", ⓘ About, ⟳ Refresh, **＋ Add Reason** (`create_reason_of_wastage`).
- About (Q5): *"These are the reasons staff pick when they write off stock in Wastage. Switched-off reasons can't be picked, and past wastage keeps its reason. The Wastage Report totals by reason."* Links: "Wastage" → `/stock/wastage`, "Wastage Report" → `/report/wastage_report` (both keys appear in the text). Replaces the old text that promised photo / approval rules (D9).
- Filter bar: Search ("Search reason...") · Status ▾ (All / Active / Inactive) · "Clear filters".
- Card list; footer "Showing X of Y reasons"; **no pagination** (D8).
- Browser tab "Reason of Wastage - RMS" (unchanged).

## 2. Columns / fields

| Column | Priority | Content |
|---|:-:|---|
| Reason | P1 | Text, weight 600, wraps (`wordBreak: break-word`) |
| Status | P1 | "Active" (`success`) / "Inactive" (`default`) chip |
| Actions | P1 | Edit icon (`edit_reason_of_wastage`) · Active switch (`edit_reason_of_wastage`, tooltip "Deactivate" / "Activate") |

Phone (xs): card — text, status chip, Edit and switch at the bottom.

| Field | Required | Rule / hint | Error text |
|---|:-:|---|---|
| Reason | ✔ | Text box that wraps (grows to 4 rows) but holds one line — line breaks become spaces; **no max, no counter**; live duplicate check; placeholder "e.g. Expired" | "Reason is required" · "Reason can only contain letters, numbers, spaces and & - ( ) ' / . ," · "Reason already exists" |

## 3. Dialog / form

- One module dialog `reason-modal.tsx`, used by this page **and** Wastage Entry. Department's style (dark `grey.900` title bar with Close): "Add Reason" / "Edit Reason — <text>" (title truncates with ellipsis); `maxWidth="xs"`, `fullScreen` on xs.
- No Active toggle (add is always Active; status lives on the list switch).
- Buttons: **Cancel** · **Add Reason** / **Save changes**; Enter submits; spinner while saving; X / Esc / backdrop disabled while saving; "Discard changes?" when closing a changed form.
- Edit loads behind `LoadingOverlay`; "Reason not found" → toast, list refresh, no dialog.
- `SingleFieldModal` is no longer used for reasons (left unchanged for its other screens).

## 4. Feedback states

| State | Behaviour (exact text) |
|---|---|
| First load | Skeleton rows |
| Refresh | `LinearProgress` on the card |
| Empty | "No reasons yet. Add the reasons staff pick when they write off stock." + Add button |
| No match | "No reasons match your filters." + Clear filters |
| Load error | "We couldn't load your reasons. Check your connection and try again." + **Retry**; toast "Failed to load reasons" |
| Save | "Reason added" / "Reason updated"; failure: API message (fallback "Failed to save reason") |
| Status | "Reason deactivated" / "Reason activated"; failure: API message, switch returns |

## 5. Confirmations

- Deactivate: title "Deactivate reason"; "Deactivate **<text>**? Staff won't be able to pick it in Wastage. Past wastage keeps this reason. You can activate it again at any time." — Cancel · **Deactivate**.
- Activate: title "Activate reason"; "Activate **<text>**?" — Cancel · **Activate**.
- Module-local confirm component (the shared `ConfirmDialog` ends with "?" and uses delete styling — as Department).

## 6. Permissions

No `create_reason_of_wastage` → Add hidden (page) and "＋" hidden (Wastage Entry). No `edit_reason_of_wastage` → Edit icon and switch hidden. No delete anywhere. The API enforces the same.

## 7. Responsive

| | xs | sm | md+ |
|---|---|---|---|
| Header | Add full width | Inline | Inline |
| Filters | Stacked | One row | One row |
| List | Card list | Table | Table |
| Dialog | Full screen | xs | xs |

No horizontal page scroll; table height from `ScrollableTableContainer`.

## 8. Accessibility

Switch `aria-label="Active status for <text>"`; icon buttons with tooltips; Wastage Entry "＋" `aria-label="Add reason"` with tooltip "Add reason"; keyboard order follows the layout.

## 9. Shared components

Reused: `PageHeader`, `ListSearch`, `ModalActionButton`, `LoadingOverlay`, `LinearProgress`, `BazaarSwitch`, `CustomTooltip`, `ConfirmDialog` (discard prompt), `ScrollableTableContainer`, `useNameValidation`. Module-local: row, card, status chip + switch, status confirm, dialog. Removed: `request-row.tsx` (→ `reason-row.tsx`), `types.ts`.

## 10. Template impact

None.

## 11. Wastage Entry

Only the reason parts of `pages-sections/vendor-dashboard/wastage-entry/` change:

- Reasons load once with `OpenMode: "Active"` and after a quick-add; the item-search effect no longer reloads them (D1, D2).
- The row's "＋" opens `reason-modal.tsx` (add mode). It shows only when the user has `create_reason_of_wastage` and the existing Wastage save access.
- On success: toast "Reason added", reasons reload, the new reason is selected in that row. On failure: the API message; the dialog stays open (D3).
- A row whose selected reason is no longer active (page open across a switch-off) keeps its value until the user changes it; saving is decided by the Wastage API (out of scope).

## UI acceptance criteria

1. At 375, 768, 1280, 1920 px: no horizontal page scroll; xs card list and full-screen dialog; a 300-character reason wraps.
2. Empty, no-match, load-error (with Retry) and refresh states as in §4.
3. No hex colours; theme tokens only.
4. Search → add → save → toggle works with the keyboard.
5. The dialog can't be closed while saving; Wastage Entry quick-add selects the new reason in its row.
