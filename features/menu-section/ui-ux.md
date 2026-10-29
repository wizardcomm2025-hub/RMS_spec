# UI / UX — Menu Section

> Follows `RMS_API/specs/UI_UX_STANDARDS.md` and the §12 list recipe (closest sibling: Setting → Department). Text the user sees is written here **verbatim**.

Route: `RMS_NextJS/src/app/(admin-dashboard)/admin/(inventory)/menu-section/` (link `/inventory/menu_section`) · View: `src/pages-sections/vendor-dashboard/menu-section/`.

## 1. Page anatomy

- `PageHeader`: breadcrumb "Inventory › Menu Section", title "Menu Section", ⓘ About, ⟳ Refresh, **＋ Add Menu Section** (`create_menu_section`). No separate description line.
- About (kept, Q5): *"A Menu Section is a group on the POS menu that cashiers and guests see (Starters, Main Course, Beverages). It is separate from inventory: a finished item keeps its category from Category Master for stock & costing and is mapped to a Menu Section for selling. Finished Goods pick their section in Recipe, so create sections before you add sellable recipes."* Links: "Category Master" → `/setting/item_category`, "Recipe" → `/inventory/recipe`.
- Filter bar: Search ("Search menu section...") · Status ▾ (All / Active / Inactive) · "Clear filters".
- Card list; footer "Showing X of Y menu sections"; **no pagination**.
- Browser tab "Menu Section - RMS".

## 2. Columns / fields

Alignment (UI_UX_STANDARDS §7, strict): badge columns are centred; Actions is the last column and right-aligned.

| Column | Content |
|---|---|
| Menu Section Name | Name, weight 600 |
| Status | "Active" (`success`) / "Inactive" (`default`) chip — centred |
| Actions | Edit (`edit_menu_section`) · Active switch (`edit_menu_section`, tooltip "Deactivate" / "Activate") — right |

Phone (xs): card — name, status chip, Edit and switch at the bottom right.

| Field | Required | Rule | Error text |
|---|:-:|---|---|
| Menu Section Name | ✔ | Max 100, counter, live duplicate check | "Menu section name is required" · "Menu section name can only contain letters, numbers, spaces and & - ( ) ' / . ," · "Menu section name already exists" |

## 3. Dialog / form

- Module dialog like Department (dark title bar, Close): "Add Menu Section" / "Edit Menu Section — <name>"; `maxWidth="xs"`, full screen on xs; buttons Cancel · **Add Menu Section** / **Save changes**; spinner; can't close while saving; "Discard changes?".
- No Active toggle in the dialog. `SingleFieldModal` is no longer used here.

## 4. Feedback states

| State | Text |
|---|---|
| First load | Skeleton rows |
| Refresh | `LinearProgress` on the card |
| Empty | "No menu sections yet. Add the groups cashiers see on the POS menu." + Add |
| No match | "No menu sections match your filters." + Clear filters |
| Load error | "We couldn't load your menu sections. Check your connection and try again." + **Retry**; toast "Failed to load menu sections" |
| Save | "Menu section created" / "Menu section updated"; failure: API message (fallback "Failed to save menu section") |
| Status | "Menu section deactivated" / "Menu section activated"; failure: API message, switch returns |

## 5. Confirmations

- Deactivate: "Deactivate menu section" — "Deactivate **<name>**? It can't be picked for recipes any more. You can activate it again at any time." — Cancel · **Deactivate**.
- Activate: "Activate menu section" — "Activate **<name>**?" — Cancel · **Activate**.

## 6. Permissions

No `create_menu_section` → Add hidden. No `edit_menu_section` → Edit and switch hidden. No delete anywhere.

## 7. Responsive

As Department: xs card list and full-screen dialog; table from sm; no horizontal page scroll.

## 8. Accessibility

Switch `aria-label="Active status for <name>"`; tooltips on icons; Enter submits.

## 9. Shared components

Reused as Department. Module-local: row, card, status chip + switch, status confirm, dialog. Removed: `customer-row.tsx` (template leftover).

## 10. Template impact

None.

## 11. Recipe dialog

`recipe/recipe-modal.tsx` only: the Menu Section field is bound by **id**. When editing a recipe whose section isn't in the active list, the field shows "<name> (inactive)" selected and keeps it on save; the list offers active sections for a new choice (D7, Q4).

## UI acceptance criteria

1. 375 / 768 / 1280 / 1920 px: no horizontal scroll; phone cards; full-screen dialog on xs.
2. Empty, no-match, load-error (Retry) and refresh states as §4.
3. Theme tokens only; Status centred, Actions right.
4. Tab title "Menu Section - RMS"; the dialog can't be closed while saving.
