# Data model — POS Configuration

Tenant DB. Objects in `RMS_API/DBSCRIPT/`.

| Table | Use |
|---|---|
| `TaxSettingsMaster` | Groups: `TaxSettingId`, `TaxSettingName`, `IsActive`, `CompanyId`, `CreatedBy/At`, `UpdatedBy/At`, **`IsDefault` (new)** |
| `TaxComponent` | Components: `TaxComponentId`, `TaxSettingId`, `ComponentName`, `CurrentPercentage numeric(4,2)`, `IsActive` (removed = 0, Q3) |
| `BillHead`, `BillTaxComponent` | A bill's copy of its tax (unchanged; written by `SP_CreateBill`) |
| `OrderChannel`, `OrderChannelTypeMaster`, `Warehouse` | Read and written by the PosPro API (unchanged by this feature) |

| Object | Change |
|---|---|
| `Tables/dbo.TaxSettingsMaster` | **New column** `IsDefault bit NOT NULL DEFAULT 0` (guarded) + filtered unique index `UX_TaxSettingsMaster_Default (CompanyId) WHERE IsDefault = 1` |
| `specs/pos-configuration/migrate_default_tax_group.sql` (run once) | Sets the oldest active group as default for each company with active groups and no default (D2) |
| `SP/dbo.Sp_GetTaxSettings` (**new**) | `@CompanyId, @Status (All / Active / Inactive), @Search` → groups: id, name, `IsActive`, `IsDefault`, active component count, `ComponentsText` ("CGST 2.5% · SGST 2.5%"), `TotalPercentage`, `UpdatedAt` |
| `SP/dbo.Sp_GetTaxSetting` (**new**) | `@CompanyId, @TaxSettingId` → header + active components; nothing for another company |
| `SP/dbo.Sp_SaveTaxSetting` (**new**) | `@CompanyId, @UserId, @TaxSettingId (0 = new), @Name, @Lines XML (TaxComponentId, Name, Percentage)`; one transaction; codes `ok / NotFound / NoName / NameTooLong / DuplicateName / NoLine / BadLineName / DuplicateLine / BadPercent / TotalTooHigh / BadLine` → `TaxSettingId` |
| `SP/dbo.Sp_SetTaxSettingStatus` (**new**) | `@CompanyId, @UserId, @TaxSettingId, @IsActive`; codes `ok / NotFound / IsDefault` |
| `SP/dbo.Sp_SetDefaultTaxSetting` (**new**) | `@CompanyId, @UserId, @TaxSettingId`; codes `ok / NotFound / Inactive` |
| `SP/dbo.SP_CreateBill` | Tax part: only `IsDefault = 1` group's active components (Q2, T1); `CREATE OR ALTER` |
| `SP/dbo.GetTaxDt` | Same filter; `CREATE OR ALTER` |
| `SP/dbo.SP_AddEditTaxSetting`, `SP_GetTaxSettingList`, `SP_GetTaxSettingById`, `SP_GetTaxComponentById` | Unchanged; no longer called by the API (D8) |

## Rules in the procedures
- Every lookup filters by `CompanyId`; a component id is used only if it belongs to the group being saved (T4).
- Names compared trimmed and case-insensitive (D3, D4); total = sum of the sent components (D4).
- A new group is the default when the company has none (D1); the unique index guarantees one default per company.
- `Sp_SetDefaultTaxSetting` clears the old default and sets the new one in one transaction.
