# 01 — Onboard a company

## Summary
A signed-in super admin creates a new customer company in four steps and gets its address, admin e-mail and payment link.

## Main flow
1. Super Admin → Companies → **Onboard company** → wizard page `/superadmin/companyCreation` with a step bar: Company · Admin & location · Plan · Review.
2. **Company**: Company name * (unique) · Short name * (D1, unique; live preview "https://cafe-one.rmsv2.gobiens.com", available ✓ / taken ✗) · Business type * · Currency * · Country * (sets the phone code) · Address · Date format · Time format. **Next** validates the step.
3. **Admin & location**: First name * · Last name · E-mail * (format) · Mobile * (with the country's phone code) · First location name * (default "Main Store"). Note: "A temporary password will be e-mailed to the admin; they set their own at first sign-in."
4. **Plan**: plan cards and Monthly / Annual (as today); one plan required.
5. **Review**: summary of every step with **Edit** links; price for the chosen cycle. **Create company**.
6. Server (D3): checks the super admin, every field (D1, D7), name / code / address free in Central; then
   - `Company`: tenant procedure creates the company, roles, admin user (temporary password, must change), support user, statuses, units, inventory types, wastage reasons and the first location with the admin mapped — one transaction;
   - `Register`: Central site row (code, host, database);
   - `Plan`: subscription assigned (Pending payment);
   - `Email`: welcome email with login link, user name, temporary password and payment link;
   - `Payment`: payment link created (when the plan has a price).
7. **Result** page: "Cafe One is ready" with the address (open / copy), admin e-mail, "Temporary password e-mailed to …", payment link (copy) and a step list (✓ / ⚠). Buttons: **Go to companies** · **Onboard another**.

## Alternative flows
- Field errors inline ("Use 3–30 lower-case letters, digits or hyphens", "This short name is taken", "Enter a valid e-mail").
- Name / code taken at create time (someone else just used it) → back to step 1 with the message.
- `Company` step fails → nothing was created: "Company could not be created: <reason>. Nothing was saved." (raw SQL text is logged, not shown).
- `Register` or `Plan` fails → result page shows *Setup incomplete* with the failed step and **Retry** (use case 02).
- `Email` or `Payment` fails → result page shows the company as ready with ⚠ "Email not sent" / "Payment link not created" and **Resend email** / **Create link**.
- Leaving the wizard with entries → "Discard this onboarding?".

## Acceptance criteria
1. Only a signed-in super admin can onboard; `/Company/CompanyOnboard` no longer creates companies (C1).
2. No password is typed, shown or logged; the admin receives one by e-mail (C2, Q1).
3. A failure never leaves an onboarding without a record: it is either fully rolled back (`Company`) or listed as *Setup incomplete* (C3, Q2).
4. The short name is a valid, free sub-domain (C4).
5. The new company has a location with the admin mapped (C5); the payment link is in the email and on screen, never auto-opened (C6).

## Open questions
None.
