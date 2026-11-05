# 03 — Assign roles

## Summary

A user with `edit_userlist` gives a user one or more of the company's active roles (Q3). Refused for your own account (Q1) and when it would remove the last active admin (Q2) — closes D1.

## Main flow

1. Roles step: multi-select of active roles (system roles marked "System").
2. Save: the server checks permission, that the user and every role belong to the company and are active, the self rule and the last-admin rule; then replaces the user's active role list in one transaction and signs the user out so the new rights apply.
3. "User saved"; the list refreshes.

## Alternative flows

- "Pick at least one role" · "One or more roles are not available" · "You can't change your own roles or status." · "This is the last active admin. Give another user the Admin role first." · "User not found" · "You don't have permission to perform this action.".

## Acceptance criteria

1. A user without `edit_userlist` calling it → permission message; nothing changes (D1).
2. A CO-B role id or a switched-off role → refused.
3. Your own user id → self message.
4. Removing the system role from the last active admin → last-admin message.
5. Two roles saved → both listed and both apply after the user's next sign-in.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
