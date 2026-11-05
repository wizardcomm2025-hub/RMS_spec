# 04 — Switch a role on or off

## Summary

A user with `edit_roles` switches a role off; refused while active users have it, and for system roles.

## Main flow

1. Switch off → "Deactivate role": "Deactivate **<name>**? It can't be given to users any more. You can activate it again at any time." → "Role deactivated".
2. Switch on → "Activate **<name>**?" → "Role activated".

## Alternative flows

- "This role is assigned to active users. Move them to another role first." · "System roles can't be changed." · "Role not found" · "You don't have permission to perform this action.".

## Acceptance criteria

1. Role with no active users → Inactive; not offered when assigning roles in User List.
2. Role with an active user → refused.
3. System role → refused.
4. CO-B id → not found.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
