# 04 — Switch a user on or off

## Summary

A user with `edit_userlist` switches a user off (they can't sign in) or back on. Not your own account (Q1); not the last active admin (Q2). Users are never deleted (D2).

## Main flow

1. Switch off → "Deactivate user": "Deactivate **<name>**? They won't be able to sign in. You can activate them again at any time." → "User deactivated".
2. Switch on → "Activate **<name>**?" → "User activated".

## Alternative flows

- "You can't change your own roles or status." · "This is the last active admin. Give another user the Admin role first." · "User not found" · "You don't have permission to perform this action.".
- A direct `DeleteUser` call → "Users cannot be deleted. Deactivate the user instead.".

## Acceptance criteria

1. Another active user → Inactive, signed out, can't sign in.
2. Yourself → refused. 3. Last active admin → refused.
4. CO-B id (any body `CompanyId`) → not found (D2). 5. Delete refused.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
