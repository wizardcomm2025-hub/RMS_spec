# 05 — Add or edit a sub-brand

## Summary

A user with `create_sub_brand` adds a sub-brand under an active brand; a user with `edit_sub_brand` renames it or moves it to another active brand — moving only while no active raw material uses it (Q4).

## Actors & preconditions

- **Actor:** a logged-in company user; permissions per step below.

## Main flow

1. "＋ Add Sub Brand" opens "Add Sub Brand" with **Brand** (searchable, active brands) and **Sub Brand Name**; Edit opens "Edit Sub Brand — <name>".
2. Live duplicate check within the chosen brand.
3. Save: validated; add → Active; edit → name and brand only, company-scoped, edit token must match.
4. "Sub-brand created" / "Sub-brand updated".

## Alternative flows

- "Brand is required" · "Brand not found" (unknown / another company's brand) · "Pick an active brand." (inactive brand).
- "Sub-brand name is required" · "Sub-brand name must be 100 characters or less" · "Sub-brand name can only contain letters, numbers, spaces and & - ( ) ' / . ," · "Sub-brand name already exists under this brand".
- **Move while used:** "This sub-brand is used by active raw materials, so its brand can't be changed.".
- "Sub-brand not found" · "This sub-brand was changed by someone else. Reload and try again." · "You don't have permission to perform this action."

## Acceptance criteria

1. "Diet Coke" under Coca-Cola → created Active.
2. " diet coke " again under Coca-Cola → duplicate; under Pepsi → allowed.
3. Brand inactive or CO-B → the brand messages.
4. Move an unused sub-brand → saved; move one with an active raw material → the move message.
5. CO-B sub-brand id → not found and CO-B unchanged (D2).
6. Body `IsActive`, `IsDeleted`, `CompanyId` ignored; stale token refused.

## Platform notes

### RMS API

- `POST /Setting/AddEditStockSubDetailsType` — `create_sub_brand` / `edit_sub_brand`. `POST /Setting/GetStockSubTypeDetailsById`, `POST /Users/ValidateStockSubTypeName` — `[CompanyAuthorize]`.

### Data — Tenant DB

- `SP_AddEditStockSubTypeDetails` (lock, codes, company-scoped, never writes `CompanyId`), `SP_GetStockSubTypeDetailsById`, `SP_ValidateStockSubTypeName`.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

See [`ui-ux.md`](./ui-ux.md).

## Configuration

None.

## Out of scope

None.

## Open questions

None.
