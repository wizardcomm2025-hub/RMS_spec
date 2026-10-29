# 04 — Switch Driver On / Off

## User story

As an operations manager,
I want to deactivate a driver when they leave or are temporarily unavailable,
So that they cannot be chosen for dispatches, without breaking historical transfer records.

---

## Acceptance criteria

1. **Active Switch Display**:
   - Each table row and mobile card has an Active switch.
   - Switch is checked (`true`) for active drivers and unchecked (`false`) for inactive drivers.
   - Tooltip displays "Deactivate" when active, "Activate" when inactive.
2. **Deactivation Confirmation**:
   - Clicking an active switch opens confirmation dialog:
     - Title: "Deactivate driver"
     - Description: "Deactivate **<driverName>**? Staff won't be able to assign this driver to delivery or stock transfers. Past transfers keep this driver. You can activate them again at any time."
     - Actions: Cancel · **Deactivate** (red/warning styling).
3. **Activation Confirmation**:
   - Clicking an inactive switch opens confirmation dialog:
     - Title: "Activate driver"
     - Description: "Activate **<driverName>**?"
     - Actions: Cancel · **Activate** (primary/success styling).
4. **Execution & Rollback**:
   - Confirming sends `POST /Setting/ChangeDriverStatus` `{ driverId, isActive }` (new; needs `edit_driver_master`; company from the token; another company's id → "Driver not found"). Name, licence and photo are never re-sent (D7).
   - An inactive driver is not offered in Inbound Requisition's dispatch dialog (D6); past transfers keep showing them.
   - On success: Toast displays "Driver deactivated" or "Driver activated", switch state flips, and list refreshes.
   - On failure: Toast displays the API error message, modal closes, and switch returns to original state.
5. **No Hard Delete**:
   - The UI provides no Delete button or delete action.
   - A direct call to `POST /Setting/DeleteDriver` returns "Drivers cannot be deleted. Deactivate the driver instead." and writes nothing.
