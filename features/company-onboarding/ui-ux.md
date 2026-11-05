# UI / UX — Company Onboarding

> Complete UI / UX plan for the super-admin onboarding screens and the tenant "Set your password" page, per `RMS_API/specs/UI_UX_STANDARDS.md` (10-point checklist). Text the user sees is written **verbatim**; tester cases quote it. Theme tokens only, no hex. Screens live in the super-admin layout (`app/superadmin/layout.tsx`) except Set your password (tenant app, no menu).

Screens:
- **A** Onboarding wizard — `/superadmin/companyCreation` (4 steps)
- **B** Creating (progress) and **C** Result — same route, after Create
- **D** Company list additions — `/superadmin/companyList` (incomplete banner + drawer, Resend welcome email)
- **E** Set your password — `/set-password` (tenant app)

---

## 1. Page anatomy

### A. Wizard
```
┌──────────────────────────────────────────────────────────────────────────────────┐
│ Companies › Onboard company                                                       │
│ [←]  Onboard company                                                              │
├──────────────────────────────────────────────────────────────────────────────────┤
│  ①──────────────②──────────────③──────────────④                                   │
│  Company         Admin &        Plan           Review                             │
│  ●done           location ●now  ○              ○                                  │
├──────────────────────────────────────────────────────────────────────────────────┤
│ ┌ card ─────────────────────────────────────────────────────────────────────────┐ │
│ │ STEP TITLE                                       step subtitle (one line)      │ │
│ │ fields (2 columns md+, 1 column below)                                         │ │
│ └────────────────────────────────────────────────────────────────────────────────┘ │
│  (content scrolls; max width 960, centred)                                        │
├──────────────────────────────────────────────────────────────────────────────────┤
│ sticky footer:  Start over (text, step 1 only)        [Back]   [Next] / [Create company] │
└──────────────────────────────────────────────────────────────────────────────────┘
```
- `PageHeader`: breadcrumb "Companies › Onboard company", title "Onboard company", `onBack` → list (with the discard prompt when changed), no description, no About.
- Step bar: numbered circles joined by a line; done = filled success colour with ✓, current = filled info colour, upcoming = outlined grey. Clicking a done step goes back to it; upcoming steps are not clickable.
- One card per step; card title is the step name in overline style with a one-line subtitle.

### Step 1 — Company
```
COMPANY                                             Who the customer is and how their site works
┌──────────────────────────────┐ ┌──────────────────────────────┐
│ Company name *               │ │ Short name *          ✓ free │
└──────────────────────────────┘ └──────────────────────────────┘
                                   https://cafeone.rmsv2.gobiens.com   (monospace caption)
┌──────────────────────────────┐ ┌──────────────────────────────┐
│ Currency ▾                   │ │ Country ▾  (sets phone code) │
└──────────────┘ └──────────────┘ └──────────────────────────────┘
┌───────────────────────────────────────────────────────────────┐
│ Address                                                        │
└───────────────────────────────────────────────────────────────┘
┌──────────────────┐ ┌──────────────────┐
│ Date format ▾    │ │ Time format ▾    │      (preview: "04/Nov/2026 · 06:30 PM")
└──────────────────┘ └──────────────────┘
```

### Step 2 — Admin & location
```
ADMIN USER                                          Signs in first and sets up the company
┌──────────────────┐ ┌──────────────────┐
│ First name *     │ │ Last name        │
└──────────────────┘ └──────────────────┘
┌──────────────────────────────┐ ┌──────┬───────────────────────┐
│ E-mail (user name) *         │ │ +91 ▾│ Mobile *              │
└──────────────────────────────┘ └──────┴───────────────────────┘
ⓘ A temporary password is e-mailed to the admin. They choose their own at first sign-in.

FIRST LOCATION                                      Where stock is kept from day one
┌──────────────────────────────┐
│ Location name *   Main Store │      caption: "The admin is assigned to it. Add more later in Setting → Location."
└──────────────────────────────┘
```

### Step 3 — Plan
Existing `ModuleSelectionSection` (plan cards + Monthly / Annual toggle) inside the step card. Selected card: info border + check badge. Caption under the toggle: "Price shown for the chosen billing cycle."

### Step 4 — Review
```
REVIEW                                              Check everything before creating the company
┌ Company ──────────────────────────────── Edit ┐ ┌ Admin & location ─────────────── Edit ┐
│ Cafe One                                       │ │ Asha Rao · asha@cafeone.com            │
│ https://cafeone.rmsv2.gobiens.com              │ │ +91 98765 43210                        │
│ INR · India                                    │ │ First location: Main Store             │
│ 04/Nov/2026 · 06:30 PM                         │ │ Temporary password: e-mailed           │
└────────────────────────────────────────────────┘ └────────────────────────────────────────┘
┌ Plan ─────────────────────────────────── Edit ┐
│ Silver · Monthly · ₹999 / month                │
│ Payment link e-mailed to asha@cafeone.com      │
└────────────────────────────────────────────────┘
```

