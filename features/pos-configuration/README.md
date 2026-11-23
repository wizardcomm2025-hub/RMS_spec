# POS Configuration → Tax Setting & Order Channels

**Tax Setting** defines the tax groups charged on POS bills: a group (for example *GST 5%*) is made of components (*CGST 2.5%* + *SGST 2.5%*). Exactly one active group is the company's **default**, and every bill charges only that group. **Order Channels** are the tables and counters (Takeaway, Home Delivery, Swiggy / Zomato) a cashier picks when starting an order, each belonging to an outlet.

Status (2026-11-12): **Built (2026-11-12) — SQL not yet run on dev; testing deferred.** Plan: [`RMS_API/specs/pos-configuration/plan.md`](../../../RMS_API/specs/pos-configuration/plan.md). UI / UX plan: [`ui-ux.md`](./ui-ux.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `POS`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-tax-groups.md`](./01-tax-groups.md) | See tax groups with their total, status and which one is the default |
| 02 | [`02-save-tax-group.md`](./02-save-tax-group.md) | Add or edit a tax group and its components; removing a component deactivates it |
| 03 | [`03-default-and-status.md`](./03-default-and-status.md) | Make a group the default; activate or deactivate a group |
| 04 | [`04-tax-on-bills.md`](./04-tax-on-bills.md) | A POS bill charges only the default group |
| 05 | [`05-order-channels.md`](./05-order-channels.md) | List, add, edit and switch order channels (screen only; the PosPro API is unchanged) |

## Defects this feature fixes (found 2026-11-12)

| # | Defect | Fixed by |
|---|---|---|
| T1 | **Every bill adds up all active tax groups**: with *GST 5%* and *GST 18%* both active, a bill charges 23% (`SP_CreateBill`, `GetTaxDt`), and the bill records only the first group's name | [04](./04-tax-on-bills.md) |
| T2 | **Removing a component on the form doesn't remove it**: only the rows sent are updated, so the removed component stays active and keeps being charged | [02](./02-save-tax-group.md) |
| T3 | **A component name with "," or "#" breaks the save**: components are sent as one `id#name#percent#active,…` string and split on the server; a percentage of 100 or more fails the `numeric(4,2)` conversion with a raw SQL error | [02](./02-save-tax-group.md) |
| T4 | **No company check**: a group and its components are updated, and read, by id alone, so another company's tax group can be changed or read | [01](./01-tax-groups.md), [02](./02-save-tax-group.md) |
| T5 | No permission checks on the server (`[Authorize]`), raw exception text returned; the Role screen offers only *View* for Tax Setting and Order Channel View, so Create and Edit can't be granted to anyone | [01](./01-tax-groups.md), [05](./05-order-channels.md) |
| T6 | No way to deactivate a group (inactive groups vanish from the list), no duplicate-name check, no transaction (a cursor; a failure half-way leaves half the components saved) | [02](./02-save-tax-group.md), [03](./03-default-and-status.md) |
| T7 | The help texts describe features that don't exist: tax groups "apply to the order types you tick" and "the Tax group depends on the channel type" | [01](./01-tax-groups.md), [05](./05-order-channels.md) |
| C1 | Order Channels: search starts only at 3 letters; *Seats* (capacity) is required even for Takeaway or Delivery; the outlet filter is labelled "Warehouse"; switching a channel off has no confirmation; load errors are swallowed; old page design | [05](./05-order-channels.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **Both screens; Order Channels on the screen only**: Order Channels keep using the PosPro API (`NEXT_PUBLIC_POS_URL`), which isn't in this workspace, so its server-side behaviour (permissions, location checks) is unchanged | 2026-11-12 |
| Q2 | **One default group**: exactly one active group per company is the default; a POS bill charges only its active components. Other groups can stay set up without being charged | 2026-11-12 |
| Q3 | **Removing a component deactivates it**: it stops being charged and leaves the form; past bills keep their own copy (`BillTaxComponent`), so history is unchanged | 2026-11-12 |
| Q4 | **Order channels belong to Outlet-type locations** (`Warehouse.WarehouseType = 'Outlet'`), as the screen and the outlet-wise order channel script already assume | 2026-11-12 |
| D1 | Default rules: the first group a company creates becomes the default; making another group the default clears the old one in the same step; the default can't be deactivated (make another the default first); an inactive group can't be made the default | 2026-11-12 (default) |
| D2 | Existing data: a company with active groups but no default gets its **oldest active group** as the default (one-off script, after the pre-check shows which). A company with no active group charges no tax, as today | 2026-11-12 (default) |
| D3 | Group: name required, up to 50 characters, unique in the company (ignoring case). At least one component | 2026-11-12 (default) |
| D4 | Component: name required, up to 50, unique within the group; percentage 0–99.99, two decimals; the group's total must be under 100% | 2026-11-12 (default) |
| D5 | Editing a group whose name or percentages changed affects only new bills; bills already made keep their tax | 2026-11-12 (default) |
| D6 | Permissions: `viewlist_tax_setting` to see the list and a group; `create_tax_setting` to add; `edit_tax_setting` to edit, change status and set the default. The Role screen gains *Create* and *Edit* for Tax Setting and Order Channel View | 2026-11-12 (default) |
| D7 | Order Channels: *Seats* is shown and required (1 or more) only for seated types (channel type code `Table`); other types send 1, as the PosPro API expects a number. Turning a channel's *Active* off asks first; *Show online* switches without asking | 2026-11-12 (default) |
| D8 | Old `SP_AddEditTaxSetting`, `SP_GetTaxSettingList`, `SP_GetTaxSettingById`, `SP_GetTaxComponentById` stay in the database for other callers; the API stops calling them | 2026-11-12 (default) |

## Known limits
- Order Channels' server side lives in the PosPro API: it isn't permission- or location-checked by this change.
- `SP_CreateBill` keeps `numeric(4,2)` for the total tax, so a default group's total must stay under 100% (D4 enforces this when saving).
- PosPro may run its own billing code; this change fixes the two shared procedures (`SP_CreateBill`, `GetTaxDt`). If PosPro adds tax another way, that is outside this workspace.

## Out of scope
Tax by order channel type or by item; tax-inclusive prices; service charge; deleting a tax group; Order Channels' API, channel types and their prices (`ChannelTypes`); the outlet-wise order channel migration script.
