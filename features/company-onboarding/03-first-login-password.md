# 03 — Set your password at first sign-in

## Summary
A user whose account was created (or reset) with a temporary password must choose their own before using the app.

## Main flow
1. The admin opens the login link from the email and signs in with the user name and temporary password.
2. Sign-in succeeds and returns `mustChangePassword = true`; the app goes straight to **Set your password** (`/set-password`), full screen, no menu.
3. Fields: Current (temporary) password · New password · Confirm. Rules shown live: at least 8 characters, upper and lower case, a digit, not the same as the temporary password.
4. **Save** → existing `ChangeUserPassword` → success clears `MustChangePassword` → "Password set" → dashboard.

## Alternative flows
Wrong current password → "Current password is incorrect" · rules not met → inline · navigating elsewhere while the flag is set → back to `/set-password` · Sign out link available.

## Acceptance criteria
1. A user with the flag can't reach any other page in the app until the password is changed (D5).
2. After the change the flag is cleared and the next sign-in goes to the dashboard.
3. Support sign-in (`SupportLoginAsCompany`) is not affected.

## Open questions
None.
