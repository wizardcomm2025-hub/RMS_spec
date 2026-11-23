# 01 — Tax groups

## Summary
Users with `viewlist_tax_setting` see the company's tax groups, active and inactive, with their total, components and which one is the default.

## Main flow
1. POS Configuration → Tax Setting. Loads on open.
2. Status filter: All (default) · Active · Inactive. Search by group name.
3. Columns: Tax group (name, *Default* chip beside it) · Components ("CGST 2.5% · SGST 2.5%") · Total ("5%") · Status (chip, centred) · Actions (Edit, Make default, Activate / Deactivate; right).
4. **Add tax group** in the header (with `create_tax_setting`).

## Alternative flows
Empty: "No tax groups yet. Add one to charge tax on POS bills." · No match + Clear filters: "No tax groups match these filters." · Error + Retry: "We couldn't load tax groups." · No active default: a warning banner "No default tax group: POS bills are charged no tax. Make a group the default." · No permission: "You don't have permission to perform this action."

## Acceptance criteria
1. Only the caller's company's groups; a group of another company can't be read by id (T4).
2. Inactive groups are listed with an *Inactive* chip and can be found by the filter (T6).
3. Exactly one row shows *Default* when the company has a default (Q2).
4. The total equals the sum of the active components.
5. Help text says what the screen does, with no mention of order types (T7).
6. Works at 360–1920 px with all feedback states (`ui-ux.md`).

## Open questions
None.
