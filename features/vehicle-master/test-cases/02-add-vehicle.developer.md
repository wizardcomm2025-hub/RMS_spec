# 02 — Add a vehicle — developer test cases

- Use case: [`../02-add-vehicle.md`](../02-add-vehicle.md) · API: [`../api-contract.md`](../api-contract.md#save-vehicle) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Save" = `POST /Setting/AddEditVechile`; valid body:

```json
{
  "vehicleId": "00000000-0000-0000-0000-000000000000",
  "regNo": "DL02XY1234",
  "brandName": "Toyota Hilux",
  "isActive": true
}
```

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `VEH-02-D01` | Authentication | no token | Save | 401; nothing written |
| `VEH-02-D02` | Authentication | `TOKEN_EXPIRED`, `TOKEN_TAMPERED` | Save | 401; nothing written |
| `VEH-02-D03` | Company isolation | `TOKEN_A_ADMIN` · HOST-B | Save | 401 "Token does not match this site's company."; nothing written |
| `VEH-02-D04` | Main flow | default | Save, valid body | `status: true`; new row in CO-A, `IsActive = 1`, `CreatedBy` = A-ADMIN, `CreatedOn` in UTC |
| `VEH-02-D05` | Over-posting | default | Valid body + `"companyId": <CO-B>`, `"createdBy": "<B-ADMIN id>"`, `"isActive": false` | Row in CO-A, created by A-ADMIN, Active (body fields ignored) |
| `VEH-02-D06` | Validation / injection | default | `regNo` "DL-02-1234"; "<script>x</script>"; "x'); DROP TABLE VehicleMaster;--" | "Registration number can only contain letters and numbers"; nothing written; table intact |
| `VEH-02-D07` | Validation | default | `brandName` missing, "", "   ", > 100 characters | Missing/empty: "Car manufacturer name is required"; > 100: "Car manufacturer name must be 100 characters or less" |
| `VEH-02-D08` | Validation | default | `regNo` " mh12ab1234 " (spaces, lowercase) | "Registration number already exists"; nothing written |
| `VEH-02-D09` | Validation | default | `regNo` "MH14ZZ0001" (inactive vehicle `VEH-OLD`) | "Registration number already exists" |
| `VEH-02-D10` | Company isolation | default | `regNo` "KA01AB1111" (CO-B vehicle `VEH-B-VAN`) | `status: true`; saved in CO-A (other company's registration does not block) |
| `VEH-02-D11` | Concurrency | default | Same valid body ("DL99ZZ0000") sent twice in parallel | Exactly one row written; second request fails with "Registration number already exists" |
| `VEH-02-D12` | Permission | `TOKEN_A_VIEW` | Save | "You don't have permission to perform this action."; nothing written |
| `VEH-02-D13` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Save; `ValidateRegNo` | 401 each |
| `VEH-02-D14` | Error hygiene | default | Save while `SP_AddEditVehicle` fails | "Something went wrong. Please try again."; no SQL or stack trace exposed |
| `VEH-02-D15` | Authentication | no token | `POST /Users/ValidateRegNo` | 401 |
| `VEH-02-D16` | Company isolation | default | Validate "KA01AB1111" (CO-B vehicle) | `rtrValue: false` (CO-B's registration does not count) |
| `VEH-02-D17` | Validation / injection | default | Validate " mh12ab1234 " → `true`; "' OR 1=1 --" → `false`; 10,000 chars → `false` or 400 | As stated; no SQL syntax exception returned |
