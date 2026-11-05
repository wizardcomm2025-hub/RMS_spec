# 02 — Add or edit a user

## Summary

A user with `create_userlist` adds a user (details, password, department, reporting heads, locations); with `edit_userlist` edits them. The server checks every field and that the department, reporting heads and locations belong to the company (D3).

## Main flow

1. "＋ Add User" opens the form; Edit loads the user (with an edit token).
2. Save details: the server validates the fields (Q5), checks user name and phone duplicates, checks department / reporting heads / locations, and saves for the token's company in one transaction (UTC). The password is set only on add.
3. The form moves to the Roles step ([03](./03-assign-roles.md)).

## Alternative flows

- Field messages as today's form (e.g. "First name is required", "Zipcode must be 6 digits").
- "User name already exists" · "Phone number already exists" — `status: false`, the form stays on Details (D5).
- "Department not found" · "Reporting head not found" · "One or more locations are not available".
- "User not found" (another company's / unknown id) · "This user was changed by someone else. Reload and try again." · "You don't have permission to perform this action.".

## Acceptance criteria

1. Valid add → user created Active in CO-A with the chosen locations.
2. Duplicate user name or phone → refusal, no "created" toast, no role call (D5).
3. A CO-B location / department / reporting head id → refused; nothing saved (D3).
4. Editing a CO-B user id → "User not found"; CO-B's locations unchanged.
5. Body `CompanyId`, `IsActive`, `IsCustomer`, `CreatedBy` ignored; status isn't changed by the save.
6. Weak or too-long password on add → the password message from the server too.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
