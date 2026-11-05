# Data model — Role

Tenant DB. **No table schema change.** Scripts: edit the object files in `RMS_API/DBSCRIPT/SP/` (`CREATE OR ALTER`).

| Table | Use |
|---|---|
| `Role` | `RoleId`, `RoleName` nvarchar(100) (rule: ≤ 50), `Description` nvarchar(300), `IsActive`, `IsSystemRole`, `CompanyId`, `CreatedDate/By`, `ModifiedDate/By`. Edit token `ISNULL(ModifiedDate, ISNULL(CreatedDate, '19000101'))` |
| `UserRoleMapping` | `UserId`, `RoleId`, `IsActive` — users with the role (`User.IsActive` for "active users") |
| `RolePermission`, `RoleMenuPermission` | Permissions and menu visibility of a role |
| `DashboardWidgetCommon`, `PermissionList` (legacy), `CompanyLeaveSetting` | Reference a role (delete clean-up / delete block) |

## Procedures (`DBSCRIPT/SP/`)

| Procedure | Change |
|---|---|
| `Sp_GetRoleDetails` | Returns `IsSystemRole`, `UpdatedAt` token, `UserCount` |
| `Sp_AddEditRole` | `+ @UpdatedAt`; lock `Role_<company>`; codes `NotFound`, `Stale`, `Exist`, `SystemRole`; trimmed duplicate among all roles; add active non-system; edit name + description only, company-scoped; UTC; returns `RoleId`, `Msg` (`Inserted` / `Updated` / code) |
| `usp_GetRolePermissionTree` | `+ @CompanyId = NULL`; role must belong to the company |
| `usp_ToggleRolePermission` | Role must belong to `@CompanyId`; `+ @ChangedBy` own-role check; refuses system roles; returns a code row instead of RAISERROR for business refusals |
| `Sp_GetRoleList` (**new**) | All company roles except `Support`, with `UserCount` (users holding the role, active and inactive), `UpdatedAt`, `IsSystemRole` |
| `Sp_ChangeRoleStatus` (**new**) | `NotFound`, `SystemRole`, `HasActiveUsers` |
| `Sp_DeleteRole` (**new**) | `NotFound`, `SystemRole`, `HasUsers`, `UsedInLeaveSettings`; deletes dependants and the role |
| `SP_DeleteWidgetCommonRoleWise` | Unchanged, **no longer called** |
