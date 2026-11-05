# Company Onboarding — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `ONB-01-T01` | 01 | Short name "Cafe One". | Shown as "cafe one" with "Use 3–30 lower-case letters, digits or hyphens, starting with a letter"; after typing "cafeone": ✓ and "https://cafeone.rmsv2.gobiens.com". |
| `ONB-01-T02` | 01 | Short name "cafe-main" / "admin". | "This short name is taken" / "That name is reserved". |
| `ONB-01-T03` | 01 | Complete all steps with Silver Monthly; Create. | Progress ticks; "Cafe One is ready", address, admin e-mail, "Temporary password e-mailed", payment link with Copy; no new browser tab. |
| `ONB-01-T04` | 01 | Open the welcome e-mail. | Login link, user name, temporary password, "Pay now" link. |
| `ONB-03-T01` | 03 | Sign in to cafeone with the temporary password. | Goes to "Set your password"; other pages redirect back. |
| `ONB-03-T02` | 03 | Set a new password. | "Password set" → dashboard; next sign-in goes straight to the dashboard. |
| `ONB-01-T05` | 01 | As the new admin open Stock → Stock Report. | "Main Store" is available (no "not assigned" message). |
| `ONB-02-T01` | 02 | With SMTP down, onboard "Cafe Two". | Result: ready, ⚠ "Email not sent", **Resend email**. |
| `ONB-02-T02` | 02 | SMTP back; Resend email; confirm. | "Welcome email sent"; old temporary password no longer works. |
| `ONB-02-T03` | 02 | Simulate a Central failure at Register; open Companies. | Banner "1 onboarding needs attention"; Retry → "Cafe Three is ready". |
| `ONB-UI-T01` | UI | Wizard and set-password page at 360 px. | One column; step bar shows "Step 1 of 4 · Company" with a progress bar; footer buttons full width; no sideways scroll. |
| `ONB-UI-T02` | UI | Step 1 empty; click Next. | Stays on step 1; first empty field focused with its message; step bar shows a red dot on step 1. |
| `ONB-UI-T03` | UI | Type a company name; click the back arrow. | "Discard this onboarding?" with **Keep editing** / **Discard**; Keep editing keeps the text. |
| `ONB-UI-T04` | UI | On step 4 click **Edit** on Admin & location, change the e-mail, Next to Review. | Step 2 opens with values kept; Review shows the new e-mail. |
| `ONB-UI-T05` | UI | Step 3: Next without a plan. | "Select a plan to continue" above the cards. |
| `ONB-UI-T06` | UI | Set your password: new password "abc". | Checklist items unticked; **Save password** disabled; confirm mismatch shows "Passwords don't match". |
| `ONB-UI-T07` | UI | Keyboard only through the wizard. | Every field, step and button reachable with Tab; Esc closes dialogs; short-name status is read out. |
| `ONB-UI-T08` | UI | Company list with incomplete onboardings, at 360 px; open Review. | Banner "2 onboardings need attention"; drawer opens full screen with Retry on each row. |
| `ONB-UI-T09` | UI | Result page: click Copy on the address. | Snackbar "Copied"; no new browser tab opened at any point. |
