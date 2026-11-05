# Super Admin → Company Onboarding

**Company Onboarding** is how a super admin brings a new customer onto RMS: one guided flow creates the company in a tenant database (with its roles, admin user, support user, statuses, units, inventory types, wastage reasons and a **first location** the admin is mapped to), registers its sub-domain in Central, assigns the subscription plan, emails the admin a **temporary password** with the login link and the **payment link**, and shows the result. The admin must set their own password at first login. If any step after the company is created fails, the onboarding is kept as **Setup incomplete** and can be finished with **Retry**.

Status (2026-11-04): **🟢 Built · SQL not yet run · testing deferred.** Plan: [`RMS_API/specs/company-onboarding/plan.md`](../../../RMS_API/specs/company-onboarding/plan.md). UI / UX plan: [`ui-ux.md`](./ui-ux.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `ONB`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-onboard-company.md`](./01-onboard-company.md) | Super admin onboards a company through the 4-step wizard and sees the result |
| 02 | [`02-finish-incomplete.md`](./02-finish-incomplete.md) | Super admin sees onboardings that didn't finish and completes them (Retry), or re-sends the welcome email |
| 03 | [`03-first-login-password.md`](./03-first-login-password.md) | The new company admin signs in with the temporary password and must set their own |

## Defects this feature fixes (found 2026-11-04)

| # | Defect | Fixed by |
|---|---|---|
| C1 | **`/Company/CompanyOnboard` needs no login** (`[Authorize]` commented out) and takes `DatabaseId`, `ChangedBy` and host fields from the body: anyone can create companies in any database | [01](./01-onboard-company.md) |
| C2 | The **admin password is typed by the super admin and emailed**; the company status email re-sends the stored password (`UpdateCompanyStatus` → `{{Password}}`) | [01](./01-onboard-company.md), [03](./03-first-login-password.md) |
| C3 | Steps after the tenant company is created (Central registration, subscription, email, payment link) run **without recovery**: a failure leaves a company that exists but can't be reached or has no plan, while the screen says "Failed to save company"; a retry then fails with "name already in use" | [01](./01-onboard-company.md), [02](./02-finish-incomplete.md) |
| C4 | The **short name becomes the sub-domain** but is never checked: spaces, dots, underscores, upper case or reserved names (www, api, admin) produce a broken site address | [01](./01-onboard-company.md) |
| C5 | A new company has **no location**, so every stock screen says "You aren't assigned to any location" until someone creates one and maps users | [01](./01-onboard-company.md) |
| C6 | The payment page **opens in the super admin's browser**; the customer never receives the link | [01](./01-onboard-company.md) |
| C7 | Small issues: company image path hard-coded to a Dev URL; raw SQL error text shown; admin e-mail "already exists" check runs against the wrong database; one long form with no review; Reset in red next to Save | [01](./01-onboard-company.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **Generated password, change at first login**: the server generates a strong temporary password, emails it once with the login link; the admin must set their own at first sign-in; nobody else sees it; status emails never contain a password | 2026-11-04 |
| Q2 | **Setup incomplete + Retry**: each onboarding is tracked step by step in Central; a failure after the company exists keeps it as *Setup incomplete* with the failed step and message; Retry continues from that step. An email failure alone doesn't fail the onboarding (it shows *Email not sent* with **Resend email**) | 2026-11-04 |
| Q3 | **First location**: the wizard asks for the first location name (default "Main Store", type Store); it is created with the company and the admin is mapped to it | 2026-11-04 |
| Q4 | **Payment link shown and emailed**: the success screen shows the link with Copy; the welcome email includes it; nothing opens automatically | 2026-11-04 |
| D1 | Short name (= company code = sub-domain): 3–30 characters, lower-case letters, digits and hyphens, starts with a letter, doesn't end with a hyphen, not reserved (`www, api, admin, superadmin, support, mail, app, rms, static, cdn`); typed in any case and lower-cased; the full address `https://<code>.<base domain>` is previewed live | 2026-11-04 (default) |
| D2 | Wizard: **1 Company** (name, short name, business type, currency, country / phone code, address, date and time format) · **2 Admin & location** (first / last name, e-mail, mobile, first location) · **3 Plan** (existing plan cards, billing cycle) · **4 Review** → **Create company** | 2026-11-04 (default) |
| D3 | Steps and recovery (Q2): `Company` (tenant procedure, all-or-nothing, includes the location) → `Register` (Central site) → `Plan` → `Email` → `Payment`. `Company`, `Register` and `Plan` must succeed for the company to be usable; `Email` and `Payment` failures are reported but don't block | 2026-11-04 (default) |
| D4 | Temporary password: 12 characters with upper, lower, digit and symbol, generated on the server, never stored in plain text or logged; **Resend email** generates a new one (the old stops working) and sets the change-at-login flag again | 2026-11-04 (default) |
| D5 | First-login change: `User.MustChangePassword` (new column); sign-in returns it; the app sends the user to a **Set your password** page and lets them go only after a successful change, which clears the flag. Enforced in the app (the API still accepts the token) | 2026-11-04 (default) |
| D6 | All onboarding endpoints are super-admin only (`[SuperAdminAuthorize]`); the `/Company/CompanyOnboard` copy is closed (returns "Not available"); `ChangedBy` comes from the super admin token; `DatabaseId` stays a super-admin option | 2026-11-04 (default) |
| D7 | The admin e-mail is checked for format only (a new company has no users yet, so it can't clash) | 2026-11-04 (default) |

| D8 | **Business type is not asked**: RMS onboards restaurants, so the wizard hides the field and always sends `B2B`; the API defaults an empty value to `B2B` | 2026-11-04 (user) |

| D9 | **No onboarding tracking table — roll back instead** (replaces Q2): Central `CompanyOnboarding` is not used. If a required step after the restaurant exists fails (*Register* / *Plan*), the API deletes the Central site and subscription rows and calls tenant `Sp_RollbackCompanyOnboard`, then shows "Restaurant could not be created: the … step failed (…). Nothing was saved." No *Setup incomplete* list, banner, drawer or Retry. *Payment* / *Email* failures still don't block; *Resend welcome email* stays (by restaurant id) | 2026-11-04 (user) |
| D10 | Live name / short-name checks use the existing `ValidateCompanyName` / `ValidateCompanyShortName` endpoints, unchanged; `CheckCompanyCode` is removed | 2026-11-04 (user) |

## Implementation notes (2026-11-04)
Where the build differs from the text above:
- **Step order** is Company → Register → Plan → **Payment → Email**, so the welcome e-mail can carry the payment link (Q4).
- **Admin e-mail clash (D7)**: the tenant database is shared and `User.UserName` is unique across it, so the e-mail *can* clash. The wizard checks it live ("This e-mail is already used by another account") and `usp_CompanyOnboard_Save` refuses it.
- **Sign-in flag**: `mustChangePassword` is read with a separate query after `SP_AuthenticateUser`; that procedure is unchanged.
- **First location** is created with the existing location type **Warehouse** (default, takes requisitions), not "Store".
- **Creating screen** lists the steps with one progress bar (the server runs all steps in one request); the result page shows ✓ / ⚠ per step.
- **Payment link failure** shows "Send it later from the company list" — there is no separate *Create link* button.
- Onboarding confirmations use a small local dialog (the shared `ConfirmDialog` is styled for deletes and appends "?").

## Out of scope
Self-service sign-up; editing a company after onboarding (company list / settings); deleting a half-created company (Retry finishes it; deactivate from the company list if abandoned); moving a company to another database; enforcing the first-login change on the API side.
