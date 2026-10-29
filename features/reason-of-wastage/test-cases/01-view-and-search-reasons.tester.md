# 01 — View, search and filter reasons — tester test cases

- Use case: [`../01-view-and-search-reasons.md`](../01-view-and-search-reasons.md) · Screen: [`../ui-ux.md`](../ui-ux.md) · Fixtures: [`README.md`](./README.md#test-data)

All cases: A-ADMIN on HOST-A, Setting → Reason of Wastage, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `RSN-01-T01` | AC 1 | — | Open the page; read the list. | 12 reasons: the 11 active A–Z, then Old Reason. No "Theft", no "Leak". |
| `RSN-01-T02` | AC 2 | — | Find Old Reason. | Chip **Inactive**; others **Active**. |
| `RSN-01-T03` | AC 3 | — | Type "e"; then "exp"; set Status **Active**; then **Inactive**; click **Clear filters**. | Narrows from the first letter; "exp" + Active → Expired only; "exp" + Inactive → no-match; Clear filters → all 12. |
| `RSN-01-T04` | AC 4 | — | Search "zzzz". | "No reasons match your filters." with **Clear filters**. |
| `RSN-01-T05` | AC 5 | A company with no reasons | Open the page as its admin. | "No reasons yet. Add the reasons staff pick when they write off stock." with **＋ Add Reason**. |
| `RSN-01-T06` | AC 6 | — | Go offline; click ⟳ Refresh (or reload). Go online; click **Retry**. | "We couldn't load your reasons. Check your connection and try again." and toast "Failed to load reasons"; Retry loads the list. |
| `RSN-01-T07` | AC 7 | — | Scroll to the bottom. | No page numbers; "Showing 12 of 12 reasons"; with search "exp": "Showing 1 of 12 reasons". |
| `RSN-01-T08` | AC 8 | — | Log in as A-VIEW; open the page. | No **＋ Add Reason**; no Edit icons, no switches. |
| `RSN-01-T09` | AC 9 | — | Open Stock → Wastage; add an item; open the Reason dropdown. Type "chick" in the item search; open the dropdown again. | Only the 11 active reasons, no Old Reason; the list is the same after typing in the item search. |
| `RSN-01-T10` | AC 10 | A 300-character reason exists | View at 1280 px and 375 px. | Text wraps; no horizontal page scroll; phone shows cards. |
| `RSN-01-T11` | UI AC | — | At 375 / 768 / 1280 / 1920 px check header, filters, list; open About. | Layout per ui-ux §7; About text exactly as in ui-ux §1 with links "Wastage" and "Wastage Report" working. |
