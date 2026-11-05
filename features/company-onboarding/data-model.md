# Data model — Company Onboarding

Two databases. Central scripts live in `ProductManagement.API/Database Scripts/CentralDB/` (numbered); tenant scripts in `RMS_API/DBSCRIPT/`.

## Central DB (RMS_Central)

### New table `dbo.CompanyOnboarding` (`11_CompanyOnboarding.sql`)
| Column | Type | Notes |
|---|---|---|
| `OnboardingId` | uniqueidentifier PK | |
| `CompanyName`, `CompanyCode`, `HostName` | nvarchar | as requested / derived |
| `DatabaseId` | uniqueidentifier | tenant database chosen |
| `CompanyId` | bigint NULL | set after the `Company` step |
| `AdminUserId` | uniqueidentifier NULL | |
| `AdminEmail`, `AdminFirstName`, `AdminLastName`, `MobileNo`, `CompanyAddress` | nvarchar | for email / retry (no password) |
| `SubscriptionPlanId` | int | |
| `BillingCycle` | nvarchar(20) | MONTHLY / ANNUAL |
| `Status` | nvarchar(20) | `InProgress` / `Incomplete` / `Complete` |
| `CurrentStep` | nvarchar(20) | `Company` / `Register` / `Plan` / `Email` / `Payment` / `Done` |
| `FailedStep`, `LastError` | nvarchar | last failure (message shown to the super admin) |
| `EmailSent`, `PaymentLinkCreated` | bit | non-blocking steps |
| `PaymentUrl` | nvarchar(500) NULL | |
| `CreatedAt`, `CreatedBy`, `UpdatedAt` | | `CreatedBy` = super admin id from the token |
Index: `(Status)`.

### `dbo.PlatformNotificationTemplate` — "Company Onboarding"
Template gains `{{PaymentLink}}` (and a "Pay now" block shown only when a link exists); `{{Password}}` stays for the temporary password. "Company Activated / Deactivated" templates: `{{Password}}` replaced by a "use your current password or Forgot password" line (`11_CompanyOnboarding.sql` updates the HTML).

## Tenant DB

| Object | Change |
|---|---|
| `Tables/dbo.User` | **New column** `MustChangePassword bit NOT NULL DEFAULT 0` (guarded `IF COL_LENGTH`) |
| `SP/dbo.usp_CompanyOnboard_Save` | + `@FirstLocationName`; creates the location (`Warehouse`, type Store, default location, takes requisitions) and maps the admin (`UserWarehouseMapping`); admin `MustChangePassword = 1`; image path from a parameter (no hard-coded Dev URL); duplicate company / short name inside the tenant → `Status 0` with a clear message; error text kept short |
| `SP/dbo.Sp_SetTemporaryPassword` (**new**) | Sets a new password + salt and `MustChangePassword = 1` for a company's admin (Resend email) |
| `SP/dbo.SP_AuthenticateUser` | Also returns `MustChangePassword` |
| `SP/dbo.SP_ChangePassword` | Clears `MustChangePassword` on success |
| `SP/dbo.<company status procedure>` | Stops returning the decrypted password to the status email |