### B. Creating
Full-card progress replaces the step content; footer buttons disabled.
```
Creating Cafe One…
 ✓ Company and admin user        (done)
 ⟳ Web address                   (running)
 ○ Plan
 ○ Welcome e-mail
 ○ Payment link
```

### C. Result — success
```
┌───────────────────────────────────────────────────────────────┐
│                         (✓ large, success)                     │
│                     Cafe One is ready                          │
│  Address        https://cafeone.rmsv2.gobiens.com   [Open] [Copy] │
│  Admin          asha@cafeone.com                               │
│                 Temporary password e-mailed                    │
│  Payment link   https://rzp.io/l/abc123              [Copy]     │
│  ─────────────────────────────────────────────────────────    │
│  ✓ Company and admin user   ✓ Web address   ✓ Plan            │
│  ✓ Welcome e-mail           ✓ Payment link                     │
│                                                                │
│            [Go to companies]   [Onboard another]               │
└───────────────────────────────────────────────────────────────┘
```
Partial (email / payment failed): same card, the failed rows show ⚠ with the message and an inline **Resend email** / **Create link** button.
Incomplete (Register / Plan failed): warning icon, title "Setup incomplete", body "Cafe One was created but the <step> step failed: <reason>.", buttons **Retry** (primary) · **Go to companies**.

### D. Company list additions
```
┌ ⚠ 2 onboardings need attention                                         [Review] ┐
└──────────────────────────────────────────────────────────────────────────────────┘
Drawer (right, 420 px; full screen on phones): "Incomplete onboardings"
┌───────────────────────────────────────────────┐
│ Cafe Three · cafethree                         │
│ Stopped at: Web address                        │
│ "Host already registered…"                     │
│ Started 04/Nov/2026 10:12 by Super Admin       │
│                                     [Retry]    │
└───────────────────────────────────────────────┘
```
Company row actions gain **Resend welcome email** (mail icon with tooltip).

### E. Set your password
```
            ┌───────────────────────────────────────┐
            │  (app logo)                            │
            │  Set your password                     │
            │  Choose a password only you know.      │
            │  ┌─────────────────────────────────┐   │
            │  │ Temporary password *        👁 │   │
            │  └─────────────────────────────────┘   │
            │  ┌─────────────────────────────────┐   │
            │  │ New password *              👁 │   │
            │  └─────────────────────────────────┘   │
            │  ✓ At least 8 characters               │
            │  ✓ Upper and lower case                │
            │  ○ A number                            │
            │  ○ Different from the temporary one    │
            │  ┌─────────────────────────────────┐   │
            │  │ Confirm new password *      👁 │   │
            │  └─────────────────────────────────┘   │
            │  [ Save password ]  (full width)       │
            │  Sign out                              │
            └───────────────────────────────────────┘
```

---

## 2. Columns / fields

| Field | Step | Required | Rule / hint (verbatim) |
|---|---|---|---|
| Company name | 1 | yes | max 100; unique — "This company name is already in use" |
| Short name | 1 | yes | lower-cased as typed; 3–30, `a-z 0-9 -`, starts with a letter, no trailing hyphen — "Use 3–30 lower-case letters, digits or hyphens, starting with a letter"; reserved — "That name is reserved"; taken — "This short name is taken"; adornment "✓ free" / "✗ taken"; caption = full address |
| Business type | — | — | not shown; always `B2B` (D8 — RMS onboards restaurants) |
| Currency | 1 | yes | searchable select, shows "INR — Indian Rupee" |
| Country | 1 | yes | searchable select; sets the phone code on step 2 |
| Address | 1 | no | max 250, 2 lines |
| Date format | 1 | yes | select; live preview of today |
| Time format | 1 | yes | select; live preview of now |
| First name | 2 | yes | max 50 |
| Last name | 2 | no | max 50 |
| E-mail | 2 | yes | format — "Enter a valid e-mail"; hint "Used as the admin's user name" |
| Mobile | 2 | yes | digits 6–15 with the phone-code prefix select — "Enter a valid mobile number" |
| First location name | 2 | yes | max 100, default "Main Store" |
| Plan | 3 | yes | "Select a plan to continue" (inline, above the cards) |
| Billing cycle | 3 | yes | Monthly / Annual toggle |
| Temporary password | E | yes | "Current password is incorrect" from the server |
| New password | E | yes | live checklist; on Save "Password doesn't meet the rules" |
| Confirm | E | yes | "Passwords don't match" |

## 3. Dialog / form behaviour

- **Next** validates the current step only; on error it scrolls to and focuses the first invalid field; the step bar shows a red dot on a step with errors.
- **Back** keeps everything typed. Step data persists until Create succeeds or the user discards.
- Short-name check runs 400 ms after typing stops; the Next button waits for a pending check.
- **Create company** (step 4) disables the footer and shows screen B; the browser's back / close is blocked with the standard "Leave site?" prompt while creating.
- Leaving with changes (back arrow, breadcrumb, menu) → discard prompt (§5). `beforeunload` warning while changed.
- Set your password: Enter submits; Save disabled until the checklist and confirm are satisfied.

