# Company Onboarding — developer test cases

Defaults: `Authorization: Bearer TOKEN_SA` on `/SuperAdmin/*`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `ONB-01-D01` | Open endpoint (C1) | none | `POST /Company/CompanyOnboard` with a full body | `Status false`, "Not available — use the super admin panel."; no company created |
| `ONB-01-D02` | Auth | none / TOKEN_U1 / expired | `/SuperAdmin/CompanyOnboard` | 401 |
| `ONB-01-D03` | Over-posting | default | body with `password`, `changedBy`, `hostName`, `connectionString` | Ignored; host derived from the code; `CreatedBy` = SA |
| `ONB-01-D04` | Code rules (C4) | default | `companyShortName` "Cafe One", "ab", "-cafe", "cafe-", "www", 31 chars | Refused with the D1 messages |
| `ONB-01-D05` | Atomic company (C3) | default | force an error inside `usp_CompanyOnboard_Save` (e.g. bad currency id) | No tenant rows; onboarding row `Failed` at `Company`; message without SQL text |
| `ONB-01-D06` | Recovery (Q2) | default | make Central `SiteRegistry` insert fail | Onboarding `Incomplete` at `Register`; `RetryOnboarding` finishes without a second tenant company |
| `ONB-01-D07` | Password (C2, D4) | default | inspect DB / logs after onboarding | `User.Password` encrypted; `MustChangePassword = 1`; no password in logs or the onboarding row |
| `ONB-01-D08` | Location (C5) | default | after onboarding | One `Warehouse` "Main Store" for the company; admin in `UserWarehouseMapping` |
| `ONB-02-D01` | Resend (D4) | default | `ResendWelcomeEmail` | New salt / password, flag set; old password fails `authenticate` |
| `ONB-03-D01` | Flag | new admin | `authenticate` | `mustChangePassword: true`; after `ChangeUserPassword` → false |
| `ONB-01-D09` | Status email (C2) | default | `UpdateCompanyStatus` | Email has no password |
| `ONB-ALL-D01` | Garbage | default | `subscriptionPlanId` 0 / unknown; `billingCycle` "WEEKLY"; e-mail "x@" | Field messages; nothing created |
