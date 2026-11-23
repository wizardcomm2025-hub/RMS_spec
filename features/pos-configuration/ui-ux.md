# UI / UX — POS Configuration

> Per `RMS_API/specs/UI_UX_STANDARDS.md` and the list recipe in §12 (Setting → Location is the reference). Text the user sees is written verbatim; tester cases quote it. Theme tokens only, no hex.

## 1. Page anatomy

### Tax Setting
```
┌──────────────────────────────────────────────────────────────────────────────────┐
│ POS Configuration › Tax Setting                                                   │
│ Tax Setting                                         [↻]  [+ Add tax group]        │
│ ⓘ About this page ▾                                                               │
├──────────────────────────────────────────────────────────────────────────────────┤
│ ⚠ No default tax group: POS bills are charged no tax. Make a group the default.  │ (only then)
├──────────────────────────────────────────────────────────────────────────────────┤
│ [🔍 Search tax groups…            ]  [Status: All ▾]                              │
├──────────────────────────────────────────────────────────────────────────────────┤
│ TAX GROUP              COMPONENTS                  TOTAL   STATUS     ACTIONS     │
│ GST 5%  [Default]      CGST 2.5% · SGST 2.5%          5%   Active   ✎  ⋮         │
│ GST 18%                CGST 9% · SGST 9%             18%   Active   ✎  ⋮         │
│ VAT 12%                VAT 12%                       12%   Inactive ✎  ⋮         │
├──────────────────────────────────────────────────────────────────────────────────┤
│ 1–3 of 3 tax groups                                     10 / page ▾   ‹ 1 ›       │
└──────────────────────────────────────────────────────────────────────────────────┘
```
⋮ menu: **Make default** (active, not default) · **Deactivate** (active, not default; for the default it's disabled with the tooltip "Make another group the default first") · **Activate** (inactive).

Add / Edit dialog (standard):
```
┌ Add tax group ───────────────────────────────────────────── ✕ ┐
│ Group name *  [GST 5%                                  ]      │
│ COMPONENTS                                                    │
│ NAME *                         PERCENTAGE *                   │
│ [CGST                     ]    [ 2.50 ] %          🗑         │
│ [SGST                     ]    [ 2.50 ] %          🗑         │
│ [+ Add component]                             Total 5%        │
│                                         [Cancel]  [Save]      │
└───────────────────────────────────────────────────────────────┘
```
- About: "Tax groups charged on POS bills. A group is made of components, for example GST 5% = CGST 2.5% + SGST 2.5%. Every bill charges only the Default group; other groups can stay set up for later."

### Order Channels
```
┌──────────────────────────────────────────────────────────────────────────────────┐
│ POS Configuration › Order Channels                                                │
│ Order Channels                                      [↻]  [+ Add channel]          │
├──────────────────────────────────────────────────────────────────────────────────┤
│ [🔍 Search channels…            ]  [Outlet: All outlets ▾]                        │
├──────────────────────────────────────────────────────────────────────────────────┤
│ NAME        TYPE           OUTLET     SEATS   ACTIVE   SHOW ONLINE   ACTIONS      │
│ Table 5     Table          Main Hall      4    (●)        (●)          ✎         │
│ Takeaway    Takeaway       Main Hall      —    (●)        ( )          ✎         │
├──────────────────────────────────────────────────────────────────────────────────┤
│ 1–10 of 24 channels                                         ‹ 1 2 3 ›             │
└──────────────────────────────────────────────────────────────────────────────────┘
```
- About: "Tables and counters cashiers pick when they start an order on the POS. Each belongs to an Outlet-type Location. Seats apply to tables only. A channel turned off disappears from the POS." (link on "Location").
- Page title changes from "Order Channel View" to "Order Channels"; the menu item keeps its route.

## 2. Columns and fields
| Screen | Column / field | Align / rule |
|---|---|---|
| Tax | Tax group | left; *Default* chip (info) beside the name |
| Tax | Components | left; "CGST 2.5% · SGST 2.5%", ellipsis with tooltip |
| Tax | Total | right; up to 2 decimals, "%" |
| Tax | Status | center; *Active* success / *Inactive* default chip |
| Tax | Actions | right; Edit + ⋮ menu |
| Tax form | Group name | required, max 50 |
| Tax form | Component name / Percentage | required, max 50, unique in the group / 0–99.99, 2 decimals; total under 100 |
| Channels | Name · Type · Outlet | left |
| Channels | Seats | right; "—" for non-seated types |
| Channels | Active · Show online | center; switches |
| Channels | Actions | right; Edit |
| Channel form | Channel type * · Outlet * (`SearchableSelect`) · Name * (max 50) · Seats * (seated types, 1–999) · Active · Show online · Description (max 250) | |

## 3. Filters
Tax: search (name, 300 ms, from the first letter) · Status (All / Active / Inactive); client-side over the company's groups (a company has few). Channels: search (server, 300 ms, from the first letter) · Outlet. **Clear filters** when any is set.

## 4. Feedback states
| State | Tax Setting | Order Channels |
|---|---|---|
| First load | Skeleton rows | Skeleton rows |
| Refresh | Thin progress bar | Thin progress bar |
| Empty | "No tax groups yet. Add one to charge tax on POS bills." | "No order channels yet." |
| No match | "No tax groups match these filters." + Clear filters | "No order channels match these filters." + Clear filters |
| Load error | "We couldn't load tax groups." + Retry | "We couldn't load order channels." + Retry |
| No default | Warning banner (above) | — |
| No outlets | — | Info banner "Add an Outlet-type location in Setting → Location to create tables." |
| Saving | Save button spinner, dialog locked | Same |
| Save success | "Tax group added" / "Tax group updated" | "Order channel added" / "Order channel updated" |
| Save error | Server message under the field or in an alert in the dialog | API message, else "Couldn't save the order channel. Please try again." |
| Status / default | "GST 18% is now the default" · "GST 12% deactivated" · "GST 12% activated" | Switch reverts on failure with the error snackbar |
| No permission | Add / Edit / ⋮ hidden; list message "You don't have permission to perform this action." if the list is refused | Add / Edit hidden |

## 5. Confirmations
| Trigger | Title | Body | Buttons |
|---|---|---|---|
| Make default | Make GST 18% the default? | New POS bills will charge GST 18% instead of GST 5%. | Cancel · Make default |
| Deactivate group | Deactivate GST 12%? | It won't be charged and can't be the default until it's active again. | Cancel · Deactivate |
| Close dialog with changes | Discard changes? | What you typed will be lost. | Keep editing · Discard |
| Channel Active off | Turn off Table 5? | Cashiers won't see it on the POS. | Cancel · Turn off |

## 6. Permissions
Tax: `viewlist_tax_setting` list; `create_tax_setting` Add; `edit_tax_setting` Edit, status, default. Channels: `create_order_channel_view` Add; `edit_order_channel_view` Edit and switches. Role screen: Tax Setting and Order Channel View gain *Create* and *Edit* next to *View*.

## 7. Responsive
| | xs (360) | sm (600) | md+ (900–1920) |
|---|---|---|---|
| Header | Title; buttons full-width beneath | Buttons right | Buttons right |
| Filters | Stacked | Two columns | One row |
| Tax list | Card: name + Default chip; components; total · status; actions row | Cards | Table |
| Channel list | Card: name + type; outlet · seats; two switches with labels; Edit | Cards | Table |
| Dialogs | Full screen | 600 px | 600 px |

## 8. Accessibility
Switches have `aria-label` ("Active, Table 5", "Show online, Table 5"); the ⋮ button is "More actions for GST 5%"; the components grid has labelled inputs ("Component 1 name", "Component 1 percentage"); errors are tied to their fields; loading and errors use `role="status"`.

## 9. Shared components
`PageHeader`, `ListSearch`, `SearchableSelect`, `TableHeader`, `TablePagination`, `ConfirmDialog`-style dialogs, `ActionButtons`, `BazaarSwitch`, `listPageSx`, `listFilterBarSx`, `listFooterSx`, `InfoBanner`.
Rebuilt: `tax-setting/page-view/tax-setting.tsx`, `tax-setting-row.tsx`, `tax-setting-modal.tsx`, `order-channel-view/page-view/order-channel-view.tsx`, `order-channel-view-row.tsx`, `order-channel-view-modal.tsx`. Dialog headers move from the dark bar to the standard dialog header.

## 10. Template impact
None on `src/theme/*` or shared components. `role-permissions.ts` gains the two actions.

## UI acceptance criteria
1. No sideways page scroll from 360 to 1920 px; cards below `md`.
2. Every state in §4 shows the exact text.
3. Badges centred, Actions right, numbers right.
4. Theme tokens only (no dark hard-coded dialog header); every icon button has a label.
