# 04 — Switch a department on or off

## Summary

Instead of deleting a department, a user with `edit_department` switches it Inactive. An inactive department disappears from the user form and other dropdowns (they list only active departments) but stays here and can be switched back on. A department can't be switched off while active users or active roster patterns belong to it.

## Prerequisites

- **Backend.** New `POST /AssetManagement/ChangeDepartmentStatus` (see [`api-contract.md`](./api-contract.md#change-status)); `DeleteDepartment` blocked.
- **Data.** New `SP_ChangeDepartmentStatus`.
- **Depends on.** [01](./01-view-and-search-departments.md).

## Actors & preconditions

- **Actor:** a company user with `edit_department`.
- **Trigger:** the Active switch on a row or phone card.

## Main flow

1. The user turns the switch off on an active department.
2. A confirmation opens: title "Deactivate department", text "Deactivate **<name>**? It will be hidden from user, roster and other department lists. You can activate it again at any time." — Cancel · Deactivate.
3. The user clicks Deactivate.
4. The system checks no active user (`User.DepartmentId`, `IsActive = 1`) and no active roster pattern (`RosterPattern.DepartmentmasterId`, `IsActive = 1`) belongs to it, sets `IsActive = 0` and records `UpdatedBy` / `UpdatedAt`.
5. The toast "Department deactivated" appears; the row shows "Inactive".
6. To reactivate: switch on, confirm "Activate **<name>**?" with Activate, toast "Department activated".

## Alternative flows

- **Cancel.** Nothing changes; the switch returns.
- **Active users.** "This department has active users. Move or deactivate them first."; nothing changes.
- **Active roster patterns.** "This department is used by active roster patterns. Deactivate them first."; nothing changes.
- **Already in that state.** Succeeds; no harm.
- **Not found.** Unknown, deleted or another company's id: "Department not found".
- **No delete.** None in the UI; a direct call to `DeleteDepartment` returns "Departments cannot be deleted. Deactivate the department instead." and writes nothing.
- **No permission (direct call).** "You don't have permission to perform this action."
- **Server failure.** Toast with the message; the switch returns.

## Acceptance criteria

1. Given an unused active department, when switched off and confirmed, then "Inactive" here, `IsActive = 0`, `IsDelete = 0`.
2. Given it is inactive, then it isn't offered in the User List form's department dropdown.
3. Given a department with an active user, when switched off, then the active-users message and it stays Active.
4. Given a department with only an active roster pattern, when switched off, then the roster-pattern message.
5. Given a department used only by inactive users and inactive roster patterns, then it can be switched off.
6. Given the user cancels, then nothing changes.
7. Given a user without `edit_department`, then the switch is hidden and a direct call returns the permission message.
8. Given a CO-B id, then "Department not found" and nothing changes.
9. Given any caller, when `DeleteDepartment` is called, then the refusal message and no row changes.

## Platform notes

### RMS API

- `POST /AssetManagement/ChangeDepartmentStatus` `{ departmentMasterId, isActive }` — `[CompanyAuthorize]`, `edit_department`, company and user from the token; updates `UpdatedAt`.
- `POST /AssetManagement/DeleteDepartment` — always refuses.

### Data — Tenant DB

- Updates `DepartmentMaster` only; reads `User` and `RosterPattern`.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Switch, chip and confirmations: [`ui-ux.md`](./ui-ux.md#5-confirmations).

## Configuration

None.

## Out of scope

- Checking requisitions, assets or checklists before deactivating (they keep historic links).

## Open questions

None.
