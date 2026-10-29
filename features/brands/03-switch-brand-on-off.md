# 03 — Switch a brand on or off

## Summary

A user with `edit_brand` switches a brand off instead of deleting it. It's refused while the brand has active sub-brands or active raw materials (Q1).

## Actors & preconditions

- **Actor:** a logged-in company user; permissions per step below.

## Main flow

1. Switch off → "Deactivate brand": "Deactivate **<name>**? It can't be picked for raw materials any more. You can activate it again at any time." — Cancel · Deactivate.
2. Checks, then `IsActive = 0`, `UpdatedBy/At`.
3. "Brand deactivated". Switch on → "Activate **<name>**?" → "Brand activated".

## Alternative flows

- "This brand has active sub-brands. Switch them off first." · "This brand is used by active raw materials. Move them first." (D5).
- "Brand not found" for unknown / another company's id.
- A direct `DeleteStockType` call → "Brands cannot be deleted. Deactivate the brand instead.", nothing changes.
- "You don't have permission to perform this action."

## Acceptance criteria

1. A brand with no active sub-brands and no active raw materials → Inactive.
2. With an active sub-brand → the sub-brands message; with an active raw material → the items message.
3. Inactive brand isn't offered on Raw Material.
4. CO-B id → not found. Delete refused.

## Platform notes

### RMS API

- `POST /Setting/ChangeStockTypeStatus` (new) — `edit_brand`. `POST /Setting/DeleteStockType` — always refuses.

### Data — Tenant DB

- New `SP_ChangeStockTypeStatus`.

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
