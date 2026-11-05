# User Management → User List

A **user** is a person who signs in to RMS (`dbo.User`, per company). Each user has one or more **roles** (`UserRoleMapping`) that decide what they can see and do, a department, a reporting head, and the **locations** they work at (`UserWarehouseMapping`).

The screen lets an admin list and search users (server-paged), add and edit them, assign their roles and locations, and switch a user Active / Inactive. Users are never deleted.

Status (2026-10-30): **Built** — approved 2026-10-29; DBSCRIPT files not yet run on dev; manual tests deferred. Plan: [`RMS_API/specs/user-list/plan.md`](../../../RMS_API/specs/user-list/plan.md).

Feature code: `USR`

Developer guide: [`developer-guide.html`](./developer-guide.html).

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search-users.md`](./01-view-and-search-users.md) | See users page by page; search; filter by status |
| 02 | [`02-add-or-edit-user.md`](./02-add-or-edit-user.md) | Add a user (with password) or edit their details and locations |
| 03 | [`03-assign-roles.md`](./03-assign-roles.md) | Give a user one or more roles |
| 04 | [`04-switch-user-on-off.md`](./04-switch-user-on-off.md) | Deactivate / reactivate a user |

## Defects this feature fixes (found 2026-10-29)

| # | Defect | Fixed by |
|---|---|---|
| D1 | **Privilege escalation:** `AddEditAssignRole` has **no permission check and no company check** — any logged-in user can give any user (themselves included) any role, even the Admin role or another company's role | [03](./03-assign-roles.md) |
| D2 | `DeleteUser` takes `CompanyId` from the request body — anyone can switch off another company's user; there is no delete permission | [04](./04-switch-user-on-off.md) |
| D3 | `SP_AddEditUser` re-writes `UserWarehouseMapping` for the user id **before** checking the user belongs to the caller's company, and never checks that the locations, department and reporting heads belong to the company | [02](./02-add-or-edit-user.md) |
| D4 | No server permission checks (`create_userlist`, `edit_userlist`) on save, roles or status; SuperAdmin tokens pass `[Authorize]`; `ValidateUserName` has no login check at all | all |
| D5 | Duplicate user name / phone returns `Type = error` inside `Status = true`; the UI ignores it and shows "User created successfully"; the role call afterwards runs anyway | [02](./02-add-or-edit-user.md) |
| D6 | An admin can remove their own roles or switch themselves off; a company can lose its last active admin | [03](./03-assign-roles.md), [04](./04-switch-user-on-off.md) |
| D7 | `SP_ChangePassword` returns the **new password in clear text** in its result, which the API sends back to the browser | api-contract |
| D8 | No server-side validation of names, email, phone, address, zip and password rules (UI only); `GETDATE()` instead of UTC; no stale-save check | [02](./02-add-or-edit-user.md) |
| D10 | `GetUserList`, `GetUserDtlByUserId` and `ViewProfileDetails` sent every user's **password hash** to the browser | api-contract |
| D9 | List page not on the recipe (header, states, phone cards); role field allows only one role in the UI although the API stores a list | [`ui-ux.md`](./ui-ux.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | An admin can't change **their own** roles or status (details stay editable) | 2026-10-29 |
| Q2 | Refuse switching off a user, or removing the system Admin role from them, when they're the **last active user with a system role** | 2026-10-29 |
| Q3 | A user has **one or more** roles; the role field becomes a multi-select of the company's **active** roles | 2026-10-29 |
| Q4 | Keep **server paging**: search, status filter and pages by the API; standard footer with page buttons | 2026-10-29 |
| Q6 | Staff saves go through a **new** `Sp_SaveUser`; `SP_AddEditUser` is left as it is because it also creates customers when an order is placed (`AddUserForPlaceOrder`) | 2026-10-29 |
| Q5 | No delete (the endpoint refuses); field rules as the current form (names letters only, 35; email; phone; address; 6-digit zip; new-user password 8–12 with upper, lower, digit and symbol), now also checked on the server | 2026-10-29 |

## Depends on

User Management → Role (active roles, system role flag); Setting → Location, Department (dropdown sources).
