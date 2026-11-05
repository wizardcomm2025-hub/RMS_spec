# User List — tester test cases

| ID | Covers | Steps | Expected result |
|---|---|---|---|
| `USR-01-T01` | 01 | Open User List; change rows per page to 10; go to page 2. | Footer "1–10 of N users" then "11–20…"; only CO-A users; your row shows "You" and no switch. |
| `USR-01-T02` | 01 | Search "mgr"; Status Inactive; Clear filters; offline → Retry. | Results from the API; page back to 1; no-match and load-error states. |
| `USR-02-T01` | 02 | Add a user with all required fields and two locations. | "User created"; form moves to Roles. |
| `USR-02-T02` | 02 | Add a user with an existing user name; then an existing phone. | "User name already exists" / "Phone number already exists"; stays on Details; no "created" toast. |
| `USR-03-T01` | 03 | Roles step: pick R-MGR and R-STAFF; save. | "Roles saved"; list shows both role chips. |
| `USR-03-T02` | 03 | Edit your own user (U-ADMIN). | Roles read-only with "You can't change your own roles or status." |
| `USR-03-T03` | 03 | As U-MGR edit U-ADMIN; remove R-ADMIN; save. | "This is the last active admin. Give another user the Admin role first." |
| `USR-04-T01` | 04 | Turn off U-STAFF → Deactivate; try to sign in as U-STAFF. | "User deactivated"; sign-in refused. |
| `USR-04-T02` | UI | As U-STAFF open User List; at 375 px. | No Add / Edit / switch; phone cards. |
