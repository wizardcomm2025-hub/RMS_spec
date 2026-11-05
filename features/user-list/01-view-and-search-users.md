# 01 — View, search and filter users

## Summary

User Management → User List shows the company's users page by page (server paging, Q4): name, email, phone, roles, department, status.

## Main flow

1. Header "User Management › User List", title "User List", About; filters Search (name, email or phone, sent to the API after a short pause, from the first character) · Status (All / Active / Inactive) · Clear filters.
2. Columns: Name (+ small "You" chip on your own row) · Email · Phone · Roles (chips, centred) · Status (centred) · Actions (Edit, switch; right).
3. Footer "1–25 of N users" with page buttons and rows-per-page (10 / 25 / 50).

## Alternative flows

- Empty: "No users yet. Add the people who sign in to RMS." + Add (with `create_userlist`).
- No match: "No users match your filters." + Clear filters.
- Load error: "We couldn't load your users. Check your connection and try again." + Retry; toast "Failed to load users".

## Acceptance criteria

1. Only CO-A users; the page shows the API's page and total.
2. Search and status go to the API; page resets to 1 when they change.
3. Your own row shows "You" and no switch.
4. Empty / no-match / error states.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
