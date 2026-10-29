# 03 — Edit a category

## Summary

A company user with `edit_item_category` opens an existing category (active or inactive), changes its name, code, type or parent and saves. The same rules as Add apply, plus the two-level structure is protected: a main category with sub-categories cannot become a sub-category, and a category's type cannot change while it has sub-categories or items. The server only ever edits the caller's own company's categories, keeps fields the form doesn't show, and refuses a save if someone else changed the category in the meantime.

## Prerequisites

- **Backend.** `GetStockGroupById` with the company filter, `IsActive`, `UpdatedAt` and the inventory type name; `AddEditStockGroup` as in [02](./02-add-category.md) (see [`api-contract.md`](./api-contract.md)).
- **Data.** `SP_GetStockGroupById`, `SP_AddEditStockGroup` changes.
- **Depends on.** [02](./02-add-category.md) — same save contract.

## Actors & preconditions

- **Actor:** a company user with `edit_item_category`.
- **Trigger:** the Edit icon on a row or phone card.
- **Preconditions:** the category belongs to the user's company, is an item category and is not deleted.

## Main flow

1. The user clicks Edit; the dialog "Edit Category — <name>" opens behind a loading overlay and shows the stored values.
2. The user changes fields; the name is checked for duplicates as they type, excluding this category.
3. The user clicks "Save changes".
4. The system checks the category hasn't changed since it was loaded (`UpdatedAt`), validates as in [02](./02-add-category.md) and the structure rules below, and updates it: `UpdatedBy` = caller, `UpdatedAt` = now. `CompanyId`, `ClassificationId`, `ImagePath`, `IsIntangible`, `IsActive`, `CreatedBy` don't change.
5. The dialog closes, "Category updated" appears and the list refreshes.

## Alternative flows

- **Duplicate name / code.** As in [02](./02-add-category.md), excluding this category; nothing changes.
- **Rename to the same name.** Only case or spaces change: allowed.
- **Main → sub while it has sub-categories.** "A category with sub-categories can't be made a sub-category"; nothing changes.
- **Parent = itself, another type, a sub-category, inactive, another company's.** "Select a valid parent category".
- **Type change with sub-categories or items.** The category has sub-categories (any state) or items (active or inactive) using it: "Inventory type can't be changed while the category has sub-categories or items" (CAT-Q1).
- **Inactive category.** Opens and saves like an active one.
- **Not found.** Unknown, deleted, asset or another company's id, on load or on save: "Category not found"; nothing shown or changed. The page refreshes and does not open the Add dialog.
- **Changed by someone else.** Saved or switched by another user since it was loaded, or no `UpdatedAt` sent: "This category was changed by someone else. Reload and try again."; the dialog stays open; nothing changes.
- **No permission (direct call).** "You don't have permission to perform this action."
- **Discard / double click / server failure.** As in [02](./02-add-category.md).

## Acceptance criteria

1. Given category A, when the user edits and saves, then "Category updated" is shown and the list shows the new values.
2. Given A's name typed into B (same company and type), then "Category name already exists" live and on save, and nothing changes.
3. Given a main category with sub-categories, when a parent is chosen for it, then "A category with sub-categories can't be made a sub-category" and nothing changes.
4. Given a category with sub-categories or items, when its type is changed, then "Inventory type can't be changed while the category has sub-categories or items".
5. Given a category with an image and the intangible flag, when it is saved from this screen, then `ImagePath` and `IsIntangible` are unchanged (D7).
6. Given a CO-B or asset category id with a CO-A token, on load or save, then "Category not found" and nothing about it is returned or changed (D1, D2).
7. Given two users edit the same category, when the second saves, then "This category was changed by someone else. Reload and try again." and the first user's values remain.
8. Given a user without `edit_item_category`, then the Edit icon is hidden and a direct save returns the permission message.
9. Given an inactive category, then it opens and saves.

## Platform notes

### RMS API

- `POST /Setting/GetStockGroupById` — `[CompanyAuthorize]`; company and `ClassificationId = 1` filters; not found → "Category not found"; returns `IsActive`, `UpdatedAt`, `InventoryTypeName`.
- `POST /Setting/AddEditStockGroup` with the category's id and `UpdatedAt`; `edit_item_category` checked on the server.

### Data — Tenant DB

- `SP_AddEditStockGroup` edit: `WHERE StockGroupId = @id AND CompanyId = @CompanyId AND ClassificationId = 1 AND IsDeleted = 0`; never changes `CompanyId`, `ClassificationId`, `ImagePath`, `IsIntangible`, `IsActive`, `IsDeleted`, `CreatedBy`.
- Items reference categories through `StockItem.StockGroupId` and `StockItem.SubStockGroupId`.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Same dialog as Add, in edit mode: [`ui-ux.md`](./ui-ux.md#3-dialog--form).

## Configuration

None beyond [02](./02-add-category.md).

## Out of scope

- Moving items between categories.

## Open questions

None open. CAT-Q1 resolved 2026-10-28.
