# Role — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `ROL-03-D01` | **Privilege escalation (D1)** | `TOKEN_A_STAFF` | `ToggleRolePermission` `{ roleId: R-STAFF, permissions: [{ permissionId: <edit_roles>, grant: true }] }` | "You don't have permission to perform this action."; nothing changes (today granted) |
| `ROL-03-D02` | **Cross-company (D2)** | default | Toggle on R-B | "Role not found"; CO-B unchanged (today changed) |
| `ROL-03-D03` | Own role (Q2) | `TOKEN_A_MGR` | Toggle on R-MGR | "You can't change the permissions of your own role." |
| `ROL-03-D04` | System role (Q1) | default | Toggle on R-ADMIN | "System roles can't be changed." |
| `ROL-03-D05` | Validation | default | Unknown permission id; malformed list | "One or more permissions are invalid."; no stack trace |
| `ROL-03-D06` | Cross-company read | default | `GetRolePermissionTree?roleId=R-B` | "Role not found" |
| `ROL-02-D01` | Auth / SuperAdmin | no token; `TOKEN_SA` | `GetRoleList`, `SaveRole`, Toggle, Delete | 401 each |
| `ROL-02-D02` | Main flow | default | `SaveRole` empty id "Cashier" | `status: true`, `data.roleId`; Active, not system, CO-A |
| `ROL-02-D03` | Validation | default | name missing / 51 chars / "x;drop"; description 301 chars | Exact messages |
| `ROL-02-D04` | Business rule (D6) | default | " R-MGR " (any case); name of an inactive role | "Role name already exists" |
| `ROL-02-D05` | System / isolation / over-posting | default | Save R-ADMIN; save R-B id; add with `isSystemRole: true, isActive: false, companyId: CO-B` | System message; not found; saved non-system, Active, CO-A |
| `ROL-02-D06` | Concurrency / permission | default; `TOKEN_A_STAFF` | stale `updatedAt`; missing; A-STAFF save | Stale ×2; permission message |
| `ROL-04-D01` | Status | default | `ChangeRoleStatus` off R-FREE; off R-STAFF; off R-ADMIN; R-B | ok; active-users message; system message; not found |
| `ROL-05-D01` | Delete (D4) | default | `DeleteRole` R-FREE; R-OLDUSER; R-LEAVE; R-ADMIN; R-B | Deleted (rows gone from `Role`, `RolePermission`, `RoleMenuPermission`); users message; leave message; system message; not found |
| `ROL-05-D02` | Legacy (D5) | default | `AddEditRole`, `SaveMenuMenuActionList`, `SavePermission` | "This endpoint is no longer available." |
| `ROL-05-D03` | Legacy read (D5) | default | `GetRoleDDl` `{ companyId: CO-B }` | CO-A roles only |
