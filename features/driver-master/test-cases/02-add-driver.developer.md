# 02 — Add a driver — developer test cases

- Use case: [`../02-add-driver.md`](../02-add-driver.md) · API: [`../api-contract.md`](../api-contract.md) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Save" = `POST /Setting/AddEditDriver` with `driverId` = empty GUID; "Validate" = `POST /Users/ValidateLicNo`.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `DRV-02-D01` | Authentication | no token; `TOKEN_EXPIRED` | Save `{ "driverName": "Vikram Malhotra", "licNo": "DL-0420220011223" }` | 401; nothing inserted |
| `DRV-02-D02` | Authentication (D4) | **no token** | Validate `{ "licNo": "DL-1420110012345", "companyId": <CO-A>, "driverId": <empty> }` | 401 (today it answers anonymously) |
| `DRV-02-D03` | Company isolation | `TOKEN_A_ADMIN` · HOST-B | Save | 401 "Token does not match this site's company." |
| `DRV-02-D04` | Main flow | default | Save "Vikram Malhotra", " dl-0420220011223 ", `profileImage: null` | `status: true`, `data.driverId`; row `CompanyId` = CO-A, `IsActive = 1`, `LicNo = "DL-0420220011223"`, `CreatedOn`/`LastUpdatedOn` ≈ UTC now |
| `DRV-02-D05` | Over-posting | default | Save with `"isActive": 0, "companyId": <CO-B>` | Saved in CO-A, Active |
| `DRV-02-D06` | Validation / injection | default | `driverName`: missing, `"  "`, `"John'; DROP TABLE DriverMaster; --"` (has `;`), 101 characters; `licNo`: missing, `"DL@11"`, 51 characters | The exact required / characters / length messages from the API contract; nothing inserted; table intact |
| `DRV-02-D07` | Validation (photo, Q4) | default | `profileImage`: a 1.5 MB PNG data URL; a `data:image/gif;base64,…`; `"http://x/y.png"`; a 900 KB JPEG data URL | First three: "Profile picture must be a JPG or PNG image of 1 MB or less", nothing saved; last: `status: true` |
| `DRV-02-D08` | Business rule (D2) | default | Save with DRV_1's licence; with DRV_3's (inactive) licence in lower case | "License number already exists", `status: false` each (today `status: true`, empty message) |
| `DRV-02-D09` | Company isolation | default | Save with DRV_B's licence (CO-B only) | `status: true` |
| `DRV-02-D10` | Validate (D4) | default | Validate DRV_3's licence (inactive); DRV_B's licence; with `"companyId": <CO-B>` and DRV_B's licence | `true`; `false`; `false` (body company ignored) |
| `DRV-02-D11` | Concurrency | `TOKEN_A_ADMIN` and `TOKEN_A_ADMIN2` | 10 parallel saves with licence "RACE-1" | Exactly one row; the rest "License number already exists" |
| `DRV-02-D12` | Permission | `TOKEN_A_VIEW` | Save | "You don't have permission to perform this action."; nothing inserted |
| `DRV-02-D13` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Save; Validate | 401 each |
| `DRV-02-D14` | Error hygiene | default | Save while `SP_AddEditDriver` fails | "Something went wrong. Please try again."; no SQL or stack trace |
