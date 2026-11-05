# 05 — Delete a role

## Summary

A user with `delete_roles` deletes a role that no user (active or inactive) has and no leave setting uses (Q3). It removes the role, its permissions, its menu visibility rows and its dashboard widgets.

## Main flow

1. Delete → confirm "Delete role": "Delete **<name>**?" — Cancel · **Delete** (shared delete confirmation).
2. Checks, then deletes `RolePermission`, `RoleMenuPermission`, `DashboardWidgetCommon`, legacy `PermissionList` rows and the `Role` row, in one transaction.
3. "Role deleted"; the list refreshes.

## Alternative flows

- "This role is assigned to users. Move them to another role first." · "This role is used in leave settings. Remove it there first." · "System roles can't be changed." · "Role not found" · "You don't have permission to perform this action.".

## Acceptance criteria

1. Unused role → gone from the list and the database.
2. Role mapped to an inactive user only → refused (D4).
3. Role in a leave setting → refused.
4. System role → refused; CO-B id → not found.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
