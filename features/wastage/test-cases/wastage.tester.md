# Wastage — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `WST-01-T01` | 01 | Open Stock → Wastage as U1. | L1; items in stock; Wastage fields blank; ONION in stock 10 kg. |
| `WST-01-T02` | 01 | Scan B1. | Sheet filtered to Onion; chip "Barcode … · 4 kg left". |
| `WST-01-T03` | 01 | ONION wastage 12 kg. | After "−2 kg" red, "Only 10 kg in stock"; Save disabled. |
| `WST-02-T01` | 02 | ONION 500 g, reason Spoiled; OIL 1 L, reason Spoiled; Save wastage (2), confirm. | "2 wastage entries saved"; ONION 9.5 kg, OIL 4 L; lines cleared; both in Recorded today. |
| `WST-02-T02` | 02 | ONION 1 kg without reason; row ✓. | Reason field red "Select a reason". |
| `WST-02-T03` | 02 | After WST-02-T01, press Save again. | Button disabled (nothing entered). |
| `WST-02-T04` | 02 | Scan B1, ONION 1 kg, Spoiled, save. | B1 shows 3 kg left on next scan. |
| `WST-03-T01` | 03 | Cancel the ONION entry. | Confirm; "Wastage entry cancelled"; ONION back to 10 kg; entry gone. |
| `WST-03-T02` | 03 | Open the Wastage Report for today. | Cancelled entry not counted. |
| `WST-UI-T01` | UI | Page at 360 px. | Phone cards; no page sideways scroll. |
