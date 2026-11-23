# POS Configuration — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_ADMIN`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `POS-01-D01` | Company (T4) | default | `GetTaxSettingDetail` with CO-B's id | `Status false`, "This tax group was not found" |
| `POS-02-D01` | Company (T4) | default | `AddEditTaxSetting` with CO-B's group id | "This tax group was not found"; CO-B unchanged |
| `POS-02-D02` | Component of another group (T4) | default | edit GST 5% sending GST 18%'s component id | "This tax group was not found" (BadLine); nothing saved |
| `POS-02-D03` | Remove (T2) | default | edit GST 18% sending only CGST | SGST row `IsActive = 0`; `GetTaxDt` without it |
| `POS-02-D04` | Separators (T3) | default | component name `a,b#c` | Saved exactly |
| `POS-02-D05` | Range (T3, D4) | default | percentage 100; -1; 2.555; total 100 | "Enter a percentage from 0 to 99.99" (×2); stored as 2.56 (two decimals); "The total must be under 100%" |
| `POS-02-D06` | Atomic (T6) | default | force a failure on the second component | No change at all |
| `POS-02-D07` | Over-posting | default | body with `companyId`, `isActive`, `isDefault`, component `isActiveTax` | Ignored |
| `POS-02-D08` | First default (D1) | default | new company, first `AddEditTaxSetting` | Created with `IsDefault = 1` |
| `POS-03-D01` | Default (D1) | default | `SetTaxSettingStatus` false on the default | "The default tax group can't be deactivated…" |
| `POS-03-D02` | Default (D1) | default | `SetDefaultTaxSetting` on an inactive group | "Activate this tax group first." |
| `POS-03-D03` | One default | default | two parallel `SetDefaultTaxSetting` calls | Exactly one default afterwards (unique index) |
| `POS-04-D01` | Bill (T1) | POS | `SP_CreateBill` with GST 5% default, GST 18% active | `BillTaxComponent` rows = CGST 2.5 + SGST 2.5; `BillHead.TaxSettingName` = GST 5% |
| `POS-04-D02` | No default | POS | company with no default group | No tax rows; bill total excl. tax |
| `POS-ALL-D01` | Auth | none / expired / tampered / SuperAdmin | each tax endpoint | 401 |
| `POS-ALL-D02` | Permission | U-VIEW / U-NONE | Add / Edit / status / default; list | "You don't have permission to perform this action." (U-VIEW: list allowed) |
| `POS-ALL-D03` | Garbage | default | `status` "X"; `taxSettingId` -1; empty body | Treated as All; "This tax group was not found"; "Group name is required" |
