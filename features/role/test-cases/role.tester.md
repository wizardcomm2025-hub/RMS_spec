# Role — tester test cases

| ID | Covers | Steps | Expected result |
|---|---|---|---|
| `ROL-01-T01` | 01 | Open Role. | All CO-A roles except Support; R-ADMIN with a "System" chip, no switch, no Delete; Users counts shown. |
| `ROL-01-T02` | 01 | Search "mgr"; Status Inactive; Clear filters; offline → Retry. | Filters narrow; no-match state; load-error then reload. |
| `ROL-02-T01` | 02 | **＋ Add Role** "Cashier"; save. | "Role created"; listed Active. |
| `ROL-02-T02` | 02 | Add " r-mgr "-style duplicate; type 55 characters. | "Role name already exists"; field stops at 50. |
| `ROL-02-T03` | 02 | Edit R-ADMIN. | Read-only with "System roles can't be changed." |
| `ROL-03-T01` | 03 | Edit R-FREE; tick a permission. | "Permissions updated". |
| `ROL-03-T02` | 03 | As A-MGR edit R-MGR's permissions. | Tree read-only: "You can't change the permissions of your own role." |
| `ROL-04-T01` | 04 | Turn off R-FREE; turn off R-STAFF. | "Role deactivated"; then "This role is assigned to active users. Move them to another role first." |
| `ROL-05-T01` | 05 | Delete R-FREE; delete R-OLDUSER; delete R-LEAVE. | "Role deleted"; "This role is assigned to users. Move them to another role first."; "This role is used in leave settings. Remove it there first." |
| `ROL-05-T02` | UI | As A-STAFF open Role; at 375 px. | No Add / Edit / switch / Delete; phone cards. |
| `ROL-03-T03` | 03 | Edit R-FREE; click **Grant all**, reopen; click **Revoke all**, reopen. | While saving, the clicked button shows a spinner with "Granting…" / "Revoking…", the "x of y enabled" bar moves with "Granting all permissions…" / "Revoking all permissions…" and the tree is dimmed; then "Permissions updated"; after reopening all permissions are ticked, then none. For a system role or your own role both buttons are disabled. |
