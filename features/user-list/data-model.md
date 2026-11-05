# Data model — User List

Tenant DB. **No table schema change.** Scripts: object files in `RMS_API/DBSCRIPT/SP/` (`CREATE OR ALTER`).

| Table | Use |
|---|---|
| `User` | `UserId`, `CompanyId`, `UserName`, `Email`, names, address, phone, `ImagePath`, `IsActive`, `IsCustomer`, `DepartmentId`, `ReportingHeadId`, `AltReportingHeadId`, `Password` / `Salt`, `IsForceLogin`, `CreatedDate/By`, `ModifiedDate/By`. Edit token `ISNULL(ModifiedDate, ISNULL(CreatedDate, '19000101'))` |
| `UserRoleMapping` | Active roles of a user (`IsActive = 1`), `CompanyId` |
| `UserWarehouseMapping` | Active locations of a user |
| `Role` | `IsSystemRole` — "admin" for the last-admin rule (Q2) |

"Admin" = an active user holding an active `UserRoleMapping` to a role with `IsSystemRole = 1`.

| Procedure | Change |
|---|---|
| `Sp_SaveUser` (**new**) | Staff add / edit. Codes `ok`, `NotFound`, `Stale`, `DupUserName`, `DupPhone`, `BadDepartment`, `BadReportingHead`, `BadLocation`; every write company-scoped; location mapping only after the user is confirmed in the company; `@UpdatedAt` token (returns the new one); status, user name and password not changed by edit; UTC. User name unique across companies (active or not); phone unique per company. An unchanged department / reporting head / location is kept even if since switched off |
| `SP_AddEditUser` | **Unchanged** — still used by `AddUserForPlaceOrder` (customers) |
| `Sp_SetUserRoles` (**new**) | Replace a user's active roles: checks company, active company roles, self, last admin; force sign-out |
| `Sp_SetUserActive` | New optional `@ActorId`; codes `ok`, `NotFound`, `Self`, `LastAdmin`; force sign-out on deactivate; UTC |
| `SP_GetAllUserDetails` | New `@IsActive` filter (NULL = all); no `Password` column; adds `RoleIds`, `HasSystemRole`, `UpdatedAt`; roles limited to the company's active roles; page size capped at 100 |
| `SP_ChangePassword` | `[Password]` column kept for the result shape but always NULL; UTC |

`Sp_SaveUser`, `Sp_SetUserRoles` and `Sp_SetUserActive` share the lock `User_<CompanyId>` (`sp_getapplock`), so two last-admin checks can't pass at the same time. Support users (`IsSupportUser = 1`) are never listed, edited or counted as admins.
