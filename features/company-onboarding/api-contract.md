# API contract — Company Onboarding

Super-admin endpoints: `POST /SuperAdmin/...`, `[SuperAdminAuthorize]`, envelope `{ Status, Message, Data }`. The super admin id comes from the token.

| Endpoint | Change |
|---|---|
| `/SuperAdmin/GetOnboardingSettings` (**new**) | → `{ baseDomain, reservedCodes[] }` for the live address preview |
| `/SuperAdmin/CheckCompanyCode` (**new**) | `{ code }` → `{ valid, available, message, hostName }` (D1 rules + Central `SiteRegistry` / `CompanyOnboarding`) |
| `/SuperAdmin/ValidateCompanyName` | Kept; checks Central (`SiteRegistry`, `CompanyOnboarding`) as well |
| `/SuperAdmin/CompanyOnboard` | **Reshaped request**: `{ companyName, companyShortName, businessType, currencyId, currencyCode, countryCode, countryName, phoneCode, companyAddress, dateFormat, timeFormat, adminFirstName, adminLastName, adminEmail, mobileNo, firstLocationName, subscriptionPlanId, billingCycle, databaseId? }` — no password, no `changedBy`, no host fields. → `{ onboardingId, status (Complete / Incomplete / Failed), companyId, hostName, adminEmail, paymentUrl, steps: [{ step, ok, message }] }` |
| `/SuperAdmin/GetIncompleteOnboardings` (**new**) | → rows `{ onboardingId, companyName, companyCode, failedStep, lastError, createdAt, createdByName }` |
| `/SuperAdmin/RetryOnboarding` (**new**) | `{ onboardingId }` → same shape as `CompanyOnboard` |
| `/SuperAdmin/ResendWelcomeEmail` (**new**) | `{ companyId }` → `{ sent, message }` (new temporary password) |
| `/SuperAdmin/UpdateCompanyStatus` | Status email no longer contains a password |
| `/Company/CompanyOnboard` | **Closed**: returns `Status false`, "Not available — use the super admin panel." |
| `/Users/authenticate` | Response gains `mustChangePassword` |
| `/Users/ChangeUserPassword` | Unchanged contract; clears the flag |

Messages: "This short name is taken" · "Use 3–30 lower-case letters, digits or hyphens, starting with a letter" · "That name is reserved" · "This company name is already in use" · "Enter a valid e-mail" · "Select a plan" · "Company could not be created: <reason>. Nothing was saved." · "Setup incomplete at <step>: <reason>" · "You don't have permission to perform this action." 401 for no / invalid / non-super-admin token.
