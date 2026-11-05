# 02 — Finish an incomplete onboarding / resend the welcome email

## Summary
Onboardings that stopped after the company was created are listed for the super admin, who can finish them; a completed company's welcome email can be sent again with a fresh temporary password.

## Main flow
1. Super Admin → Companies: when any onboarding is *Setup incomplete*, a banner "2 onboardings need attention" opens a panel listing them: company, short name, started at / by, failed step, message, **Retry**.
2. **Retry** → the server continues from the failed step (`Register` → `Plan` → `Email` → `Payment`), skipping steps already done; the row disappears when everything required is done; the result is shown as in use case 01 step 7.
3. On a company row (and on the result page): **Resend welcome email** → confirm "Send a new temporary password to admin@cafeone.com? The old one stops working." → server sets a new temporary password (must change at login) and sends the email (with the payment link while the plan is unpaid).

## Alternative flows
Retry fails again → the row stays with the new message · Resend email fails → "Email could not be sent: <reason>" (the password is not changed) · onboarding / company not found → "Not found".

## Acceptance criteria
1. Retry never creates a second company or site row (C3).
2. Resend email invalidates the previous temporary password and sets the change-at-login flag.
3. Super admin only.

## Open questions
None.
