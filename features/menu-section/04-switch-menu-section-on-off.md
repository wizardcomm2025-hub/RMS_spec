# 04 — Switch a menu section on or off

## Summary

Instead of deleting, a user with `edit_menu_section` switches a section Inactive. An inactive section can't be picked for a recipe. It can't be switched off while active recipes use it (Q1). Existing recipes keep their section in the Recipe dialog (Q4).

## Prerequisites

- **Backend.** New `POST /Setting/ChangeMenuSectionStatus`; `DeleteMenuSection` blocked.
- **Data.** New `SP_ChangeMenuSectionStatus`.
- **Depends on.** [01](./01-view-and-search-menu-sections.md).

## Actors & preconditions

- **Actor:** a company user with `edit_menu_section`.
- **Trigger:** the Active switch.

## Main flow

1. The user turns the switch off.
2. Confirmation: title "Deactivate menu section", text "Deactivate **<name>**? It can't be picked for recipes any more. You can activate it again at any time." — Cancel · Deactivate.
3. The system checks no active recipe uses it (`StockItem.MenuSectionId`, `IsActive = 1`, not deleted), sets `IsActive = 0`, records `UpdatedBy` / `UpdatedAt`.
4. "Menu section deactivated"; the row shows "Inactive".
5. Reactivate: switch on, confirm "Activate **<name>**?" with Activate, toast "Menu section activated".

## Alternative flows

- **Cancel.** Nothing changes.
- **Used by active recipes.** "This menu section is used by active recipes. Move them to another section first."; nothing changes.
- **Not found** (unknown or another company's id): "Menu section not found" (D2).
- **Already in that state.** Succeeds.
- **No delete.** A direct call to `DeleteMenuSection` returns "Menu sections cannot be deleted. Deactivate the menu section instead." and writes nothing.
- **Recipe dialog (D7).** It links the section by id. A recipe whose section is inactive shows "<name> (inactive)" selected and keeps it on save; the dropdown offers only active sections as a new choice.
- **No permission.** "You don't have permission to perform this action."
- **Failure.** Toast; the switch returns.

## Acceptance criteria

1. Given a section used only by inactive or deleted recipes, when switched off and confirmed, then "Inactive" and `IsActive = 0`.
2. Given a section used by an active recipe, then the used-by message and it stays Active.
3. Given it is inactive, then Recipe's Menu Section dropdown doesn't offer it for a new choice.
4. Given a recipe already in an inactive section, when edited and saved without touching the section, then the recipe keeps that section (D7).
5. Given a CO-B id, then "Menu section not found" and nothing changes.
6. Given any caller, `DeleteMenuSection` refuses and writes nothing.
7. Given a user without `edit_menu_section`, then no switch and the permission message on a direct call.

## Platform notes

### RMS API

- `POST /Setting/ChangeMenuSectionStatus` `{ menuSectionId, isActive }` — `[CompanyAuthorize]`, `edit_menu_section`.
- `POST /Setting/DeleteMenuSection` — always refuses.

### Data — Tenant DB

- Updates `MenuSection`; reads `StockItem`.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Confirmations: [`ui-ux.md`](./ui-ux.md#5-confirmations). Recipe dialog: [`ui-ux.md`](./ui-ux.md#11-recipe-dialog).

## Configuration

None.

## Out of scope

None.

## Open questions

None.
