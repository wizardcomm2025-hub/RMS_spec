# Barcode Report — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `BRP-01-T01` | 01 | Open Report → Barcode Report as U1. | In stock tab, by item name: ONION, PANEER, SALT. Summary "3 items · 5 barcodes · remaining value ₹940.00". Tabs: In stock (5), Expiring soon (1), Expired (1), Used up (1). |
| `BRP-01-T02` | 01 | Look at ONION. | Remaining 8.5 kg · 3 barcodes · nearest expiry T−2 with *Expired* · ₹340.00. B4 isn't counted. |
| `BRP-01-T03` | 01 | Expand ONION. | B2 first, then B1, then B3, soonest expiry first. B1: 10 kg · 5 kg · ₹200.00 · *Expiring*. B2: 1000 g · 500 g · ₹20.00 · *Expired*. |
| `BRP-01-T04` | 01 | Read B3's Mfg date. | 2 January in the company format, not 1 February. |
| `BRP-01-T05` | 01 | Look at PANEER and SALT. | PANEER nearest expiry "—", ₹600.00. SALT Remaining "—", Value "—"; expanded: "1 box". |
| `BRP-01-T06` | 01 | Click Expiring soon. | ONION with B1 only; Remaining 5 kg; ₹200.00. |
| `BRP-01-T07` | 01 | Click Expired. | ONION with B2 only. |
| `BRP-01-T08` | 01 | Click Used up. | ONION with B5 only, *Used up*, Value ₹0.00. |
| `BRP-01-T09` | 01 | Location BR. | ONION with B3 only. |
| `BRP-01-T10` | 01 | Search "oni". | ONION with its three barcodes. |
| `BRP-01-T11` | 01 | Search B1's number. | ONION with B1 only. |
| `BRP-01-T12` | 01 | Search "zzz". | "No barcodes match these filters." + **Clear filters**; Clear filters restores the list. |
| `BRP-01-T13` | 01 | With no expired barcodes, click Expired. | "No expired barcodes." |
| `BRP-01-T14` | 01 | Export CSV on In stock. | One line per barcode: B1, B2, B3, B6, B7; values add up to the summary. |
| `BRP-01-T15` | 01 | Filter to nothing; Export CSV. | "Nothing to export for these filters." |
| `BRP-01-T16` | 01 | Sign in as U4. | "You aren't assigned to any location. Ask an admin to add you in User List." |
| `BRP-01-T17` | 01 | Sign in as U-STAFF; open the page. | "You don't have permission to perform this action." |
| `BRP-UI-T01` | UI | 360 px and 1280 px. | Cards with **Show barcodes**; desktop table with no sideways scroll. |
| `BRP-UI-T02` | UI | Stop the API; refresh. | "We couldn't load the report." + **Retry**. |
