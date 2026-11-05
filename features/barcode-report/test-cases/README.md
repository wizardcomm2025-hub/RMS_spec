# Test cases — Barcode Report

Feature code `BRP`. T = today. Fixtures: CO-A, locations CK and BR (U1 mapped to both), O2 (U1 not mapped). ONION (raw, main kg, alt g, conversion 1000, cost ₹40/kg), PANEER (raw, main kg, cost ₹300/kg), SALT (raw, main kg, cost ₹20/kg).

| Barcode | Location | Item | Initial | Remaining | Mfg | Expiry | Status |
|---|---|---|---|---|---|---|---|
| B1 | CK | ONION | 10 kg | 5 kg | 01/Jun | T+10 | Expiring |
| B2 | CK | ONION | 1000 g | 500 g | 20/Oct | T−2 | Expired |
| B3 | BR | ONION | 5 kg | 3 kg | 02/Jan | T+60 | — |
| B4 | O2 | ONION | 7 kg | 7 kg | — | T+60 | not visible to U1 |
| B5 | CK | ONION | 4 kg | 0 kg | — | T−30 | Used up |
| B6 | CK | PANEER | 2 kg | 2 kg | — | none | — |
| B7 | CK | SALT | 1 box | 1 box | — | none | — (unknown unit) |

CO-B barcodes. U-STAFF without `view_barcode_report`. U4 with no location.

| Files | |
|---|---|
| [`barcode-report.tester.md`](./barcode-report.tester.md) | UI cases |
| [`barcode-report.developer.md`](./barcode-report.developer.md) | API cases |
