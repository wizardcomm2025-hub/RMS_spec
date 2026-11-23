# 02 — Add or edit a tax group

## Summary
Users with `create_tax_setting` add a group; with `edit_tax_setting` they change it. Components are edited in a small grid; removing one deactivates it.

## Main flow
1. **Add tax group** or **Edit** opens the standard Add / Edit dialog.
2. Fields: Group name * · components grid (Name * · Percentage * · remove) · **Add component**. The total is shown live under the grid ("Total 5%").
3. **Save** → server checks (D3, D4) and saves the group and all components in one transaction:
   - a component with no id is added;
   - a component sent with its id is updated (only if it belongs to this group);
   - an active component of the group that isn't sent is **deactivated** (Q3).
4. A new group becomes the default when the company has none (D1). Snackbar "Tax group added" / "Tax group updated".

## Alternative flows
"Group name is required" · "Use up to 50 characters" · "A tax group with this name already exists" · "Add at least one component" · "Component name is required" · "Each component needs a different name" · "Enter a percentage from 0 to 99.99" · "The total must be under 100%" · "This tax group was not found" (deleted or another company's) · Closing with changes: "Discard changes?" · No permission: "You don't have permission to perform this action."

## Acceptance criteria
1. A removed component is inactive after saving, stops being charged and doesn't reappear on the form (T2, Q3).
2. Names with "," or "#" save correctly; a percentage of 100 is refused with the message, not a SQL error (T3).
3. The save is all-or-nothing (T6); a duplicate group name is refused, ignoring case (D3).
4. A group id or component id of another company or group is refused: "This tax group was not found" (T4).
5. Bills already made keep their tax after an edit (D5).

## Open questions
None.
