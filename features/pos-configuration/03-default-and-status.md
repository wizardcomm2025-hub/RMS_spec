# 03 — Default group and status

## Summary
Users with `edit_tax_setting` choose which active group is the default, and activate or deactivate groups.

## Main flow
1. **Make default** on an active, non-default group → confirm "Make GST 18% the default? New POS bills will charge GST 18% instead of GST 5%." → the old default is cleared and the new one set in one step. Snackbar "GST 18% is now the default".
2. **Deactivate** on a non-default active group → confirm "Deactivate GST 12%? It won't be charged and can't be the default until it's active again." → Snackbar "GST 12% deactivated".
3. **Activate** on an inactive group → no confirmation → Snackbar "GST 12% activated".

## Alternative flows
Deactivating the default: the action is disabled with the tooltip "Make another group the default first"; the server also refuses: "The default tax group can't be deactivated. Make another group the default first." · Making an inactive group the default: refused, "Activate this tax group first." · Not found / other company: "This tax group was not found" · No permission: "You don't have permission to perform this action."

## Acceptance criteria
1. After any change, at most one group per company is the default, and it is active (D1).
2. The default can't be deactivated, on the screen or through the API.
3. A company with active groups but no default after the migration is listed by the pre-check (D2).

## Open questions
None.
