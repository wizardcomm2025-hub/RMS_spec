# API contract — Role

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Roles/GetRoleList` | — | Uses `Sp_GetRoleList`: counts, `isSystemRole`, `updatedAt` |
| `/Setting/GetRoleDetails` | — | `isSystemRole`, `updatedAt`, `userCount`; "Role not found" |
| `/Setting/SaveRole` | `create_roles` (add) / `edit_roles` (edit) | Validation, codes → messages, stale check, system role refused |
| `/Roles/ChangeRoleStatus` | `edit_roles` | **New** |
| `/Roles/DeleteRole` | `delete_roles` | Real delete with the Q3 checks |
| `/Permission/GetRolePermissionTree?roleId=` | — | Company-scoped; "Role not found" |
| `/Permission/ToggleRolePermission` | `edit_roles` | Company, system-role and own-role checks |
| `/Permission/GetRoleDDl` | — | Company from the token (body `CompanyId` ignored) |
| Legacy `/Roles/AddEditRole`, `/Setting/SaveMenuMenuActionList`, `/Permission/SavePermission` | — | **Refused**: "This endpoint is no longer available." |
| Legacy reads `/Setting/GetMenuMenuActionList`, `/Setting/GetPerissionList`, `/Permission/GetPermissionDtl` | — | `[CompanyAuthorize]`, company from the token |

Save fields: `roleId` (empty GUID = add), `roleName`, `description`, `updatedAt` (edit, required); `isActive`, `isSystemRole`, `companyId` ignored. Status: `{ roleId, isActive }`. Delete: `{ roleId }`.

## Messages (HTTP 200, `status: false`)

`Role name is required` · `Role name must be 50 characters or less` · `Role name can only contain letters, numbers, spaces and & - ( ) ' / . ,` · `Role name already exists` · `Description must be 300 characters or less` · `Role not found` · `This role was changed by someone else. Reload and try again.` · `System roles can't be changed.` · `You can't change the permissions of your own role.` · `This role is assigned to active users. Move them to another role first.` · `This role is assigned to users. Move them to another role first.` · `This role is used in leave settings. Remove it there first.` · `One or more permissions are invalid.` · `You don't have permission to perform this action.` · `Something went wrong. Please try again.` · `This endpoint is no longer available.`. 401 `{ "message": "Unauthorized" }` for no / invalid / SuperAdmin token.

Success (UI): "Role created", "Role updated", "Permissions updated", "Role activated", "Role deactivated", "Role deleted".
