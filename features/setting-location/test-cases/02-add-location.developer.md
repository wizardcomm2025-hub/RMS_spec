# 02 — Add a location — developer test cases

- Use case: [`../02-add-location.md`](../02-add-location.md)
- API: [`../api-contract.md`](../api-contract.md#save-location) · response shapes: [`project-overview.md` → API conventions](../../../project-overview.md#api-conventions)
- Data model: [`../data-model.md`](../data-model.md)
- Fixtures and tokens: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Save" = `POST /Setting/SaveWareHouseDetails`. **Valid body** =

```json
{ "WarehouseId": 0, "WarehouseName": "Dev Test", "WarehouseType": "Outlet", "IsDefault": false,
  "AddressLine1": "", "AddressLine": "", "City": "", "StateName": "", "Zip": "",
  "EmployeeDetails": [], "BinNumbers": [], "CanTakeRequisition": false, "IsPOSOutlet": true }
```

All messages are decided (LOC-Q1 – Q11, 2026-10-28); see [`api-contract.md`](../api-contract.md#messages).

## Test cases

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `LOC-02-D01` | Authentication | no token · HOST-A | Save, valid body | 401 `{ "message": "Unauthorized" }`; no row "Dev Test" |
| `LOC-02-D02` | Authentication | `TOKEN_EXPIRED`, then `TOKEN_TAMPERED` · HOST-A | Save, valid body | 401 each; nothing written |
| `LOC-02-D03` | Company isolation | `TOKEN_A_ADMIN` · **HOST-B** | Save, valid body | 401 "Token does not match this site's company."; no row in CO-A or CO-B |
| `LOC-02-D04` | Over-posting / isolation | `TOKEN_A_ADMIN` · HOST-A | Valid body + `"CompanyId": <CO-B id>` | 200 `status: true`. The new row has `CompanyId` = CO-A; CO-B has no "Dev Test" |
| `LOC-02-D05` | Over-posting | `TOKEN_A_ADMIN` · HOST-A | Valid body + `"UserId"`, `"CreatedBy"`, `"UserResponsible"` = B-ADMIN's id, `"IsActive": false`, `"IsDeleted": true` | 200 `status: true`. Row: `CreatedBy` and `UserResponsible` = A-ADMIN, `IsActive = 1`, `IsDeleted = 0` |
| `LOC-02-D06` | Rule bypass (POS) | `TOKEN_A_ADMIN` · HOST-A | Valid body with `"IsPOSOutlet": false` | New row `IsPOSOutlet = 1`. LOC-A2's `IsPOSOutlet` is still 1, and every other location's POS flag is unchanged (compare before / after) — D1 |
| `LOC-02-D07` | Rule bypass (bins) | `TOKEN_A_ADMIN` · HOST-A | Valid body with `"BinNumbers": [{ "BinNumberId": 0, "BinNumber": "X1" }]` | No `BinWarehouse` row is created or changed, for the new location or any other (row count and values identical) — Q3, D4 |
| `LOC-02-D08` | Validation | `TOKEN_A_ADMIN` · HOST-A | Body without `WarehouseName`; then `"WarehouseName": null`; then `""` | Rejected each time: `status: false`, "Location Name is required" (or a 400 model-validation response with no stack trace); nothing written |
| `LOC-02-D09` | Validation | `TOKEN_A_ADMIN` · HOST-A | `"WarehouseName": "     "`, then `"\t\n"` | `status: false`, "Location Name is required"; nothing written |
| `LOC-02-D10` | Validation (boundary) | `TOKEN_A_ADMIN` · HOST-A | Name of exactly 100 characters; then 101; then 100 characters plus a leading and trailing space | 100: saved. 101: `status: false`, "Location Name must be 100 characters or less", nothing written. 100 + spaces: saved, stored trimmed (100 characters) |
| `LOC-02-D11` | Validation | `TOKEN_A_ADMIN` · HOST-A | Name of 10 000 characters; then a 1 MB JSON body | Rejected; no 500 page, no SQL truncation error text in the message; nothing written |
| `LOC-02-D12` | Validation (duplicate) | `TOKEN_A_ADMIN` · HOST-A | Name `"  KITCHEN "` | `status: false`, "Location name already exists"; nothing written — AC 4 |
| `LOC-02-D13` | Validation (duplicate) | `TOKEN_A_ADMIN` · HOST-A | Name "Old Store" (LOC-A3, inactive) | `status: false`, "Location name already exists"; nothing written |
| `LOC-02-D14` | Validation | `TOKEN_A_ADMIN` · HOST-A | `Zip`: "12345", "12a456", "1234567", " 560001", "-56000" | Each rejected: `status: false`, "Pincode must be 6 digits"; nothing written. `Zip` "" and "560001": saved |
| `LOC-02-D15` | Validation | `TOKEN_A_ADMIN` · HOST-A | `WarehouseType`: "NotAType"; then "Commissary" (CO-B's type) | `status: false`, "Location Type is required" each time; nothing written (LOC-Q10) |
| `LOC-02-D16` | Validation | `TOKEN_A_ADMIN` · HOST-A | `WarehouseType` missing, null, "" | `status: false`, "Location Type is required" (or 400 with no stack trace for missing); nothing written |
| `LOC-02-D17` | Company isolation | `TOKEN_A_ADMIN` · HOST-A | `EmployeeDetails: [{ "SelectedUserId": "<EMP-B1 id>" }]`; then EMP-AX (inactive); then EMP-A1 + EMP-B1 together | `status: false`, "One or more employees are not valid." each time; no location created; no `UserWarehouseMapping` row for EMP-B1 or EMP-AX (LOC-Q6) |
| `LOC-02-D18` | Validation | `TOKEN_A_ADMIN` · HOST-A | `SelectedUserId`: random new GUID; then "abc"; then "'; DELETE FROM UserWarehouseMapping;--" | GUID: `status: false`, "One or more employees are not valid.". The others: the same message or a 400 with no stack trace. No 500; nothing written; `UserWarehouseMapping` row count unchanged |
| `LOC-02-D19` | Validation | `TOKEN_A_ADMIN` · HOST-A | `"WarehouseId": "abc"`; `"IsDefault": "yes"`; `"EmployeeDetails": "x"`; malformed JSON `{ "WarehouseName": ` | 400 or `status: false`; no stack trace or type names in the body; nothing written |
| `LOC-02-D20` | Validation | `TOKEN_A_ADMIN` · HOST-A | `"WarehouseId": -1`; then `9223372036854775807` (with `UpdatedAt: null`) | `status: false`, "Location not found"; no row created or changed (LOC-Q9) |
| `LOC-02-D21` | Injection | `TOKEN_A_ADMIN` · HOST-A | Name `x'); DROP TABLE Warehouse;--` | Saved as that literal text; `dbo.Warehouse` intact; list call returns the name unchanged |
| `LOC-02-D22` | Injection | `TOKEN_A_ADMIN` · HOST-A | Name `<script>alert(1)</script>`, City `<img src=x onerror=alert(1)>` | Stored and returned unchanged as text; opening the list in the UI runs no script |
| `LOC-02-D23` | Encoding | `TOKEN_A_ADMIN` · HOST-A | Name `कैफ़े ☕ Ünïcode` | Returned by the list call exactly as sent (no `?` characters) |
| `LOC-02-D24` | Concurrency | `TOKEN_A_ADMIN` · HOST-A | Send the same valid body ("Race Test") twice in parallel (Postman runner with 2 parallel iterations, or two terminals) | Exactly one "Race Test" row in CO-A; the other call returns `status: false`, "Location name already exists" |
| `LOC-02-D25` | Permission | `TOKEN_A_VIEW` · HOST-A | Save, valid body | `status: false`, "You don't have permission to perform this action."; nothing written (LOC-Q1). Today the save succeeds |
| `LOC-02-D26` | Error hygiene | `TOKEN_A_ADMIN` · HOST-A | Valid body while `Sp_SaveWareHouseDetails` fails (dev DB: rename temporarily) | `status: false`, message exactly "Something went wrong. Please try again."; no SQL, procedure name or stack trace (LOC-Q2) |
| `LOC-02-D27` | Authentication | no token · HOST-A | `POST /Users/ValidateLocName` `{ "LocName": "Kitchen", "locationId": null }` | 401 `{ "message": "Unauthorized" }` |
| `LOC-02-D28` | Company isolation | `TOKEN_A_ADMIN` · HOST-A | `ValidateLocName` with "Central Store" (exists only in CO-B) | `rtrValue` says **not** a duplicate — CO-B names are invisible to CO-A |
| `LOC-02-D29` | Injection / validation | `TOKEN_A_ADMIN` · HOST-A | `ValidateLocName` with `' OR 1=1 --`, then "" , then 10 000 characters | Treated as plain text: not a duplicate; no error text with SQL; no 500 |
| `LOC-02-D30` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Save, valid body; `ValidateLocName`; `GetUserWarehouse` | 401 `{ "message": "Unauthorized" }` each; nothing written or returned (LOC-Q11) |
| `LOC-02-D31` | Authentication | no token, then `TOKEN_EXPIRED` · HOST-A | `POST /Product/GetUserWarehouse` | 401 `{ "message": "Unauthorized" }`; no user list of any company (LOC-Q4). Expected to pass already (`[Authorize]` is in code) |
| `LOC-02-D32` | Company isolation | `TOKEN_A_ADMIN` · HOST-A | `POST /Product/GetUserWarehouse` | Only CO-A active staff (EMP-A1, EMP-A2); no EMP-B1, no EMP-AX |
