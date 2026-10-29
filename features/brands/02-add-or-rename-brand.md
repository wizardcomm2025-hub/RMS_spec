# 02 — Add or rename a brand

## Summary

A user with `create_brand` adds a brand; a user with `edit_brand` renames one. The name is checked (characters, length, unique in the company including switched-off brands). The save never changes status or `IsShortShelfLife`.

## Actors & preconditions

- **Actor:** a logged-in company user; permissions per step below.

## Main flow

1. "＋ Add Brand" opens "Add Brand"; Edit loads the brand and opens "Edit Brand — <name>".
2. Live duplicate check while typing.
3. Save: trimmed and validated; add → Active for the token's company (UTC); edit → name only, for the token's company, edit token must match.
4. "Brand created" / "Brand updated"; the list refreshes.

## Alternative flows

- "Brand name is required" · "Brand name must be 100 characters or less" · "Brand name can only contain letters, numbers, spaces and & - ( ) ' / . ," · "Brand name already exists" (D4).
- **Not found** (unknown / another company's id): "Brand not found" (D2).
- **Stale:** "This brand was changed by someone else. Reload and try again.".
- **No permission:** "You don't have permission to perform this action."
- **Failure:** "Something went wrong. Please try again." (or "Failed to save brand").

## Acceptance criteria

1. Valid name → created Active / renamed.
2. " amul " while "Amul" exists (active or inactive) → duplicate message, never a success toast.
3. A CO-B id → "Brand not found", CO-B unchanged.
4. Body `IsActive = false`, `IsDeleted`, `IsShortShelfLife`, `CompanyId` → ignored.
5. Stale / missing token → stale message.
6. No permission → hidden button and the permission message.

## Platform notes

### RMS API

- `POST /Setting/AddEditStockDetailsType` — `[CompanyAuthorize]`, `create_brand` / `edit_brand`. `POST /Setting/GetStockTypeDetailsById`, `POST /Users/ValidateStockTypeName` — `[CompanyAuthorize]`.

### Data — Tenant DB

- `SP_AddEditStockTypeDetails` (lock, codes, company-scoped), `SP_GetStockTypeDetailsById`, `SP_ValidateStockTypeName`.

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
