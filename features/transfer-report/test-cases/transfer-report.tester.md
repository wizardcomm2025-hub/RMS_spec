# Transfer Report — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `TRP-01-T01` | 01 | Open Report → Transfer Report as U1. | Last 7 days, newest first: TRF-1, TRF-2, TRF-4. TRF-3 isn't listed. |
| `TRP-01-T02` | 01 | Look at TRF-1. | REQ-9 beneath the number; CK → BR; Sent by U1; Received by U2; *Received* and *Short* chips; Sent ₹500.00; Short ₹80.00. |
| `TRP-01-T03` | 01 | Expand TRF-1. | ONION 10 kg · 8 kg · 2 kg · ₹400.00 · ₹80.00 · "Spilled"; TOMATO 2 kg · 2 kg · — · ₹100.00 · —. |
| `TRP-01-T04` | 01 | Look at TRF-2 and expand it. | Received by "—", *In transit*, Short "—". ONION 500 g · — · —, Sent value ₹20.00. |
| `TRP-01-T05` | 01 | Expand TRF-4. | SALT 1 box · 3 box · "2 box over"; values "—". |
| `TRP-01-T06` | 01 | Click With shortage. | Only TRF-1; tab counts stay correct. |
| `TRP-01-T07` | 01 | Click In transit. | Only TRF-2; summary short ₹0.00. |
| `TRP-01-T08` | 01 | Location BR. | TRF-1 and TRF-4 (either end). |
| `TRP-01-T09` | 01 | Search "tomato". | TRF-1 with both its lines. |
| `TRP-01-T10` | 01 | Search "REQ-9". | TRF-1. |
| `TRP-01-T11` | 01 | Choose a range over one year; Apply. | "Choose a period of up to one year."; list unchanged. |
| `TRP-01-T12` | 01 | Filter to a period with nothing. | "No transfers match these filters." + **Clear filters**; Clear filters restores the list. |
| `TRP-01-T13` | 01 | Export CSV. | One line per item of the filtered transfers; values add up to the summary. |
| `TRP-01-T14` | 01 | Filter to nothing; Export CSV. | "Nothing to export for these filters." |
| `TRP-01-T15` | 01 | Sign in as U4. | "You aren't assigned to any location. Ask an admin to add you in User List." |
| `TRP-01-T16` | 01 | Sign in as U-STAFF; open the page. | "You don't have permission to perform this action." |
| `TRP-UI-T01` | UI | 360 px and 1280 px. | Phone cards with **Show items**; desktop table with no sideways scroll. |
| `TRP-UI-T02` | UI | Stop the API; refresh. | "We couldn't load the report." + **Retry**. |
