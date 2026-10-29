# 06 — Switch a sub-brand on or off

## Summary

A user with `edit_sub_brand` switches a sub-brand off, refused while active raw materials use it; switching on needs its brand to be on (Q2).

## Actors & preconditions

- **Actor:** a logged-in company user; permissions per step below.

## Main flow

1. Switch off → "Deactivate sub-brand": "Deactivate **<name>**? It can't be picked for raw materials any more. You can activate it again at any time."
2. Checks → `IsActive = 0`; "Sub-brand deactivated". Switch on → "Activate **<name>**?" → "Sub-brand activated".

## Alternative flows

- "This sub-brand is used by active raw materials. Move them first." · "Switch its brand on first." · "Sub-brand not found".
- Direct `DeleteStockSubType` → "Sub-brands cannot be deleted. Deactivate the sub-brand instead.".
- "You don't have permission to perform this action."

## Acceptance criteria

1. Unused sub-brand → Inactive, still listed (D3), can be switched back on.
2. Used by an active raw material → the items message.
3. Switch on under an inactive brand → "Switch its brand on first."
4. CO-B id → not found; delete refused.

## Platform notes

### RMS API

- `POST /Setting/ChangeStockSubTypeStatus` (new) — `edit_sub_brand`. `POST /Setting/DeleteStockSubType` — always refuses.

### Data — Tenant DB

- New `SP_ChangeStockSubTypeStatus`.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

See [`ui-ux.md`](./ui-ux.md).

## Configuration

None.

## Out of scope

None.

## Open questions

None.
