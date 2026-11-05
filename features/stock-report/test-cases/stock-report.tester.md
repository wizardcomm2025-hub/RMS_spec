# Stock Report — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `SRP-01-T01` | 01 | Open Report → Stock Report as U1. | ONION 12.5 kg, value ₹500.00; TOMATO 0 kg with *Out*; O2's 7 kg not counted. |
| `SRP-01-T02` | 01 | Expand ONION. | CK 2.5 kg *Low* ₹100.00; O1 10 kg ₹400.00. |
| `SRP-01-T03` | 01 | Location CK. | ONION 2.5 kg *Low*; TOMATO 0 *Out*. |
| `SRP-01-T04` | 01 | Show: Low stock. | Only items with a *Low* location line. |
| `SRP-01-T05` | 01 | Search "oni". | Only ONION. |
| `SRP-01-T06` | 01 | Export CSV. | File with one line per item × location, same quantities and values; total matches the summary. |
| `SRP-UI-T01` | UI | 360 px and 1280 px. | Phone cards with location lines; desktop table without sideways scroll. |
