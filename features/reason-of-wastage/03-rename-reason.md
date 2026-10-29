# 03 — Rename a reason of wastage

## Summary

A company user with `edit_reason_of_wastage` changes a reason's text. Past wastage lines point at the reason by id, so they — and the Wastage Report — show the new text. The save changes the text only; status is changed with the switch ([04](./04-switch-reason-on-off.md)).

## Prerequisites

- **Backend.** `GetWestageReasonById` returns a non-deleted reason of the company with an edit token; `AddEditResonMaster` refuses unknown ids and stale saves (see [`api-contract.md`](./api-contract.md#save-reason)).
- **Data.** `SP_GetWestageReasonById`, `SP_AddEditResonMaster` changes.
- **Depends on.** [02](./02-add-reason.md).

## Actors & preconditions

- **Actor:** a company user with `edit_reason_of_wastage`.
- **Trigger:** the Edit icon on a row or phone card.

## Main flow

1. The user clicks Edit; the system loads the reason (overlay while loading).
2. The dialog "Edit Reason — <text>" opens with the text filled in.
3. The user changes the text and clicks "Save changes".
4. The system validates as in [02](./02-add-reason.md), checks the reason still exists in the company and the edit token still matches, and updates `Description`, `UpdatedBy`, `UpdatedOn` (UTC). `IsActive` is not touched.
5. The dialog closes, "Reason updated" appears and the list refreshes.

## Alternative flows

- **Validation / duplicate.** Same messages as [02](./02-add-reason.md); its own current text never counts as a duplicate.
- **Inactive reason.** Can be renamed; stays Inactive.
- **Not found.** Unknown, deleted or another company's id — on load or save: "Reason not found"; the list refreshes (D5).
- **Changed by someone else.** The edit token differs, or is missing: "This reason was changed by someone else. Reload and try again."; nothing saved.
- **No permission (direct call).** "You don't have permission to perform this action."
- **Unchanged text.** Save allowed; same result.
- **Discard / failure.** As in [02](./02-add-reason.md).

## Acceptance criteria

1. Given "Expired", when renamed to "Expired (past use-by)", then "Reason updated", the list and the Wastage Report show the new text.
2. Given "Spoiled" exists, when "Expired" is renamed to " spoiled ", then "Reason already exists" and nothing changes.
3. Given an inactive reason, when renamed, then it stays Inactive.
4. Given the body sends `IsActive = false` for an active reason, then the text changes and it stays Active (D5).
5. Given a CO-B id, a deleted id or id 999999, then "Reason not found" and nothing changes (D5).
6. Given A-ADMIN2 saved the reason after A-ADMIN opened it, when A-ADMIN saves, then the stale message; given no `UpdatedOn` in the body, then the stale message.
7. Given a user without `edit_reason_of_wastage`, then no Edit icon and a direct call returns the permission message.

## Platform notes

### RMS API

- `POST /Setting/GetWestageReasonById` — `[CompanyAuthorize]`. `POST /Setting/AddEditResonMaster` with `ReasonMasterId > 0` — `[CompanyAuthorize]`, `edit_reason_of_wastage`, `UpdatedOn` required.

### Data — Tenant DB

- Updates `WastageReasonMaster.Description`, `UpdatedBy`, `UpdatedOn` under the per-company lock.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Dialog: [`ui-ux.md`](./ui-ux.md#3-dialog--form).

## Configuration

None.

## Out of scope

- Keeping a text history on past wastage lines (they follow the reason by id).

## Open questions

None.
