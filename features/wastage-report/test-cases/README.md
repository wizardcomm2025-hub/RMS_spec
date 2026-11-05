# Test cases — Wastage Report

Feature code `WRP`. Fixtures: CO-A, locations CK and BR (U1 mapped to both), O2 (U1 not mapped). ONION (raw, main kg, alt g, conversion 1000, cost ₹40/kg), PANEER (raw, main kg, cost ₹300/kg), SALT (raw, main kg, cost ₹20/kg). Reasons: Spoiled, Spilled.

| Entry | When | Location | Item | Quantity | Reason | By |
|---|---|---|---|---|---|---|
| E1 | today 06:30 PM | CK | ONION | 2.5 kg | Spoiled | U1 |
| E2 | today 10:00 AM | CK | ONION | 500 g (legacy row) | Spilled | U2 |
| E3 | yesterday | BR | PANEER | 1 kg | Spoiled | U1 |
| E4 | today | O2 | ONION | 7 kg | Spoiled | U3 |
| E5 | today | CK | ONION | 1 kg, **cancelled** | Spoiled | U1 |
| E6 | yesterday | CK | SALT | 1 box (unknown unit) | no reason (legacy) | U1 |

CO-B entries. U-STAFF without `view_wastage_report`. U4 with no location.

| Files | |
|---|---|
| [`wastage-report.tester.md`](./wastage-report.tester.md) | UI cases |
| [`wastage-report.developer.md`](./wastage-report.developer.md) | API cases |
