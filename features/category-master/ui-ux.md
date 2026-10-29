# UI / UX — Category Master

> Follows `RMS_API/specs/UI_UX_STANDARDS.md` and the list-page recipe §12 (reference: Setting → Location). Only what differs from Location is spelled out; everything else copies it. Text the user sees is written here **verbatim**.

Route: `RMS_NextJS/src/app/(admin-dashboard)/admin/(setting)/stock-group/` (unchanged) · View: `src/pages-sections/vendor-dashboard/stock-group/`.

## 1. Page anatomy

- `PageHeader`: breadcrumb "Setting › Category Master", title "Category Master", ⓘ About, ⟳ Refresh, **＋ Add Category** (needs `create_item_category`).
- Description: none (omitted to avoid duplicating the About panel).
- About: *"Each inventory type keeps its own categories, in two levels. A sub-category sits under a main category of the same type. Items, recipes, stock checks and food-cost reports use these categories. See Raw Material and Recipe."* (links: Raw Material, Recipe). The Finished Goods note (*"Finished Goods categories are for stock & food-cost reporting only and are not shown on the POS."*) stays as an info line shown when the Category Type filter is Finished Goods.
- Filter bar: Search ("Search category or code...") · Category Type ▾ (All + company types) · Status ▾ (All / Active / Inactive) · "Clear filters" (only when a filter is set). The old type tabs are replaced by the Category Type filter.
- List inside a Card; footer "Showing X of Y categories". **No pagination** — the API returns the whole list (no server-side paging), so this page differs from the §12 recipe here (decided 2026-10-28).
- Browser tab title "Category Master - RMS".
- The Menu Section page's About text mentions "Category Master" and links it to `/setting/item_category` (the friendly URL rewritten to `/admin/stock-group`) (D12).

## 2. Columns / fields

**List**

| Column | Priority | Content |
|---|:-:|---|
| Category Name | P1 | Main: borderless chevron (rotates 90° when expanded, `info.main` when open / on hover) + bold name + "N sub" chip. Sub: a thin grey └ connector starting under the parent's chevron, name at normal weight on a `grey.50` row (updated 2026-10-28 after review: the bordered circle button and blue ↳ icon looked heavy) |
| Code | P1 | Category code, monospace-like weight 600, "—" if empty (legacy) |
| Parent | P2 | Parent name or "—" |
| Category Type | P1 | Chip, theme colours only: `info` for raw types, `warning` for semi-finished, `success` for finished, `default` otherwise (by `SystemName` when available, else by name) |
| Status | P1 | "Active" (`success`) / "Inactive" (`default`) chip |
| Actions | P1 | Edit icon (`edit_item_category`) · Active switch (`edit_item_category`, tooltip "Deactivate" / "Activate") |

Phone (xs): card list — name (with "N sub" / ↳ marker), Code, Type and Status chips, Parent line for sub-categories, Edit and switch at the bottom; expand works the same.

**Form fields**

| Field | Required | Rule / hint | Error text |
|---|:-:|---|---|
| Category Name | ✔ | Max 100, counter, live duplicate check within the type | "Category name is required" · "Category name can only contain letters, numbers, spaces and & - ( ) ' / . ," · "Category name already exists" |
| Category Code | ✔ | Max 10. Only letters and digits can be typed (other characters are dropped) and letters are upper-cased as typed; suggested from the name until the user types one. Hint: *"Max 10 letters or digits"* (the code is shown on stock and food-cost reports) | "Category code is required" · "Category code must be 1–10 letters or digits" · "Category code already exists" |
| Category Type | ✔ | Company's types | "Category type is required" |
| Parent Category | — | **Searchable dropdown** (`components/searchable-select`): type to filter the active main categories of the type (not itself); clear (×) for none, placeholder "None (main category)". Hint: *"Leave empty for a main category. Only main categories of this type are listed."* Disabled with the hint *"This category has sub-categories, so it stays a main category."* when editing a main category that has sub-categories | "Select a valid parent category" |

## 3. Dialog / form

