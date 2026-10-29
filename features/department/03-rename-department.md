# 03 — Rename a department

## Summary

A company user with `edit_department` opens a department (active or inactive) and changes its name. The same name rules as Add apply. The status isn't changed here (that's [04](./04-switch-department-on-off.md)). The server only edits the caller's own company's departments and refuses a save if someone else changed the department in the meantime.

## Prerequisites

- **Backend.** `GetDepartmentById` (company filter, not deleted, `UpdatedAt` token); `AddEditDepartment` as in [02](./02-add-department.md).
- **Data.** `SP_GetDepartmentById`, `SP_AddEditDepartment` changes.
- **Depends on.** [02](./02-add-department.md).

## Actors & preconditions

- **Actor:** a company user with `edit_department`.
- **Trigger:** the Edit icon on a row or phone card.

## Main flow

1. The user clicks Edit; the dialog "Edit Department — <name>" opens behind a loading overlay with the stored name.
2. The user changes the name; duplicates are checked as they type, excluding this department.
3. The user clicks "Save changes".
4. The system checks the department hasn't changed since it was loaded (`UpdatedAt`), validates the name, and updates it: `UpdatedBy` = caller, `UpdatedAt` = now (UTC). `IsActive`, `CompanyId`, `CreatedBy` don't change.
5. The dialog closes, "Department updated" appears and the list refreshes.

## Alternative flows

- **Duplicate / bad name.** As in [02](./02-add-department.md).
- **Only case or spaces change.** Allowed.
- **Not found.** Unknown, deleted or another company's id, on load or save: "Department not found"; nothing shown or changed; the list refreshes and the Add dialog doesn't open (D4).
- **Changed by someone else.** Renamed or switched by another user since it was loaded, or no `UpdatedAt` sent: "This department was changed by someone else. Reload and try again."; the dialog stays open; nothing changes.
- **No permission (direct call).** "You don't have permission to perform this action."

## Acceptance criteria

1. Given department A, when it is renamed, then "Department updated" and the new name is listed.
2. Given B's name typed into A, then "Department name already exists" live and on save; nothing changes.
3. Given an inactive department, then it opens, saves and stays Inactive.
4. Given a save that sends `IsActive = false` for an active department, then it stays Active.
5. Given a CO-B id with a CO-A token, on load or save, then "Department not found" and nothing about it is returned or changed (D4).
6. Given two users edit the same department, when the second saves, then the changed-by-someone-else message and the first user's name remains.
7. Given a user without `edit_department`, then the Edit icon is hidden and a direct save returns the permission message.

## Platform notes

### RMS API

- `POST /AssetManagement/GetDepartmentById` — `[CompanyAuthorize]`; null → "Department not found"; returns `UpdatedAt`.
- `POST /AssetManagement/AddEditDepartment` with the id and `UpdatedAt`; `edit_department` on the server.

### Data — Tenant DB

- Update filtered by `DepartmentMasterId`, `CompanyId` and `IsDelete = 0`; only `DepartmentName`, `UpdatedBy`, `UpdatedAt` change.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Same dialog as Add in edit mode.

## Configuration

None.

## Out of scope

- Moving users between departments.

## Open questions

None.
