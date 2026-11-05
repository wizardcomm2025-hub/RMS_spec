# Test cases — Opening Stock Report (stock movement)

Feature code `OSR`. Fixtures: CO-A, CK and O1 (U1 mapped to both), O2 (not mapped). Ledger switched on 01/11/2026 with ONION CK 10 kg (main kg, alt g, conversion 1000, ₹40/kg). Then: 02/11 PO receive ONION CK +5 kg (GRN/…/0012); 02/11 dispatch CK → O1 3 kg (TRF/…/0004); 03/11 O1 receives 3 kg; 03/11 production at CK uses 2 kg; 03/11 wastage CK 0.5 kg; 04/11 Stock Check CK counts 9 kg (system 9.5 → −0.5); 04/11 POS sale O1 −1 kg; 04/11 old screen sets O2 ONION. U-STAFF without the permission.

| Files | |
|---|---|
| [`opening-stock-report.tester.md`](./opening-stock-report.tester.md) | UI cases |
| [`opening-stock-report.developer.md`](./opening-stock-report.developer.md) | API / DB cases |
