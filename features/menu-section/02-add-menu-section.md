# 02 — Add a menu section

## Summary

A company user with `create_menu_section` adds a section by typing its name. The system checks the name (characters, length, unique in the company including inactive sections) and saves it as Active.

## Prerequisites

- **Backend.** `AddEditMenuSection` with server validation and `status: false` on any refusal; `ValidateMenuName` for the live check (see [`api-contract.md`](./api-contract.md#save-section)).
- **Data.** `SP_AddEditMenuSection`, `SP_ValidateMenuName`.
- **Depends on.** [03](./03-rename-menu-section.md) shares the save.

## Actors & preconditions

- **Actor:** a company user with `create_menu_section`.
- **Trigger:** "＋ Add Menu Section" in the header or the empty state.

## Main flow

1. The dialog "Add Menu Section" opens.
2. The user types the name; duplicates are checked as they type.
3. "Add Menu Section" saves (spinner; the dialog can't close).
4. The system trims, validates and saves for the token's company: `IsActive = 1`, `CreatedBy` = caller, `CreatedAt` = now (UTC).
5. The dialog closes, "Menu section created" appears, the list refreshes.

## Alternative flows

- **Empty / spaces only.** "Menu section name is required".
- **Over 100.** The UI stops at 100; the API refuses longer: "Menu section name must be 100 characters or less".
- **Other characters.** "Menu section name can only contain letters, numbers, spaces and & - ( ) ' / . ,".
- **Duplicate** (trimmed, case-insensitive, active or inactive): "Menu section name already exists" under the field and as a toast; nothing saved (D3, D4).
- **Same name in another company.** Allowed.
- **No permission (direct call).** "You don't have permission to perform this action."
- **Double click.** One section is created.
- **Discard.** "Discard changes?".
- **Failure.** "Something went wrong. Please try again." (or "Failed to save menu section"); the dialog stays open.

## Acceptance criteria

1. Given a valid name, then "Menu section created" and it is listed Active.
2. Given " starters " while "Starters" exists (active or inactive), then the duplicate message and nothing saved; never a success toast (D3).
3. Given "Starters" only in CO-B, then CO-A can add it.
4. Given 101 characters or "Soup@Night", then the length / characters message; "Chef's Special (Veg) - 2" is saved.
5. Given the body sends `IsActive = false`, `CompanyId` or `Meta_MenuSectionId`, then they are ignored: Active, token company, no meta link.
6. Given a user without `create_menu_section`, then no Add button and a direct call returns the permission message.

## Platform notes

### RMS API

- `POST /Setting/AddEditMenuSection` with `MenuSectionId = 0` — `[CompanyAuthorize]`, `create_menu_section`. `POST /Users/ValidateMenuName` — `[CompanyAuthorize]`.

### Data — Tenant DB

- Inserts `MenuSection` under a per-company lock; UTC dates.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Dialog: [`ui-ux.md`](./ui-ux.md#3-dialog--form).

## Configuration

None.

## Out of scope

- Bulk import.

## Open questions

None.
