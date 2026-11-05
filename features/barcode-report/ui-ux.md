# UI / UX — Barcode Report

> Per `RMS_API/specs/UI_UX_STANDARDS.md` and the list recipe in §12. Text the user sees is written verbatim; tester cases quote it. Theme tokens only, no hex.

## 1. Page anatomy
```
┌──────────────────────────────────────────────────────────────────────────────────┐
│ Report › Barcode Report                                                           │
│ Barcode Report                                         [↻]  [⭳ Export CSV]        │
│ ⓘ About this page ▾                                                               │
├──────────────────────────────────────────────────────────────────────────────────┤
│  In stock (124) │ Expiring soon (6) │ Expired (2) │ Used up (40)      (status tabs) │
├──────────────────────────────────────────────────────────────────────────────────┤
│ [🔍 Search item or barcode…      ] [Location: All my locations ▾] [Type: All ▾]   │
├──────────────────────────────────────────────────────────────────────────────────┤
│ 38 items · 124 barcodes · remaining value ₹2,14,300.00                            │
│    ITEM          TYPE      REMAINING  BARCODES  NEAREST EXPIRY          VALUE     │
│ ▸  Paneer        Raw Mat.     2 kg           1  —                      600.00     │
│ ▾  Onion         Raw Mat.     8.5 kg         3  02/Nov/2026 [Expired]   340.00    │
│    pyaz                                                                           │
│      BARCODE   LOCATION  VENDOR   MFG         EXPIRY                INITIAL REMAINING VALUE │
│      100231    Kitchen   Fresh Co 20/Oct/2026 02/Nov/2026 [Expired]  1000 g   500 g   20.00 │
│      100232    Kitchen   Fresh Co 25/Oct/2026 14/Nov/2026 [Expiring] 10 kg    5 kg   200.00 │
│      100240    Bar       Fresh Co 01/Nov/2026 03/Jan/2027            5 kg     3 kg   120.00 │
├──────────────────────────────────────────────────────────────────────────────────┤
│ 1–25 of 38 items                                       25 / page ▾   ‹ 1 2 › │
└──────────────────────────────────────────────────────────────────────────────────┘
```
- `PageHeader`: breadcrumb "Report › Barcode Report", no description. About: "Stock held under the barcodes made in Bar-code Generation, and what's left of each. Scanning a barcode in Wastage reduces it. Expiring soon means within the next 30 days. Values use today's cost." (links on "Bar-code Generation" and "Wastage").
- Refresh and **Export CSV** on the right of the header; a full-width row on phones.

## 2. Columns
| Column | Align | Content |
|---|---|---|
| ▸ | center | Expand / collapse barcodes |
| Item | left | Name bold; alias caption |
| Type | left | Inventory type |
| Remaining | right | Main-unit total, "8.5 kg"; "—" if every barcode is in an unknown unit |
| Barcodes | right | Count in this tab |
| Nearest expiry | left | Company date + status chip; "—" with no date |
| Value | right | `Money`, bold; "—" if no barcode can be valued |

Barcode rows (indented, subtle background): Barcode (monospace) · Location ("—" for none) · Vendor · Mfg date · Expiry + chip · Initial · Remaining (the barcode's unit) · Value.

Chips: *Expired* error · *Expiring* warning · *Used up* default; none otherwise. Fits at 1280 px with no sideways scroll; below `md` it becomes cards.

## 3. Filters
| Filter | Behaviour |
|---|---|
| Status tabs | In stock / Expiring soon / Expired / Used up, each with its barcode count under the other filters |
| Search | `ListSearch`, "Search item or barcode...", 300 ms debounce |
| Location | `SearchableSelect`, "All my locations" + the user's locations |
| Inventory type | `SearchableSelect`, "All types" + the company's types |

Changing a filter or tab returns to page 1. Default 25 items per page. **Clear filters** shows when search, location or type is set; it keeps the tab.

## 4. Feedback states
| State | Behaviour (exact text) |
|---|---|
| First load | Table skeleton rows |
| Refresh / paging | Thin progress bar above the table; rows stay |
| Empty (In stock, no filter) | "No barcodes at your locations yet." |
| Empty tab, no filter | "Nothing expiring in the next 30 days." · "No expired barcodes." · "No used-up barcodes." |
| No match | "No barcodes match these filters." + **Clear filters** |
| Load error | "We couldn't load the report." + **Retry** |
| No location | "You aren't assigned to any location. Ask an admin to add you in User List." |
| No permission | "You don't have permission to perform this action." in place of the table |
| Exporting | Spinner in the button, disabled until done |
| Export empty | Snackbar "Nothing to export for these filters." |
| Export error | Snackbar "Couldn't export the report. Please try again." |

## 5. Confirmations
None: nothing on this page changes data.

## 6. Permissions
`view_barcode_report` for the page and the export; checked on the server too.

## 7. Responsive
| | xs (360) | sm (600) | md+ (900–1920) |
|---|---|---|---|
| Header | Title, buttons full-width beneath | Buttons right | Buttons right |
| Tabs | Scroll sideways inside the tab bar | Same | All visible |
| Filters | Stacked | Two columns | One row from `lg` |
| List | Card: item + value; remaining · barcodes; nearest expiry + chip; **Show barcodes** toggles the lines | Cards | Table with expand |
| Footer | Compact pager | Standard | Standard |

## 8. Accessibility
Expand buttons have `aria-expanded` and `aria-label` "Show barcodes of Onion"; tabs are a labelled `tablist`; status has text, not colour alone; loading and errors use `role="status"`.

## 9. Shared components
`PageHeader`, `ListSearch`, `SearchableSelect`, `TableHeader`, `TablePagination`, `Money`, `listPageSx`, `listFilterBarSx`, `listFooterSx`.
New (in `report/barcode-report/`): `barcode-report-row.tsx` (row + barcode lines, phone card, headings, grouping). `page-view/barcode-report.tsx` is rebuilt. Removed: `barcode-row.tsx`, `barcode-table.tsx`, `barcode-pagination.tsx`, `table-heading.ts`, `utils.ts`.

## 10. Template impact
None on `src/theme/*` or shared components.

## UI acceptance criteria
1. No sideways page scroll from 360 to 1920 px; cards below `md`.
2. Every state in §4 shows the exact text.
3. Dates follow the company format with the right day and month.
4. Numbers are right-aligned; status always has text.
5. Theme tokens only; every icon button has a label.
