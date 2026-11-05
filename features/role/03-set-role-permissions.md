# 03 — Set a role's permissions

## Summary

A user with `edit_roles` grants or removes permissions in the tree (section → sub-module → permission). Refused for system roles (Q1), for a role the user holds (Q2), and for another company's role (D2).

## Main flow

1. The dialog loads the tree for the role (`GetRolePermissionTree`, company-scoped).
2. Ticking a permission / sub-module / section sends `ToggleRolePermission` with the changed ids.
   **Grant all** / **Revoke all** save straight away the same way (one call with only the permissions that change); on a refusal the ticks go back.
3. The server checks permission, company, system role and own role, then merges `RolePermission`, syncs menu visibility, signs out the role's users and clears their permission cache (as today).
4. Toast "Permissions updated".

## Alternative flows

- "System roles can't be changed." · "You can't change the permissions of your own role." · "Role not found" (another company's / unknown role) · "One or more permissions are invalid." · "You don't have permission to perform this action.".
- A switched-off role can still have its permissions edited (they apply when it's switched back on).

## Acceptance criteria

1. A user without `edit_roles` calling Toggle → permission message; nothing changes (D1).
2. A user changing their own role → own-role message (Q2).
3. CO-A user toggling a CO-B role id → "Role not found"; CO-B unchanged (D2).
4. System role → system message.
5. Tree for a CO-B role id → "Role not found".
6. Granted change takes effect at the role users' next sign-in (unchanged behaviour).

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
