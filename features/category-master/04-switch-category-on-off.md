# 04 — Switch a category on or off

## Summary

Instead of deleting a category, a user with `edit_item_category` switches it Inactive. An inactive category disappears from item, recipe and stock screens (they already list only active categories) but stays here and can be switched back on. To keep the data consistent, a category can't be switched off while active sub-categories or active items use it, and a sub-category can't be switched on while its parent is inactive.

## Prerequisites

- **Backend.** New `POST /Setting/ChangeStockGroupStatus` (see [`api-contract.md`](./api-contract.md#change-status)); `DelStockGroupById` blocked.
- **Data.** New `SP_ChangeStockGroupStatus` (see [`data-model.md`](./data-model.md#stored-procedures)).
- **Depends on.** [01](./01-view-and-filter-categories.md) — the list must show inactive rows.

## Actors & preconditions

- **Actor:** a company user with `edit_item_category`.
- **Trigger:** the Active switch on a row or phone card.
- **Preconditions:** the category belongs to the user's company, is an item category and is not deleted.

## Main flow

1. The user turns the switch off on an active category.
2. A confirmation opens: "Deactivate **<name>**? It will be hidden from item, recipe and stock screens. You can activate it again at any time." with Cancel and Deactivate.
3. The user clicks Deactivate.
4. The system checks there are no active sub-categories and no active items in it, sets `IsActive = 0` (keeps `IsDeleted = 0`) and records `UpdatedBy` / `UpdatedAt`.
5. The toast "Category deactivated" appears and the row shows "Inactive".
6. To reactivate, the user turns the switch on, confirms "Activate **<name>**?" with Activate, and sees "Category activated".

## Alternative flows

- **Cancel.** Nothing changes; the switch returns to its previous position.
- **Has active sub-categories.** "Deactivate its sub-categories first."; nothing changes.
- **Has active items.** "This category is used by active items. Move or deactivate them first."; nothing changes.
- **Parent inactive (activating a sub-category).** "Activate the parent category first."; nothing changes.
- **Already in that state.** No harm; succeeds.
- **Not found.** Unknown, deleted, asset or another company's id: "Category not found".
- **No delete.** No delete action in the UI. A direct call to the legacy delete endpoint returns "Categories cannot be deleted. Deactivate the category instead." and writes nothing.
- **No permission (direct call).** "You don't have permission to perform this action."
- **Server failure.** Toast with the message; the switch returns to its previous position.

## Acceptance criteria

1. Given an unused active category, when it is switched off and confirmed, then it shows "Inactive" here and stores `IsActive = 0`, `IsDeleted = 0`.
2. Given an inactive category, then it is not offered in the item / recipe category dropdowns.
3. Given a main category with an active sub-category, when it is switched off, then "Deactivate its sub-categories first." and it stays active.
4. Given a category used by an active item (as category or sub-category), when it is switched off, then "This category is used by active items. Move or deactivate them first." and it stays active.
5. Given an inactive main category with an inactive sub-category, when the sub-category is switched on, then "Activate the parent category first."
6. Given the user cancels, then nothing changes.
7. Given a user without `edit_item_category`, then the switch is hidden and a direct call returns the permission message.
8. Given a CO-B or asset category id, then "Category not found" and nothing changes (D3).
9. Given any caller, when `DelStockGroupById` is called, then "Categories cannot be deleted. Deactivate the category instead." and no row changes.

## Platform notes

### RMS API

- `POST /Setting/ChangeStockGroupStatus` `{ stockGroupId, isActive }` — `[CompanyAuthorize]`, `edit_item_category`, company and user from the token. Updates `UpdatedAt` (an edit form opened earlier is then refused as stale).
- `POST /Setting/DelStockGroupById` — always refuses.

### Data — Tenant DB

- Updates `StockGroup` only. Checks `StockGroup` (children by `ParentStockGroupId`) and `StockItem` (`StockGroupId`, `SubStockGroupId`, `IsActive = 1`).

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Switch, chips and confirmations: [`ui-ux.md`](./ui-ux.md#5-confirmations).

## Configuration

None.

## Out of scope

- Bulk activate / deactivate.

## Open questions

None.