- Title "Add Category" / "Edit Category — <name>". `fullScreen` on xs; header and buttons fixed, content scrolls.
- Layout: Category Name (full width, counter under it); Category Code and Category Type side by side on sm+; Parent Category (full width). Every hint fits on one line at sm and up (fixed 2026-10-28 after review: the code hint wrapped to four lines in a third-width column).
- Buttons: **Cancel** · **Add Category** / **Save changes**. Enter submits.
- While submitting: spinner; X, Esc and backdrop disabled. Closing a changed form: "Discard changes?" — "You have unsaved changes. Discard them" — Cancel · Discard.
- Edit loads behind `LoadingOverlay`. On "Category not found" the dialog does not open; the list refreshes.
- Duplicate name / code: inline on the field plus toast. Other refusals: toast with the API message; the dialog stays open.
- The dialog uses the same style as Location's dialog (the reference): dark title bar with a Close button.

## 4. Feedback states

| State | Behaviour (exact text) |
|---|---|
| First load | Skeleton rows |
| Refresh / after save | `LinearProgress` on the card; rows stay |
| Empty | Icon + "No categories yet. Add your first category to organise your items." + Add button |
| No match | "No categories match your filters." + Clear filters |
| Load error | Inline "We couldn't load your categories. Check your connection and try again." with **Retry** + toast "Failed to load categories" |
| Types fail | Toast "Failed to load category types" |
| Save success | Toast "Category created" / "Category updated" |
| Save failure | API message as toast (fallback "Failed to save category") |
| Status change | Toast "Category deactivated" / "Category activated"; on failure the API message and the switch returns |

## 5. Confirmations

- Deactivate: title "Deactivate category"; text "Deactivate **<name>**? It will be hidden from item, recipe and stock screens. You can activate it again at any time." — Cancel · **Deactivate**.
- Activate: title "Activate category"; text "Activate **<name>**?" — Cancel · **Activate**.
- Reuse Location's module-local confirm pattern (a shared `ConfirmDialog` can't show the second sentence).

## 6. Permissions

- No `create_item_category` → Add hidden (header and empty state).
- No `edit_item_category` → Edit icon and switch hidden.
- No delete anywhere. The API enforces the same permissions.

## 7. Responsive

| | xs | sm | md+ |
|---|---|---|---|
| Header | Add full width under the title; Refresh as icon | Inline | Inline |
| Filters | Stacked | 2 per row | One row |
| List | Card list | Table without Parent | Full table |
| Dialog | Full screen | sm | sm |

No fixed `minWidth`; no horizontal page scroll at any width.

## 8. Accessibility

- Expand control: `aria-label` "Expand <name>" / "Collapse <name>", `aria-expanded`.
- Switch: `aria-label="Active status for <name>"`. Icon buttons have tooltips.
- Keyboard: Tab order follows the visual order; Enter submits; Space toggles.

## 9. Shared components

Reused: `PageHeader`, `ListSearch`, `ModalActionButton`, `LoadingOverlay`, `LinearProgress`, `BazaarSwitch`, `CustomTooltip`. **New shared component** `components/searchable-select` (`SearchableSelect`) — added for the Parent field and meant for reuse (listed in `UI_UX_STANDARDS.md` §4); no existing component changed. Module-local: category row / card, status chip + switch, status confirm. Dead files removed: `package-card.tsx`, `packages.ts`, unused `types.ts` (template leftovers).

## 10. Template impact

None. No theme or shared-component changes. Navigation label change in `dashboard-navigation.ts` (static fallback) only.

## UI acceptance criteria

1. Every visible "Item Category" / "Stock group" text reads "Category Master" / "Category"; the sidebar shows "Category Master" (after the rename script).
2. At 375, 768, 1280 and 1920 px there is no horizontal page scroll; xs shows the card list and a full-screen dialog.
3. Empty, no-match, load-error (with Retry) and refresh states match §4.
4. No hex colours in the module's files; only theme tokens.
5. Search → add → save → toggle works with the keyboard only.
6. Browser tab title "Category Master - RMS"; the dialog can't be closed while saving.
