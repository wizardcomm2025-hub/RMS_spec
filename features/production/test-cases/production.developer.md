# Production — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_U1`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `PRD-02-D01` | First production (P1) | default | GRAVY 10 at CK (no GRAVY stock row) | Row created: 10 kg |
| `PRD-02-D02` | Short (Q2, P3) | default | OIL actual 2 (stock 1) | "Not enough Oil at …: 1 L in stock, 2 L needed"; nothing saved |
| `PRD-02-D03` | Race (P4) | default | two parallel productions each using 6 kg ONION (stock 10) | One ok, one short; stock never negative |
| `PRD-02-D04` | Units (P4) | default | ONION stock row kept in g (legacy) | Reduced in g by actual × 1000; row unit unchanged |
| `PRD-02-D05` | Cost (Q3, P5) | default | over-posted `TotalStockCost` 1, `FinalProductionCost` 1 | Ignored; server totals saved |
| `PRD-02-D06` | Company / location (P6) | default | RB; location O1; extra ingredient; missing ingredient | "Select an item that has a recipe" / "Select one of your locations" / "Recipe has changed…" |
| `PRD-02-D07` | Date (P7) | default | `productionDate` today at 23:30 IST | Saved with today's date |
| `PRD-02-D08` | Old endpoint (D8) | default | `AddEditProduction` new; with `ProductionId` 5 | Saved via the new procedure (actual = standard); "Productions can't be changed after saving." |
| `PRD-ALL-D01` | Auth | none / expired / tampered / SuperAdmin | each endpoint | 401 |
| `PRD-ALL-D02` | Permission | U-STAFF | each endpoint | "You don't have permission to perform this action." |
| `PRD-ALL-D03` | Garbage | default | qty 0 / −1 / 1e12; overhead −1; actual −1; 1001 lines | Messages; nothing saved |
