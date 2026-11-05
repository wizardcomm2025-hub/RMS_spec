# UI / UX — Role

> §12 recipe; badges centred, Actions right; desktop density (§7). Texts verbatim.

- `PageHeader`: "User Management › Role", title "Role", About (kept), ⟳, **＋ Add Role** (`create_roles`). Tab "Role - RMS".
- Filters: Search ("Search role...") on name and description · Status · Clear filters. Footer "Showing X of Y roles"; no pagination.
- Columns: Role Name (600; small "System" chip after it) · Description (`text.secondary`, one line, tooltip) · Users (centred) · Status chip (centred) · Actions (Edit · switch · Delete; right). System roles: Edit opens read-only, no switch, no Delete. Phone: cards.
- Dialog: keeps the existing role dialog and permission tree; titles "Add Role" / "Edit Role — <name>"; Name counter "n/50", Description counter "n/300"; read-only banner for a system role: "System roles can't be changed."; own role: tree read-only with "You can't change the permissions of your own role."; errors show the API message.
- Confirmations: deactivate / activate (use case 04); delete (use case 05, error-coloured confirm).
- States: skeleton, refresh bar, empty / no-match / load-error (use case 01).
- **Grant all / Revoke all saving:** the clicked button shows a small spinner and "Granting…" / "Revoking…"; both buttons are disabled; the panel's own "x of y enabled" bar turns into a moving (indeterminate) bar with the text "Granting all permissions…" / "Revoking all permissions…" and the percentage hidden; the tree is dimmed and locked until the call returns. No extra bar at the top of the panel.
