# 03 — Rename a menu section

## Summary

A user with `edit_menu_section` changes a section's name. Recipes point at the section by id, so they and the POS show the new name. The save changes the name only; status uses the switch ([04](./04-switch-menu-section-on-off.md)).

## Prerequisites

- **Backend.** `GetMenuSectionById` returns a section of the caller's company (active or inactive) with an edit token; `AddEditMenuSection` refuses unknown ids and stale saves.
- **Data.** `SP_GetMenuSectionById`, `SP_AddEditMenuSection`.
- **Depends on.** [02](./02-add-menu-section.md).

## Actors & preconditions

- **Actor:** a company user with `edit_menu_section`.
- **Trigger:** the Edit icon.

## Main flow

1. Edit loads the section (overlay).
2. The dialog "Edit Menu Section — <name>" opens with the name.
3. "Save changes" validates as in [02](./02-add-menu-section.md), checks the section still exists in the company and the edit token matches, and updates the name, `UpdatedBy`, `UpdatedAt` (UTC). `IsActive` and `Meta_MenuSectionId` are untouched.
4. "Menu section updated"; the list refreshes.

## Alternative flows

- **Validation / duplicate.** As in [02](./02-add-menu-section.md); its own name never counts.
- **Inactive section.** Can be renamed; stays Inactive (D5).
- **Not found** (unknown or another company's id, on load or save): "Menu section not found"; the list refreshes (D1, D2).
- **Changed by someone else** (token differs or missing): "This menu section was changed by someone else. Reload and try again.".
- **No permission.** "You don't have permission to perform this action."

## Acceptance criteria

1. Given "Starters" renamed to "Starters & Soups", then "Menu section updated" and Recipe and the POS show the new name.
2. Given the new name duplicates another section, then the duplicate message.
3. Given an inactive section, then it opens with its own data and stays Inactive after saving.
4. Given the body sends `IsActive = false`, then the name changes and the status doesn't.
5. Given a CO-B id, then "Menu section not found" on load and on save, and CO-B's row is unchanged (D1, D2).
6. Given another user saved first, or no token is sent, then the stale message.
7. Given a user without `edit_menu_section`, then no Edit icon and the permission message on a direct call.

## Platform notes

### RMS API

- `POST /Setting/GetMenuSectionById` — `[CompanyAuthorize]`. `POST /Setting/AddEditMenuSection` with an id — `edit_menu_section`, `UpdatedAt` required.

### Data — Tenant DB

- Updates `MenuSection.MenuSectionName`, `UpdatedBy`, `UpdatedAt` for `MenuSectionId` **and** `CompanyId`.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Dialog: [`ui-ux.md`](./ui-ux.md#3-dialog--form).

## Configuration

None.

## Out of scope

None.

## Open questions

None.
