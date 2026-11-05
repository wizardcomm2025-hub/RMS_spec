# Test cases — Transfer Report

Feature code `TRP`. Fixtures: CO-A, locations CK and BR (U1 mapped to both), ST (U1 not mapped), X1 and X2 (U1 mapped to neither). ONION (main kg, alt g, conversion 1000, cost ₹40/kg). TOMATO (main kg, cost ₹50/kg).

| Transfer | Date | From → To | Status | Lines |
|---|---|---|---|---|
| TRF-1 (REQ-9) | today | CK → BR, sent by U1 | Received by U2 | ONION 10 kg sent, 8 kg received, reason "Spilled"; TOMATO 2 kg sent, 2 kg received |
| TRF-2 | today | ST → CK, sent by U3 | In transit | ONION 500 g sent |
| TRF-3 | today | X1 → X2 | Received | not visible to U1 |
| TRF-4 | yesterday | BR → CK | Received, legacy | SALT 1 box sent, 3 box received (unknown unit, over-receipt) |

CO-B transfers. U-STAFF without `view_transfer_report`. U4 with no location.

| Files | |
|---|---|
| [`transfer-report.tester.md`](./transfer-report.tester.md) | UI cases |
| [`transfer-report.developer.md`](./transfer-report.developer.md) | API cases |
