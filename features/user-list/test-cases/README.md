# Test cases — User List

Feature code `USR`. Fixtures: CO-A / CO-B, tokens as in Setting → Location. CO-A users: U-ADMIN (system Admin role, `create_userlist` + `edit_userlist`) — the **only** active admin; U-MGR (role R-MGR, `edit_userlist`); U-STAFF (R-STAFF, no user permissions); U-OLD (inactive). CO-A roles: R-ADMIN (system), R-MGR, R-STAFF, R-OFF (inactive). CO-B: U-B, R-B, location L-B, department D-B.

| Files | |
|---|---|
| [`user-list.tester.md`](./user-list.tester.md) | UI cases |
| [`user-list.developer.md`](./user-list.developer.md) | API cases (14) |
