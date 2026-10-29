# 01 — View, search and filter — tester test cases

- Use case: [`../01-view-and-search-menu-sections.md`](../01-view-and-search-menu-sections.md) · Screen: [`../ui-ux.md`](../ui-ux.md) · Fixtures: [`README.md`](./README.md#test-data)

All cases: A-ADMIN on HOST-A, Inventory → Menu Section, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `MSE-01-T01` | AC 1 | — | Open the page. | 12 sections: the 11 active A–Z, then Old Specials. No "Main Course" from CO-B. |
| `MSE-01-T02` | AC 2 | — | Look at Old Specials. | Chip **Inactive**, centred in the Status column; Edit and switch at the right. |
| `MSE-01-T03` | AC 3 | — | Type "s", then "star"; set Status Active, then Inactive; **Clear filters**. | Narrows from the first letter; "star" + Active → Starters; + Inactive → no-match; Clear → all 12. |
| `MSE-01-T04` | AC 4 | — | Search "zzzz". | "No menu sections match your filters." with **Clear filters**. |
| `MSE-01-T05` | AC 4 | A company with no sections | Open the page. | "No menu sections yet. Add the groups cashiers see on the POS menu." with **＋ Add Menu Section**. |
| `MSE-01-T06` | AC 4 | — | Go offline; Refresh; go online; **Retry**. | "We couldn't load your menu sections. Check your connection and try again.", toast "Failed to load menu sections"; Retry loads. |
| `MSE-01-T07` | AC 5 | — | Scroll to the bottom. | No page numbers; "Showing 12 of 12 menu sections". |
| `MSE-01-T08` | AC 6, UI AC | — | As A-VIEW open the page; as A-ADMIN check 375 / 768 / 1280 / 1920 px and the tab title. | A-VIEW: no Add, no Edit, no switch. Layout per ui-ux §7; tab "Menu Section - RMS". |
