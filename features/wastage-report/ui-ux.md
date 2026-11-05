# UI / UX — Wastage Report

> Per `RMS_API/specs/UI_UX_STANDARDS.md` and the list recipe in §12. Text the user sees is written verbatim; tester cases quote it. Theme tokens only, no hex.

## 1. Page anatomy
```
┌──────────────────────────────────────────────────────────────────────────────────┐
│ Report › Wastage Report                                                           │
│ Wastage Report                                         [↻]  [⭳ Export CSV]        │
│ ⓘ About this page ▾                                                               │
├──────────────────────────────────────────────────────────────────────────────────┤
│  All (58) │ Raw Material (51) │ Finished Goods (7)                    (type tabs) │
├──────────────────────────────────────────────────────────────────────────────────┤
│ [📅 28/Oct/2026 – 03/Nov/2026 ▾] [🔍 Search item…] [Location ▾] [Reason ▾]        │
├──────────────────────────────────────────────────────────────────────────────────┤
│ 58 entries · wastage value ₹12,480.50                                             │
│ ┌ card ───────────────────────────────────────────────────────────────────────┐  │
│ │ [ Entries | By item | By reason ]                                            │  │
│ │ RECORDED AT   ITEM     TYPE     LOCATION  QUANTITY  REASON    VALUE  BY      │  │
│ │ 03/Nov/2026   Onion    Raw Mat. Kitchen     2.5 kg  Spoiled  100.00  Asha    │  │
│ │ 06:30 PM      pyaz                                                           │  │
│ ├──────────────────────────────────────────────────────────────────────────────┤  │
│ │ 1–25 of 58 entries                                 25 / page ▾   ‹ 1 2 3 ›   │  │
│ └──────────────────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────────────────┘
```
By item:
```
│ ITEM         TYPE        QUANTITY   ENTRIES     VALUE     SHARE │
│ Paneer       Raw Mat.     12.5 kg         6   3,750.00      30% │
│ Onion        Raw Mat.       40 kg         9   1,600.00      13% │
```
By reason:
```
│ REASON            ENTRIES   ITEMS       VALUE     SHARE │
│ Spoiled                21      14    6,240.25       50% │
│ Spilled                 9       5    2,100.00       17% │
│ No reason               2       2      120.00        1% │
```
- `PageHeader`: breadcrumb "Report › Wastage Report", no description. About: "What was recorded in Wastage, why, and what it cost at today's cost. Use By reason and By item to find recurring losses; the reasons come from Reason of Wastage." (links on "Wastage" and "Reason of Wastage").
- Refresh and **Export CSV** on the right of the header; a full-width row on phones.
- The view toggle sits in a toolbar inside the card, as in PO Receive; the choice is kept in `localStorage` key `rms.wastageReport.view`.

## 2. Columns
| View | Column | Align | Content |
|---|---|---|---|
| Entries | Recorded at | left | Company date format; time beneath as a caption |
| Entries | Item | left | Name bold; alias caption |
| Entries | Type | left | Inventory type |
| Entries | Location | left | Location name |
| Entries | Quantity | right | "2.5 kg", up to 3 decimals |
| Entries | Reason | left | Reason text; "No reason" for legacy rows |
| Entries | Value | right | `Money`, bold; "—" for an unknown unit |
| Entries | Recorded by | left | User name; "—" if unknown |
| By item | Item · Type | left | As above |
| By item | Quantity · Entries · Value · Share | right | Main-unit total · count · `Money` · whole percent ("<1%" when small, "—" with no value) |
| By reason | Reason | left | Bold |
| By reason | Entries · Items · Value · Share | right | Counts · `Money` · whole percent |

No Actions column: the report is read-only. Fits at 1280 px with no sideways scroll; below `md` it becomes cards.

## 3. Filters
| Filter | Behaviour |
|---|---|
| Type tabs | All + one per inventory type, each with its entry count under the other filters |
| Date range | `DateRangePicker`, draft + **Apply**; default last 7 days; up to 366 days |
| Search | `ListSearch`, "Search item...", 300 ms debounce |
| Location | `SearchableSelect`, "All my locations" + the user's locations |
| Reason | `SearchableSelect`, "All reasons" + the company's reasons |

Changing a filter or view returns to page 1; filters stay when switching view. Default 25 rows per page. **Clear filters** shows when any filter differs from its default; it doesn't change the view.

## 4. Feedback states
| State | Behaviour (exact text) |
|---|---|
| First load | Table skeleton rows |
| Refresh / paging / view switch | Thin progress bar above the table; rows stay until the new ones arrive |
| Empty (default filters) | "No wastage in this period." |
| No match | "No wastage matches these filters." + **Clear filters** |
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
`view_wastage_report` for the page and the export; checked on the server too.

## 7. Responsive
| | xs (360) | sm (600) | md+ (900–1920) |
|---|---|---|---|
| Header | Title, buttons full-width beneath | Buttons right | Buttons right |
| Tabs | Scroll sideways inside the tab bar | Same | All visible |
| Filters | Stacked | Two columns | One row from `lg` |
| Toggle | Full width, three equal parts | Left aligned | Left aligned |
| Entries | Card: item + value; "2.5 kg · Kitchen"; reason; "03/Nov/2026 06:30 PM · Asha" | Cards | Table |
| By item / By reason | Card: name + value; quantity or entries and items · share | Cards | Table |
| Footer | Compact pager | Standard | Standard |

## 8. Accessibility
The view toggle is a `ToggleButtonGroup` with `aria-label` "Report view"; tabs are a labelled `tablist`; table headers are real `th`; loading and errors use `role="status"`; the refresh and export buttons have labels.

## 9. Shared components
`PageHeader`, `ListSearch`, `DateRangePicker`, `SearchableSelect`, `TableHeader`, `TablePagination`, `Money`, `listPageSx`, `listFilterBarSx`, `listFooterSx`.
New (in `report/wastage-report/`): `wastage-report-row.tsx` (rows and cards for the three views, headings). `page-view/wastage-report.tsx` is rebuilt. Removed: `wastage-row.tsx`, `wastage-table.tsx`, `table-heading.ts`, `utils.ts`.

## 10. Template impact
None on `src/theme/*` or shared components.

## UI acceptance criteria
1. No sideways page scroll from 360 to 1920 px; cards below `md`.
2. Every state in §4 shows the exact text.
3. Dates and times follow the company format.
4. Numbers are right-aligned; the view is remembered after a refresh.
5. Theme tokens only; every icon button has a label.
