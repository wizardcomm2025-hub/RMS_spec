# 01 — View, search and filter departments — tester test cases

- Use case: [`../01-view-and-search-departments.md`](../01-view-and-search-departments.md) · Screen: [`../ui-ux.md`](../ui-ux.md) · Fixtures: [`README.md`](./README.md#test-data)

All cases: log in as A-ADMIN on HOST-A and open Setting → Department, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `DEP-01-T01` | AC 1 | Fixtures loaded | Open the page; scroll to the end. | All 12 CO-A departments, active ones first in A–Z order, "Old Team" last. "Closed Team" and CO-B's "Sales" absent. |
| `DEP-01-T02` | AC 2 | — | Look at Old Team. | **Inactive** chip; the others **Active**. |
| `DEP-01-T03` | AC 3 | — | Search "kit", Status Active. Then Status Inactive. | First: Kitchen only. Then: "No departments match your filters." |
| `DEP-01-T04` | AC 4 | — | Search "zzz"; click **Clear filters**. | No-match message with Clear filters; after clicking, the full list. |
| `DEP-01-T05` | AC 5 | A company with no departments | Open the page there. | "No departments yet. Add your first department to group your staff." with Add. |
| `DEP-01-T06` | AC 6 | — | Offline; Refresh; online; **Retry**. | "We couldn't load your departments. Check your connection and try again." with Retry; toast "Failed to load departments"; Retry loads. |
| `DEP-01-T07` | AC 7 | — | Look at the footer. | "Showing 12 of 12 departments"; no page controls. |
| `DEP-01-T08` | AC 8 | — | Log in as A-VIEW. | No **＋ Add Department**, no Edit icon, no switch. |
| `DEP-01-T09` | AC 9 | — | Open Manage Steward → Roster; open its department filter. | All 12 CO-A departments are offered (before the fix only 10). |
| `DEP-01-T10` | UI AC 1 | — | View at 375, 768, 1280, 1920 px. | No horizontal page scroll; cards at 375; footer visible without scrolling the page. |
| `DEP-01-T11` | UI AC 4 | — | Keyboard only: Search, Edit, switch. | All reachable with visible focus; the switch announces "Active status for <name>". |
| `DEP-01-T12` | UI AC 5 | — | Look at the browser tab; open About and click "User". | "Department - RMS"; "User" opens User List. |
