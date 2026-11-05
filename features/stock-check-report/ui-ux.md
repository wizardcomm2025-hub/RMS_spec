# UI / UX — Stock Check Report

> Per `RMS_API/specs/UI_UX_STANDARDS.md` and the list recipe in §12. Text the user sees is written verbatim; tester cases quote it. Theme tokens only, no hex.

## 1. Page anatomy
```
┌──────────────────────────────────────────────────────────────────────────────────┐
│ Report › Stock Check Report                                                       │
│ Stock Check Report                                     [↻]  [⭳ Export CSV]        │
│ ⓘ About this page ▾                                                               │
├──────────────────────────────────────────────────────────────────────────────────┤
│  All 124 │ Raw Material 96 │ Finished Goods 28                       (type tabs)  │
├──────────────────────────────────────────────────────────────────────────────────┤
│ [🔍 Search item…      ] [📅 28/Oct/2026 – 03/Nov/2026 ▾] [Location: All my locations ▾] │
├──────────────────────────────────────────────────────────────────────────────────┤
│ 124 counts · counted value ₹4,52,310.50                                           │
│ COUNTED AT      ITEM        TYPE      LOCATION  COUNTED QTY  VALUE ₹   BY    NOTE │
│ 03/Nov/2026     Onion       Raw Mat.  Kitchen       12.5 kg  500.00    Asha  Rot… │
│ 06:30 PM        pyaz                                                              │
│ 03/Nov/2026     Tomato      Raw Mat.  Kitchen          0 kg    0.00    Asha  —    │
│ 10:12 AM                                                                          │
├──────────────────────────────────────────────────────────────────────────────────┤
│ 1–25 of 124 counts                                     25 / page ▾   ‹ 1 2 3 › │
└──────────────────────────────────────────────────────────────────────────────────┘
```
- `PageHeader`: breadcrumb "Report › Stock Check Report", no description. About: "One row for every item counted in Stock Check. Use the Stock Report for what's in stock now and the Opening Stock Report for how it moved." (links on the three names).
- Refresh and **Export CSV** on the right of the header; a full-width row on phones.

## 2. Columns
| Column | Align | Content |
|---|---|---|
| Counted at | left | Company date format; time with AM / PM beneath, as a caption |
| Item | left | Name bold; alias caption beneath |
| Type | left | Inventory type |
| Location | left | Location name |
| Counted qty | right | "12.5 kg", up to 3 decimals; 0 shows as "0 kg" |
| Value ₹ | right | `Money` in the company currency, bold; "—" for an unknown unit |
| Counted by | left | User name; "—" if the user no longer exists |
| Note | left | One line with ellipsis, full text in a tooltip; "—" when empty |

No Actions column: the report is read-only. Fits at 1280 px with no sideways scroll; below `md` it becomes phone cards.

## 3. Filters
| Filter | Behaviour |
|---|---|
| Type tabs | All + one per inventory type, each with its count under the other filters (Recipe / Stock Report pattern) |
| Search | `ListSearch`, item name / alias, 300 ms debounce |
| Date range | `DateRangePicker`, draft + **Apply**; default last 7 days; up to 366 days |
| Location | `SearchableSelect`, "All my locations" + the user's locations |

Changing a filter returns to page 1. Default 25 rows per page.

## 4. Feedback states
| State | Behaviour (exact text) |
|---|---|
| First load | Table skeleton rows |
| Refresh / paging | Thin progress bar above the table; rows stay |
| Empty (default filters) | "No stock checks in this period." |
| No match | "No counts match these filters." + **Clear filters** |
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
`view_stock_check_report` for the page and the export; checked on the server too.

## 7. Responsive
| | xs (360) | sm (600) | md+ (900–1920) |
|---|---|---|---|
| Header | Title, buttons full-width beneath | Buttons right | Buttons right |
| Tabs | Scroll sideways inside the tab bar | Same | All visible |
| Filters | Stacked | Two columns | One row from `lg` |
| List | Cards: item + value; "12.5 kg · Kitchen"; "03/Nov/2026 06:30 PM · Asha"; note if any | Cards | Table |
| Footer | Compact pager | Standard | Standard |

## 8. Accessibility
Table headers are real `th`; tabs are a labelled `tablist`; the refresh and export buttons have `aria-label`s; the note tooltip is reachable by keyboard; status changes (loading, errors) use `role="status"` / `role="alert"`.

## 9. Shared components
`PageHeader`, `ListSearch`, `DateRangePicker`, `SearchableSelect`, `TableHeader`, `TablePagination`, `Money`, `listPageSx`, `listFilterBarSx`, `listFooterSx`, `CustomTooltip`.
New (in `report/stock-check-report/`): `stock-check-report-row.tsx` (row, phone card, headings). `page-view/stock-check-report.tsx` is rebuilt. Removed: `stock-check-row.tsx`, `stock-check-table.tsx`, `table-heading.ts`, `utils.ts`.

## 10. Template impact
None on `src/theme/*` or shared components.

## UI acceptance criteria
1. No sideways page scroll from 360 to 1920 px; cards below `md`.
2. Every state in §4 shows the exact text.
3. Dates and times follow the company format, with AM / PM.
4. Numbers are right-aligned; there are no badges or row actions on this page.
5. Theme tokens only; every icon button has a label.
