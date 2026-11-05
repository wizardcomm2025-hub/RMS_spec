# Wastage Report — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `WRP-01-T01` | 01 | Open Report → Wastage Report as U1. | Entries view, last 7 days, newest first: E1, E2, E3, E6. E4 and E5 aren't listed. Summary "4 entries · wastage value ₹420.00". |
| `WRP-01-T02` | 01 | Look at E1. | 2.5 kg · Spoiled · ₹100.00 · U1's name, with date and time. |
| `WRP-01-T03` | 01 | Look at E2. | Its own row, not a copy of E1: 0.5 kg · Spilled · ₹20.00. |
| `WRP-01-T04` | 01 | Look at E6. | "1 box", "No reason", Value "—". |
| `WRP-01-T05` | 01 | Click By item. | PANEER 1 kg · 1 · ₹300.00 · 71%; ONION 3 kg · 2 · ₹120.00 · 29%; SALT quantity "—" · 1 · "—". Summary unchanged. |
| `WRP-01-T06` | 01 | Click By reason. | Spoiled 2 · 2 · ₹400.00 · 95%; Spilled 1 · 1 · ₹20.00 · 5%; No reason 1 · 1 · "—". |
| `WRP-01-T07` | 01 | Refresh the page. | Still By reason. |
| `WRP-01-T08` | 01 | Location CK. | E1, E2, E6 only. |
| `WRP-01-T09` | 01 | Reason Spilled. | E2 only. |
| `WRP-01-T10` | 01 | Search "oni". | E1 and E2; tab counts follow. |
| `WRP-01-T11` | 01 | Choose a range over one year; Apply. | "Choose a period of up to one year."; list unchanged. |
| `WRP-01-T12` | 01 | Filter to a period with nothing. | "No wastage matches these filters." + **Clear filters**; Clear filters restores the list and keeps the view. |
| `WRP-01-T13` | 01 | Export CSV in each view. | A file of the current view with every filtered row; values add up to the summary. |
| `WRP-01-T14` | 01 | Filter to nothing; Export CSV. | "Nothing to export for these filters." |
| `WRP-01-T15` | 01 | Cancel E1 today in Stock → Wastage; refresh. | E1 is gone and the total drops by ₹100.00. |
| `WRP-01-T16` | 01 | Sign in as U4. | "You aren't assigned to any location. Ask an admin to add you in User List." |
| `WRP-01-T17` | 01 | Sign in as U-STAFF; open the page. | "You don't have permission to perform this action." |
| `WRP-UI-T01` | UI | 360 px and 1280 px, all three views. | Cards on the phone; desktop tables with no sideways scroll. |
| `WRP-UI-T02` | UI | Stop the API; refresh. | "We couldn't load the report." + **Retry**. |
