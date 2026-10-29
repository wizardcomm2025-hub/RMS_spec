# 04 — Switch a reason of wastage on or off

## Summary

Instead of deleting a reason, a user with `edit_reason_of_wastage` switches it Inactive. An inactive reason can't be picked in Wastage Entry any more, but past wastage keeps it and the Wastage Report still shows it. Switching off is always allowed (Q3). It can be switched back on at any time.

## Prerequisites

- **Backend.** `POST /Setting/WestageReasonActive` fixed: permission, company check, "not found" (see [`api-contract.md`](./api-contract.md#change-status)); `DeleteWastageById` blocked.
- **Data.** `SP_WestageReasonActive` changed.
- **Depends on.** [01](./01-view-and-search-reasons.md).

## Actors & preconditions

- **Actor:** a company user with `edit_reason_of_wastage`.
- **Trigger:** the Active switch on a row or phone card.

## Main flow

1. The user turns the switch off on an active reason.
2. A confirmation opens: title "Deactivate reason", text "Deactivate **<text>**? Staff won't be able to pick it in Wastage. Past wastage keeps this reason. You can activate it again at any time." — Cancel · Deactivate.
3. The user clicks Deactivate.
4. The system sets `IsActive = 0` and records `UpdatedBy` / `UpdatedOn` (UTC).
5. The toast "Reason deactivated" appears; the row shows "Inactive".
6. To reactivate: switch on, confirm "Activate **<text>**?" with Activate, toast "Reason activated".

## Alternative flows

- **Cancel.** Nothing changes; the switch returns.
- **Already in that state.** Succeeds; no harm.
- **Not found.** Unknown, deleted or another company's id: "Reason not found" (D6).
- **Last active reason.** Allowed; Wastage Entry then offers no reason until one is added or reactivated (the quick-add still works).
- **No delete.** None in the UI; a direct call to `DeleteWastageById` returns "Reasons cannot be deleted. Deactivate the reason instead." and writes nothing (D7).
- **No permission (direct call).** "You don't have permission to perform this action."
- **Server failure.** Toast with the message; the switch returns (D6).

## Acceptance criteria

1. Given an active reason used by past wastage, when switched off and confirmed, then "Inactive" here, `IsActive = 0`, `IsDelete = 0`, and past wastage lines still show it in the Wastage Report.
2. Given it is inactive, then Wastage Entry's reason dropdown doesn't offer it (D1).
3. Given a Wastage Entry page already open with the reason loaded, then after a reload it is gone from the dropdown.
4. Given the user cancels, then nothing changes.
5. Given a user without `edit_reason_of_wastage`, then the switch is hidden and a direct call returns the permission message.
6. Given a CO-B id or a deleted id, then "Reason not found" and nothing changes (D6).
7. Given any caller, when `DeleteWastageById` is called, then the refusal message and no row changes (D7).
8. Given the call fails, then the switch shows the old state.

## Platform notes

### RMS API

- `POST /Setting/WestageReasonActive` `{ reasonMasterId, isActive }` — `[CompanyAuthorize]`, `edit_reason_of_wastage`, company and user from the token.
- `POST /Setting/DeleteWastageById` — always refuses.

### Data — Tenant DB

- Updates `WastageReasonMaster` only.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Switch, chip and confirmations: [`ui-ux.md`](./ui-ux.md#5-confirmations).

## Configuration

None.

## Out of scope

- Refusing an inactive reason inside the Wastage save (`SP_AddEditWastage`): a direct API call to save wastage can still send an inactive reason id. Recorded as a follow-up for Stock → Wastage.

## Open questions

None.
