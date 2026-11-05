# 01 — View, search and filter roles

## Summary

User Management → Role lists every role of the company (the hidden `Support` role excluded), active first then by name, with description, number of users holding it (active and inactive), and a "System" badge for system roles.

## Main flow

1. Header "User Management › Role", title "Role", About; filters Search (name or description) · Status · Clear filters.
2. Columns: Role Name (+ "System" badge) · Description (secondary text, truncated with tooltip) · Users (users holding the role, active and inactive, centred) · Status (centred) · Actions (Edit, switch, Delete; right).
3. Footer "Showing X of Y roles"; no pagination (the API returns all).

## Alternative flows

- Empty: "No roles yet. Add a role to give staff access to the right screens." + Add (with `create_roles`).
- No match: "No roles match your filters." + Clear filters.
- Load error: "We couldn't load your roles. Check your connection and try again." + Retry; toast "Failed to load roles".

## Acceptance criteria

1. All CO-A roles except Support, none of CO-B's; system roles show the badge.
2. Users column = every user holding the role, active and inactive (the same users that block a delete).
3. Search / Status / Clear filters work from the first character.
4. A system role has no switch and no Delete; Edit opens it read-only.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
