# Test cases — Role

Feature code `ROL`. Fixtures: CO-A / CO-B, tokens as in Setting → Location. A-ADMIN has `create_roles`, `edit_roles`, `delete_roles` and holds role R-ADMIN (system); A-MGR holds role R-MGR and has `edit_roles`; A-STAFF holds R-STAFF and has no role permissions. CO-A roles: R-ADMIN (system), R-MGR, R-STAFF (active users), R-OLDUSER (only an inactive user), R-LEAVE (used in a leave setting, no users), R-FREE (unused). CO-B: R-B.

| Files | |
|---|---|
| [`role.tester.md`](./role.tester.md) | UI cases |
| [`role.developer.md`](./role.developer.md) | API cases |
