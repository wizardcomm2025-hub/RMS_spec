# Stock Check Report — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `SCR-01-T01` | 01 | Open Report → Stock Check Report as U1. | Last 7 days, newest first. ONION today shows two rows: 12.5 kg at 06:30 PM and 10 kg at 10:00 AM. O2's count isn't listed. |
| `SCR-01-T02` | 01 | Look at the ONION 06:30 PM row. | Value ₹500.00, Counted by the user's name, Note "Rotten removed". |
| `SCR-01-T03` | 01 | Look at TOMATO and GARLIC. | TOMATO "0 kg" is listed; GARLIC isn't listed. |
| `SCR-01-T04` | 01 | Date range covering 01/Nov; Apply. | ONION 01/Nov shows 0.5 kg, value ₹20.00. |
| `SCR-01-T05` | 01 | Location O1, range covering BEANS' count. | BEANS listed although no longer mapped. |
| `SCR-01-T06` | 01 | Range covering SALT's count. | "… box", Value "—"; not in the total. |
| `SCR-01-T07` | 01 | Search "oni". | Only ONION rows; tab counts follow. |
| `SCR-01-T08` | 01 | Click the Finished Goods tab with no counts of that type. | "No counts match these filters." + **Clear filters**; Clear filters restores the list. |
| `SCR-01-T09` | 01 | Choose a range over one year; Apply. | "Choose a period of up to one year."; list unchanged. |
| `SCR-01-T10` | 01 | Export CSV. | File with every filtered row and the same columns; its values add up to the summary total. |
| `SCR-01-T11` | 01 | Filter to a period with no counts; Export CSV. | "Nothing to export for these filters." |
| `SCR-01-T12` | 01 | Sign in as a user with no location. | "You aren't assigned to any location. Ask an admin to add you in User List." |
| `SCR-01-T13` | 01 | Sign in as U-STAFF; open the page. | "You don't have permission to perform this action." |
| `SCR-UI-T01` | UI | 360 px and 1280 px. | Phone cards; desktop table with no sideways scroll. |
| `SCR-UI-T02` | UI | Stop the API; refresh. | "We couldn't load the report." + **Retry**. |