## 4. Feedback states

| State | Behaviour (exact text) |
|---|---|
| First load (wizard) | Step card skeleton while currencies, countries and plans load |
| Lists failed to load | Inline alert on step 1: "We couldn't load currencies and countries. Retry" (Retry button) ; plans: "We couldn't load plans. Retry" on step 3 |
| Code check failed | Adornment "⚠ couldn't check" and Next re-checks |
| Creating | Screen B, steps tick as they finish |
| Success | Screen C success + snackbar "Cafe One onboarded" |
| Company step failed | Back on step 4 with an error alert: "Company could not be created: <reason>. Nothing was saved." |
| Incomplete | Screen C incomplete variant |
| Retry success | Snackbar "Cafe Three onboarded"; drawer row removed |
| Retry failure | Row message updated; snackbar "Still not finished: <reason>" |
| Resend email | Snackbar "Welcome e-mail sent to asha@cafeone.com" / "E-mail could not be sent: <reason>" |
| Set password success | Snackbar "Password set" → dashboard |
| Set password failure | Inline under the temporary password: "Current password is incorrect" |

## 5. Confirmations

| Trigger | Title | Body | Buttons |
|---|---|---|---|
| Leave wizard with changes | Discard this onboarding? | Everything entered so far will be lost. | Keep editing · Discard |
| Start over (step 1) | Start over? | Clear all steps and begin again. | Cancel · Start over |
| Resend welcome email | Resend welcome e-mail? | A new temporary password will be sent to asha@cafeone.com. The current one stops working. | Cancel · Resend |
| Retry (drawer / result) | — (no confirm; safe to repeat) | | |

## 6. Permissions

Super-admin sign-in for screens A–D (the super-admin layout already guards them). Screen E: any signed-in tenant user whose `mustChangePassword` is true; others visiting `/set-password` are sent to the dashboard.

## 7. Responsive

| | xs (360) | sm (600) | md+ (900–1920) |
|---|---|---|---|
| Header | title only, back arrow | breadcrumb + title | breadcrumb + title |
| Step bar | compact: "Step 2 of 4 · Admin & location" + thin progress bar | numbered circles, labels under | numbered circles, labels under, connecting line |
| Fields | one column | one column | two / three columns as in §1 |
| Review | blocks stacked | blocks stacked | two-column grid |
| Footer | buttons full width side by side; Start over above | right-aligned | right-aligned |
| Result | card full width, buttons stacked | card 560 px centred | card 560 px centred |
| Drawer | full screen | 420 px | 420 px |
| Set password | card full width, 16 px gutter | 420 px centred | 420 px centred |

## 8. Accessibility

- Step bar is an ordered list with `aria-current="step"` on the current step; done steps are buttons "Go to step 1, Company".
- Short-name status is announced (`aria-live="polite"`): "Short name available" / "Short name taken".
- Every icon button has an `aria-label` and tooltip (Copy address, Copy payment link, Open site, Resend welcome email).
- Password fields: show / hide toggle with `aria-label` "Show password"; checklist items are a live region.
- Keyboard: Tab order follows the visual order; Enter on a field = Next (except multi-line address); Esc closes dialogs and the drawer.
- Colour is never the only signal: ✓ / ✗ / ⚠ icons plus text.

## 9. Shared components

Reused: `PageHeader`, `SearchableSelect`, `ConfirmDialog`, `LoadingOverlay` (replaced by screen B for create), existing `ModuleSelectionSection` / plan cards, `CustomTooltip`, `components/money` for the plan price.
New (in `pages-sections/super-admin/company-creation/`): `onboarding-steps.tsx` (step bar), `company-step.tsx`, `admin-location-step.tsx`, `review-step.tsx`, `onboarding-progress.tsx` (screen B), `onboarding-result.tsx` (screen C). In `company-list/`: `incomplete-onboardings.tsx` (banner + drawer). Tenant: `pages-sections/sessions/page-view/set-password.tsx` + route `app/set-password/page.tsx`.
Removed / replaced: `company-details-section.tsx` and `user-details-section.tsx` are split into the step components; `package-summary-panel.tsx` kept only if the plan step still uses it.

## 10. Template impact

None on `src/theme/*` or shared components. The login flow gains one redirect (after sign-in, `mustChangePassword` → `/set-password`) and a route guard that keeps such a user on that page.

## UI acceptance criteria

1. The wizard works at 360, 600, 900 and 1920 px with no sideways page scroll; the step bar switches to its compact form below `sm`.
2. Next never moves past a step with an invalid field; the first invalid field is focused.
3. The short name shows its full address and availability before the user leaves the field.
4. Every state in §4 shows the exact text listed.
5. Creating shows each step's progress; the result never auto-opens a browser tab.
6. Incomplete onboardings are visible from the company list without searching, and Retry needs no re-typing.
7. A user with a temporary password cannot reach any page except Set your password until it's changed.
8. Theme tokens only (no hex); all actions reachable by keyboard; icons have labels.
