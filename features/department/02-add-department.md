# 02 — Add a department

## Summary

A company user with `create_department` adds a department by typing its name. The system checks the name (allowed characters, length, unique in the company including inactive departments), saves it as Active for the caller's company, and shows it in the list.

## Prerequisites

- **Backend.** `AddEditDepartment` with server validation, exact messages and `Status=false` on any refusal; `ValidateDeptName` for the live check (see [`api-contract.md`](./api-contract.md#save-department)).
- **Data.** `SP_AddEditDepartment`, `SP_ValidateDeptName` changes.
- **Depends on.** [03](./03-rename-department.md) shares the save contract.

## Actors & preconditions

- **Actor:** a company user with `create_department`.
- **Trigger:** "＋ Add Department" in the header or the empty state.

## Main flow

1. The user clicks "＋ Add Department"; the dialog "Add Department" opens.
2. The user types the Department Name; the system checks for duplicates as they type.
3. The user clicks "Add Department"; the button shows a spinner and the dialog can't be closed.
4. The system trims the name, validates it and saves the department for the token's company: `IsActive = 1`, `IsDelete = 0`, `CreatedBy` = caller, `CreatedAt` = now (UTC).
5. The dialog closes, "Department created" appears and the list refreshes.

## Alternative flows

- **Empty / spaces only.** "Department name is required".
- **Over 100 characters.** The UI stops at 100; the API refuses longer: "Department name must be 100 characters or less".
- **Other characters.** "Department name can only contain letters, numbers, spaces and & - ( ) ' / . ,".
- **Duplicate.** The trimmed name equals (case-insensitive) another non-deleted department of the company, active or inactive: "Department name already exists" under the field and as a toast; nothing saved (D2, D3).
- **Same name in another company.** Allowed.
- **No permission (direct call).** "You don't have permission to perform this action."
- **Double click.** One department is created.
- **Discard.** Closing a changed form asks "Discard changes?".
- **Server failure.** "Something went wrong. Please try again." (or "Failed to save department" when unreachable); the dialog stays open.

## Acceptance criteria

1. Given a valid name, then "Department created" and the department is listed as Active.
2. Given " kitchen " while "Kitchen" exists (active or inactive), then "Department name already exists" and nothing is saved.
3. Given "Sales" exists only in CO-B, when CO-A adds "Sales", then it is saved.
4. Given 101 characters, then the UI doesn't accept it and the API refuses it.
5. Given "Bar@Night", then the characters message and nothing saved; given "Front Office (Day) - 2", then it is saved.
6. Given a duplicate, then the toast is the duplicate message, never a success (D2).
7. Given the body carries `CompanyId`, `CreatedBy` or `IsActive = false`, then they are ignored: token company and caller, Active.
8. Given a user without `create_department`, then the Add button is hidden and a direct call returns the permission message.

## Platform notes

### RMS API

- `POST /AssetManagement/AddEditDepartment` with an empty `DepartmentMasterId` — `[CompanyAuthorize]`, `create_department` checked on the server. `POST /Users/ValidateDeptName` — `[CompanyAuthorize]`.

### Data — Tenant DB

- Inserts `DepartmentMaster` under a per-company lock; dates `GETUTCDATE()`.

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
