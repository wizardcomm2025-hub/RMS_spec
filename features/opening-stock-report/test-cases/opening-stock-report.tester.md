# Opening Stock Report — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `OSR-01-T01` | 01 | Range 02/11–04/11, location CK. | ONION opening 10, in +5, out −5.5 (3 + 2 + 0.5), count −0.5, closing 9 kg. |
| `OSR-01-T02` | 01 | Same range, all my locations; expand ONION. | CK as above; O1 opening 0, in +3, out −1, closing 2 kg; total closing 11 kg. |
| `OSR-01-T03` | 01 | Range 03/11–03/11, CK. | Opening 12 (10 + 5 − 3), in 0, out −2.5, closing 9.5 kg. |
| `OSR-01-T04` | 01 | Today only; compare with Stock Report. | Closing = Stock Report quantity for every item. |
| `OSR-01-T05` | 01 | From 20/10. | Notice "Stock history starts on 01/11/2026"; figures from 01/11. |
| `OSR-02-T01` | 02 | Movements on ONION (CK), 02/11–04/11. | Opening 10 · Purchase GRN/…/0012 +5 · TransferOut TRF/…/0004 −3 · Production −2 · Wastage −0.5 · Count −0.5 · Closing 9; balance after each line. |
| `OSR-01-T06` | 01 | Export CSV. | One line per item × location with the same figures. |
| `OSR-UI-T01` | UI | 360 px / 1280 px. | Phone cards; no sideways scroll on desktop; dialog full screen on phone. |
