# 02 — Add or edit a role

## Summary

A user with `create_roles` adds a role; with `edit_roles` changes its name and description. System roles can't be changed (Q1).

## Main flow

1. "＋ Add Role" opens the role dialog (Name, Description, then the permission tree once saved); Edit loads the role with its edit token.
2. Save: trimmed and validated; add → Active, non-system, for the token's company (UTC); edit → name and description only, company-scoped, edit token must match.
3. "Role created" / "Role updated".

## Alternative flows

- "Role name is required" · "Role name must be 50 characters or less" · "Role name can only contain letters, numbers, spaces and & - ( ) ' / . ," · "Role name already exists" · "Description must be 300 characters or less".
- "Role not found" · "This role was changed by someone else. Reload and try again." · "System roles can't be changed." · "You don't have permission to perform this action.".

## Acceptance criteria

1. Valid name → created Active, `IsSystemRole = 0`.
2. " store manager " while "Store Manager" exists (active or inactive) → duplicate.
3. 51-character name → length message.
4. Editing a system role (any field) through the API → "System roles can't be changed."
5. CO-B role id → not found; body `IsActive`, `IsSystemRole`, `CompanyId` ignored; stale token refused.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
