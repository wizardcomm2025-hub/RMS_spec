# User Management → Role

A **Role** is a named set of permissions (Kitchen Staff, Store Manager, Admin). Each user has one or more roles (`UserRoleMapping`); what a user can see and do is the union of their roles' permissions (`RolePermission`, menu visibility in `RoleMenuPermission`). Each company keeps its own roles in the Tenant DB table `dbo.Role`; onboarding creates **system roles** (`IsSystemRole = 1`).

The screen lets a company admin list and search roles, add and edit them (name, description, permissions), switch a role Active / Inactive, and delete an unused role.

Status (2026-10-29): **Approved — API and UI implemented (builds pass); DBSCRIPT procedures changed, not yet run on dev; not tested.** Plan: [`RMS_API/specs/role/plan.md`](../../../RMS_API/specs/role/plan.md).

Feature code: `ROL`

## Scope

- Close the privilege-escalation and cross-company holes below (first).
- Server-side rules for names, system roles, own role, switch-off and delete.
- Rebuild the list page on the §12 recipe; keep the permission tree dialog, aligned to the API.

## Out of scope

- The permission catalogue itself (`Meta_Permission`, `Meta_PermissionGroup`) and plan-level menu access (`CompanyMenuAccess`).
- Assigning roles to users (User List module).
- The force-logout behaviour after a permission change (kept as is).

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search-roles.md`](./01-view-and-search-roles.md) | See every role with its user count; search; filter |
| 02 | [`02-add-or-edit-role.md`](./02-add-or-edit-role.md) | Add a role or change its name / description |
| 03 | [`03-set-role-permissions.md`](./03-set-role-permissions.md) | Grant / remove permissions in the permission tree |
| 04 | [`04-switch-role-on-off.md`](./04-switch-role-on-off.md) | Deactivate / reactivate a role |
| 05 | [`05-delete-role.md`](./05-delete-role.md) | Delete a role nobody uses |

## Defects this feature fixes (found 2026-10-29)

| # | Defect | Fixed by |
|---|---|---|
| D1 | **Privilege escalation:** `ToggleRolePermission` has **no permission check** — any logged-in user can grant any permission to any role, including their own | [03](./03-set-role-permissions.md) |
| D2 | **Cross-company:** `usp_ToggleRolePermission` checks the role exists but not its company — a user can change **another company's** role permissions; `GetRolePermissionTree` reads any company's role | [03](./03-set-role-permissions.md) |
| D3 | **System roles** are protected only in the UI — the API lets anyone rename, switch off, delete or change the permissions of the company Admin role | [02](./02-add-or-edit-role.md)–[05](./05-delete-role.md) |
| D4 | "Delete" only switches the role off (`IsActive = 0`, no company filter in the SP) and checks only the **first** user mapped to the role, so a role with other active users can be removed | [05](./05-delete-role.md) |
| D5 | `GetRoleDDl` takes the company from the request body (any company's roles); the legacy `AddEditRole`, `SaveMenuMenuActionList` and `SavePermission` writes are reachable though the UI doesn't use them | api-contract |
| D6 | Duplicate check counts only active roles and isn't trimmed; the save always reports success text; no stale-save check; no server permission checks; SuperAdmin tokens pass `[Authorize]`; `GETDATE()` instead of UTC | [02](./02-add-or-edit-role.md) |
| D7 | Status switch saves through the full role save with no confirmation and no check for active users | [04](./04-switch-role-on-off.md) |
| D8 | List page not on the recipe (full-page loader, no states, no phone layout) | [`ui-ux.md`](./ui-ux.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | System roles are **fully read-only** through the API (name, description, status, permissions, delete) | 2026-10-29 |
| Q2 | A user can't change the permissions of a role they hold | 2026-10-29 |
| Q3 | **Delete kept**: allowed only when no user (active or inactive) has the role and no leave setting uses it; plus an Active switch (off refused while active users have the role) | 2026-10-29 |
| Q4 | Name: required, trimmed, **1–50**, letters, numbers, spaces and & - ( ) ' / . ,; unique per company among active and inactive roles. Description optional, up to 300 | 2026-10-29 |
| Q5 | Same API patterns as Department; legacy role / permission write endpoints refused | 2026-10-29 |

## Depends on

User Management → User List (role assignment, `UserRoleMapping`); every module's permission checks (`IPermissionService`).
