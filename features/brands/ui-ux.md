# UI / UX — Brand and Sub Brand

> Follows `UI_UX_STANDARDS.md` §12 (sibling: Setting → Department). Badges centred, Actions right (§7). Texts verbatim.

Routes: `admin/(inventory)/brand/`, `admin/(inventory)/sub-brand/` · Views: `pages-sections/vendor-dashboard/brands/`, `sub-brands/`.

## 1. Page anatomy

**Brand:** breadcrumb "Inventory › Brand", title "Brand", About (kept `BRAND_INFO`, links "Raw Material", "Sub-Brand"), ⟳, **＋ Add Brand** (`create_brand`). Filters: Search ("Search brand...") · Status · Clear filters. Footer "Showing X of Y brands".

**Sub Brand:** breadcrumb "Inventory › Sub Brand", title "Sub Brand", About (kept `SUB_BRAND_INFO`, links "Brand", "Raw Material"), ⟳, **＋ Add Sub Brand** (`create_sub_brand`). Filters: Search ("Search sub-brand or brand...") · Brand ▾ (all brands) · Status · Clear filters. Footer "Showing X of Y sub-brands".

No description line; no pagination; tabs "Brand - RMS", "Sub Brand - RMS".

## 2. Columns

| Brand page | Sub Brand page |
|---|---|
| Brand Name (600) | Sub Brand Name (600) |
| Sub Brands — chips of active sub-brands, wrap, left | Brand — name; "(inactive)" in secondary text when off |
| Raw Materials — count of active raw materials, centred | Raw Materials — centred |
| Status — chip, centred | Status — chip, centred |
| Actions — Edit · switch, right | Actions — Edit · switch, right |

Phone: cards with the same fields; actions bottom right.

## 3. Dialogs

Department style. **Brand:** "Add Brand" / "Edit Brand — <name>"; field Brand Name (max 100, counter, live check). **Sub Brand:** "Add Sub Brand" / "Edit Sub Brand — <name>"; fields **Brand** (`SearchableSelect`, active brands; on edit the current brand is shown even if inactive) and Sub Brand Name (max 100, counter, live check within the brand). Buttons Cancel · Add … / Save changes; "Discard changes?". `SingleFieldModal` no longer used here.

## 4. Feedback states

Skeleton; refresh bar; empty / no-match / load-error with exact texts from use cases 01 and 04; toasts per use cases 02, 03, 05, 06 (fallbacks "Failed to save brand", "Failed to save sub-brand").

## 5. Confirmations

Texts in use cases 03 and 06 ("Deactivate brand", "Deactivate sub-brand", "Activate …?").

## 6–10

Permissions per use cases; responsive and accessibility as Department; reused shared components as Department plus `SearchableSelect`; removed `brand-form.tsx`, `brand-edit.tsx` (dead); template impact none.

## UI acceptance criteria

1. 375–1920 px: no horizontal scroll; phone cards; full-screen dialogs on xs.
2. All feedback states; theme tokens only.
3. Sub Brand page opens with **one** list request (no per-item requests).
