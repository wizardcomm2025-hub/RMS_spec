# 02 — Add a category

## Summary

A company user with `create_item_category` adds a main category or a sub-category. They give it a name, a short code (suggested from the name), an inventory type and, for a sub-category, a parent main category of the same type. The system validates the input, keeps names and codes unique within the company and type, and saves the category as Active.

## Prerequisites

- **Backend.** `AddEditStockGroup` with server validation, `Status=false` on failure and the company filter (see [`api-contract.md`](./api-contract.md#save-category)). `ValidateStockGroupName` per company + type. `GetStockGroupByInventoryType` for the parent list.
- **Data.** `SP_AddEditStockGroup` and `SP_ValidateStockGroupName` changes (see [`data-model.md`](./data-model.md#stored-procedures)).
- **Depends on.** [01](./01-view-and-filter-categories.md) for the list it returns to. [03](./03-edit-category.md) shares the save contract; build together.

## Actors & preconditions

- **Actor:** a company user with `create_item_category`.
- **Trigger:** "＋ Add Category" in the header (or the empty state).
- **Preconditions:** the company has at least one active inventory type.

## Main flow

1. The user clicks "＋ Add Category"; the dialog "Add Category" opens. Inventory Type defaults to the type selected in the filter, else the first type.
2. The user enters Category Name; the system checks for duplicates within the company and the selected type as they type.
3. While the user hasn't typed a code, Category Code is suggested from the name (see CAT-Q3).
4. The user optionally picks a Parent Category — only active main categories of the selected type are offered. "None" makes it a main category.
5. Changing the Inventory Type clears the chosen parent and re-checks the name.
6. The user clicks "Add Category"; the button shows a spinner and the dialog cannot be closed.
7. The system trims name and code, validates everything, and saves the category for the token's company with `ClassificationId = 1`, `IsActive = 1`, `IsDeleted = 0`, `CreatedBy` = caller.
8. The dialog closes, the toast "Category created" appears and the list refreshes; a new sub-category's parent is expanded.

## Alternative flows

- **Name empty or spaces only.** "Category name is required".
- **Name over 100 characters.** The UI stops input at 100; the API rejects longer names: "Category name must be 100 characters or less".
- **Name with other characters.** "Category name can only contain letters, numbers, spaces and & - ( ) ' / . ,".
- **Duplicate name.** Same trimmed name (case-insensitive) as another non-deleted category of the same company and type, active or inactive: "Category name already exists" inline and as a toast; nothing saved.
- **Same name in another type or company.** Allowed.
- **Code empty / bad format / duplicate.** "Category code is required" / "Category code must be 1–10 letters or digits" / "Category code already exists". The code is stored in upper case.
- **Type missing or not the company's.** "Inventory type is required".
- **Invalid parent.** The parent is not an active main category of the same type and company (another type, a sub-category, inactive, another company's, unknown): "Select a valid parent category".
- **No permission (direct call).** "You don't have permission to perform this action."
- **Double click.** One category is created.
- **Discard.** Closing a changed form asks "Discard changes?".
- **Server failure.** Toast "Something went wrong. Please try again." (or "Failed to save category" when unreachable); the dialog stays open with the input kept.

## Acceptance criteria

1. Given valid input, when the user adds a main category, then "Category created" is shown and it appears in the list as Active with its code and type.
2. Given a parent is chosen, then the new category appears under that parent, and the parent shows "N sub" increased by one.
3. Given " vegetables " while "Vegetables" exists in the same company and type (active or inactive), then "Category name already exists" and nothing is saved.
4. Given "Vegetables" exists under Raw Material, when "Vegetables" is added under Finished Goods, then it is saved.
5. Given a code already used in the same company and type, then "Category code already exists"; given "veg-1", then "Category code must be 1–10 letters or digits"; given "veg1", then it is stored as "VEG1".
6. Given a 101-character name, then the UI does not accept it and the API rejects it.
7. Given a parent of another type, a sub-category, an inactive or another company's category, then "Select a valid parent category" and nothing is saved.
8. Given the caller sends `CompanyId`, `CreatedBy`, `ClasificationId = 2` or `IsActive = false`, then they are ignored: the category belongs to the token's company and caller, is an item category and is Active.
9. Given a user without `create_item_category`, then the Add button is hidden and a direct call returns "You don't have permission to perform this action."
10. Given the name "Fresh Vegetables" and no code typed, then the code field suggests "FREVEG".

## Platform notes

### RMS API

- `POST /Setting/AddEditStockGroup` with an empty `StockGroupId`, `[CompanyAuthorize]`, `create_item_category` checked on the server. `CompanyId` and `UserId` from the token; `ClassificationId` forced to 1. Messages: [`api-contract.md`](./api-contract.md#messages).
- `POST /Users/ValidateStockGroupName` gets `InventoryTypeId` and checks per company + type.
- `POST /Setting/GetStockGroupByInventoryType` for the parent list (active main categories of the type).

### Data — Tenant DB

- Inserts `StockGroup`. All checks run in the procedure under a per-company lock.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Dialog layout, hints and states: [`ui-ux.md`](./ui-ux.md#3-dialog--form).

## Configuration

- Inventory types per company. Name / code limits are fixed rules (Q2).

## Out of scope

- Images and the intangible flag.

## Open questions

None open. CAT-Q3 resolved 2026-10-28.
