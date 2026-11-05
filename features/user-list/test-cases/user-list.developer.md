# User List — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_U_ADMIN`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `USR-03-D01` | **Privilege escalation (D1)** | `TOKEN_U_STAFF` | `AddEditAssignRole` `{ userId: U-STAFF, roleIdList: [R-ADMIN] }` | "You don't have permission to perform this action."; roles unchanged (today: granted) |
| `USR-03-D02` | Cross-company role (D1) | default | `AddEditAssignRole` `{ userId: U-STAFF, roleIdList: [R-B] }` | "One or more roles are not available" |
| `USR-03-D03` | Cross-company user | default | `AddEditAssignRole` `{ userId: U-B, roleIdList: [R-MGR] }` | "User not found" |
| `USR-03-D04` | Self / last admin (Q1, Q2) | default; `TOKEN_U_MGR` | U-ADMIN on self; U-MGR removing R-ADMIN from U-ADMIN | "You can't change your own roles or status."; last-admin message |
| `USR-03-D05` | Validation | default | empty list; inactive role R-OFF; malformed id | "Pick at least one role"; roles message; 400 without stack trace |
| `USR-04-D01` | Cross-company delete (D2) | default | `DeleteUser` `{ userId: U-B, companyId: <CO-B> }` | "Users cannot be deleted. Deactivate the user instead."; U-B unchanged |
| `USR-04-D02` | Status | default; `TOKEN_U_STAFF` | `SetUserActive` off U-STAFF; off self; off U-B; as U-STAFF | ok; self message; not found; permission message |
| `USR-02-D01` | Over-posting / company (D3) | default | `AddEditUser` edit of U-B; add with `locationIds: "<L-B>"`, `departmentId: D-B` | "User not found" and U-B's locations unchanged; location / department messages |
| `USR-02-D02` | Duplicates (D5) | default | add with an existing user name; phone | `status: false` with the exact message |
| `USR-02-D03` | Validation (D8) | default | first name "J0hn", zip "12", weak password, 13-char password | Field messages from the server |
| `USR-02-D04` | Concurrency | default | stale / missing `updatedAt` on edit | "This user was changed by someone else. Reload and try again." |
| `USR-02-D05` | Auth | no token; `TOKEN_SA` | every Users endpoint above | 401 — except `ValidateUserName`, which allows `TOKEN_SA` (company creation) and refuses no token |
| `USR-01-D01` | Password hash leak (D10) | default | `GetUserList`; `GetUserDtlByUserId`; `ViewProfileDetails` | No `password` value in any user object |
| `USR-02-D06` | Password leak (D7) | default | `ChangeUserPassword` own password | Response has no password field / value |
