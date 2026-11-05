# Production — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `PRD-01-T01` | 01 | Open Production as U1. | List of CK productions this month; O1 productions not shown. |
| `PRD-02-T01` | 02 | New production: GRAVY, CK, 10 kg. | ONION standard 4 kg, actual 4; OIL standard 1 L, actual 1; in stock 10 / 1; costs ₹160 / ₹150; final ₹310; ₹31.00/kg. |
| `PRD-02-T02` | 02 | OIL actual 1.2. | Red outline; "Only 1 L in stock". |
| `PRD-02-T03` | 02 | ONION actual 4.5; adjustment ₹−10 without reason; Save. | "Enter a reason for the adjustment on Onion". |
| `PRD-02-T04` | 02 | Reason "Discount"; overhead ₹50; Save; confirm. | "Production of 10 kg Gravy saved"; view page: ONION std 4 / actual 4.5 / variance +0.5; ingredients ₹330 (180 + 150), adjustments ₹−10, overhead ₹50, final ₹370, ₹37.00/kg; CK stock ONION 5.5 kg, OIL 0, GRAVY 10 kg. |
| `PRD-02-T05` | 02 | Date tomorrow. | "Production date can't be in the future". |
| `PRD-01-T02` | 01 | View the production; Print. | Read-only sheet; PDF with the same lines. |
| `PRD-UI-T01` | UI | List and new production at 360 px. | Phone cards; fields stack; ingredient pinned; no page sideways scroll. |
