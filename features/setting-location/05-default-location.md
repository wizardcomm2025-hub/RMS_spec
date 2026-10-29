# 05 — Set the default location

## Summary

A company can mark one location as its Default Location, which other screens can preselect. Setting Default on one location removes it from every other location of the same company, and never from another company's locations. Default is optional, and only an active location can be default.

## Prerequisites

- **Backend.** `SaveWareHouseDetails` (`IsDefault`), `ChangeWareHouseStatus` (clears Default on deactivate).
- **Data.** `Sp_SaveWareHouseDetails` clears `IsDefault` only within the same `CompanyId`.
- **Depends on.** [02](./02-add-location.md), [03](./03-edit-location.md), [04](./04-switch-location-on-off.md) — Default is a field of their save and status flows; no separate endpoint.

## Actors & preconditions

- **Actor:** a company user with `Location.Create` (on add) or `Location.Edit` (on edit).
- **Trigger:** ticking "Default Location" in the Add / Edit dialog and saving.
- **Preconditions:** the location is active.

## Main flow

1. The user ticks Default Location in the dialog and saves.
2. The system sets `IsDefault = 1` on this location.
3. The system sets `IsDefault = 0` on every other location of the same company.
4. The list shows the "Default" badge on this location only.

## Alternative flows

- **Untick.** The company can have no default.
- **Inactive location.** The checkbox is disabled with "Activate the location to make it default".
- **Default deactivated.** See [04](./04-switch-location-on-off.md): Default is removed.
- **Direct call with `IsDefault = true` for an inactive location.** The save is refused with "Activate the location to make it default"; nothing changes, and the current default keeps its flag (LOC-Q3).

## Acceptance criteria

1. Given A is default, when Default is set on B and saved, then only B shows the "Default" badge and only B has `IsDefault = 1` in CO-A (old AC-6).
2. Given CO-B has a default location, when CO-A changes its default, then CO-B's default is unchanged (old AC-6).
3. Given Default is unticked on the default location, then the company has no default and nothing else changes.
4. Given an inactive location, then the Default checkbox is disabled with the hint.
5. Given any sequence of saves, then CO-A never has more than one location with `IsDefault = 1`.
6. Given a direct save of an inactive location with Default set, then "Activate the location to make it default" and nothing changes (LOC-Q3).

## Platform notes

### RMS API

- No own endpoint; `IsDefault` field of `SaveWareHouseDetails`.

### Data — Tenant DB

- `Sp_SaveWareHouseDetails`: when the saved location is default, clears `IsDefault` on every other location with the same `CompanyId`, and on no other row.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Checkbox with hint in the Settings section; "Default" badge on rows and phone cards.

## Configuration

None.

## Out of scope

- Which other screens preselect the default — owned by those screens.

## Open questions

None open. LOC-Q3 resolved 2026-10-28.
