# UI / UX — Transfer Report

> Per `RMS_API/specs/UI_UX_STANDARDS.md` and the list recipe in §12. Text the user sees is written verbatim; tester cases quote it. Theme tokens only, no hex.

## 1. Page anatomy
```
┌──────────────────────────────────────────────────────────────────────────────────┐
│ Report › Transfer Report                                                          │
│ Transfer Report                                        [↻]  [⭳ Export CSV]        │
│ ⓘ About this page ▾                                                               │
├──────────────────────────────────────────────────────────────────────────────────┤
│  All (42) │ In transit (5) │ Received (37) │ With shortage (3)      (status tabs) │
├──────────────────────────────────────────────────────────────────────────────────┤
│ [📅 28/Oct/2026 – 03/Nov/2026 ▾] [🔍 Search transfer, requisition or item…] [Location ▾] │
├──────────────────────────────────────────────────────────────────────────────────┤
│ 42 transfers · sent ₹1,84,200.00 · short ₹2,350.00                                │
│    TRANSFER      DATE      FROM → TO         SENT BY  RECEIVED BY  STATUS  SENT  SHORT │
│ ▸  TRF/26-27/18  03/Nov    Kitchen → Bar     Asha     Ravi         Received 4,200 350  │
│    REQ/26-27/40                                       03/Nov 06:30                │
│ ▾  TRF/26-27/17  02/Nov    Store → Kitchen   Asha     —            In transit 900  —   │
│      ITEM        SENT      RECEIVED   SHORT    SENT VALUE  SHORT VALUE  REASON    │
│      Onion       10 kg     —          —        400.00      —            —         │
│      Tomato      12.5 kg   —          —        500.00      —            —         │
├──────────────────────────────────────────────────────────────────────────────────┤
│ 1–25 of 42 transfers                                   25 / page ▾   ‹ 1 2 › │
└──────────────────────────────────────────────────────────────────────────────────┘
```
- `PageHeader`: breadcrumb "Report › Transfer Report", no description. About: "Stock moved between your locations through Inbound Requisition, and what was confirmed in Item Receive. Short means less arrived than was sent. Values use today's cost." (links on "Inbound Requisition" and "Item Receive").
- Refresh and **Export CSV** on the right of the header; a full-width row on phones.

## 2. Columns
| Column | Align | Content |
|---|---|---|
| ▸ | center | Expand / collapse item lines |
| Transfer | left | Number bold; requisition number as a caption |
| Date | left | Company date format |
| From → To | left | "Kitchen → Bar" |
| Sent by | left | User name; "—" if unknown |
| Received by | left | User name, received date and time as a caption; "—" until received |
| Status | center | Chip: *In transit* info, *Received* success; a warning *Short* chip beside it when a line is short; other statuses plain |
| Sent value | right | `Money`, sum of the lines; "—" if no line can be valued |
| Short value | right | `Money`, bold, warning colour when above 0; "—" until received or when nothing is short |

Line rows (indented, subtle background): Item (alias caption) · Sent "10 kg" · Received · Short ("2 kg" warning; "—" if none or not received; "2 kg over" for a legacy over-receipt) · Sent value · Short value · Reason (one line, ellipsis, tooltip).

Fits at 1280 px with no sideways scroll; below `md` it becomes phone cards.

## 3. Filters
| Filter | Behaviour |
|---|---|
| Status tabs | All / In transit / Received / With shortage, each with its count under the other filters |
| Date range | `DateRangePicker`, draft + **Apply**; default last 7 days; up to 366 days |
| Search | `ListSearch`, "Search transfer, requisition or item...", 300 ms debounce |
| Location | `SearchableSelect`, "All my locations" + the user's locations; matches either end |

Changing a filter returns to page 1. Default 25 transfers per page. **Clear filters** shows when any filter differs from its default.

## 4. Feedback states
| State | Behaviour (exact text) |
|---|---|
| First load | Table skeleton rows |
| Refresh / paging | Thin progress bar above the table; rows stay |
| Empty (default filters) | "No transfers in this period." |
| No match | "No transfers match these filters." + **Clear filters** |
| Load error | "We couldn't load the report." + **Retry** |
| No location | "You aren't assigned to any location. Ask an admin to add you in User List." |
| No permission | "You don't have permission to perform this action." in place of the table |
| Exporting | Spinner in the button, disabled until done |
| Export empty | Snackbar "Nothing to export for these filters." |
| Export error | Snackbar "Couldn't export the report. Please try again." |
| Range too long | Snackbar "Choose a period of up to one year." |

## 5. Confirmations
None: nothing on this page changes data.

## 6. Permissions
`view_transfer_report` for the page and the export; checked on the server too.

## 7. Responsive
| | xs (360) | sm (600) | md+ (900–1920) |
|---|---|---|---|
| Header | Title, buttons full-width beneath | Buttons right | Buttons right |
| Tabs | Scroll sideways inside the tab bar | Same | All visible |
| Filters | Stacked | Two columns | One row from `lg` |
| List | Card: number + status chips; "Kitchen → Bar"; date · sent by; sent / short values; **Show items** toggles the lines | Cards | Table with expand |
| Footer | Compact pager | Standard | Standard |

## 8. Accessibility
Expand buttons have `aria-expanded` and `aria-label` "Show items of TRF/26-27/18"; tabs are a labelled `tablist`; status has text, not colour alone; the reason tooltip is reachable by keyboard; loading and errors use `role="status"`.

## 9. Shared components
`PageHeader`, `ListSearch`, `DateRangePicker`, `SearchableSelect`, `TableHeader`, `TablePagination`, `Money`, `listPageSx`, `listFilterBarSx`, `listFooterSx`, `CustomTooltip`.
New (in `report/transfer-report/`): `transfer-report-row.tsx` (row + lines, phone card, headings, grouping). `page-view/transfer-report.tsx` is rebuilt. Removed: `transfer-row.tsx`, `transfer-table.tsx`, `table-heading.ts`, `utils.ts`.

## 10. Template impact
None on `src/theme/*` or shared components.

## UI acceptance criteria
1. No sideways page scroll from 360 to 1920 px; cards below `md`.
2. Every state in §4 shows the exact text.
3. Dates and times follow the company format.
4. Status chips are centred, numbers right-aligned.
5. Theme tokens only; every icon button has a label.
