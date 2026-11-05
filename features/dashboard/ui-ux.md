# UI / UX — Dashboard

> Complete UI / UX plan, per `RMS_API/specs/UI_UX_STANDARDS.md`. Text the user sees is written **verbatim**. Theme tokens only, no hex; money through `Money` / `useCompanyCurrency`; dates in the company format. Route stays `/admin/dashboard`.

## 1. Page anatomy

Desktop (md and up):
```
Dashboard                                                              [⟳]
┌ Location ▾ All my locations ┐  ┌ 📅 01/Nov/2026 → 04/Nov/2026 ┐  [Apply when changed]
┌─ Sales ──────┐┌─ Stock value ─┐┌─ Low / out ──┐┌─ Wastage ───┐┌─ Production ┐┌─ Open POs ──┐
│ This period  ││ Right now     ││ Right now    ││ This period ││ This period ││ Right now   │
│ ₹1,24,500.00 ││ ₹3,42,110.00  ││ 18           ││ ₹2,310.00   ││ ₹48,200.00  ││ ₹62,000.00  │
│ 312 bills ·  ││ 240 items     ││ 12 low·6 out ││ 14 entries  ││ 22 batches  ││ 4 POs · All │
│ avg ₹399.04  ││               ││              ││             ││             ││ locations   │
└──────────────┘└───────────────┘└──────────────┘└─────────────┘└─────────────┘└─────────────┘
┌─ Needs attention ──────────┐ ┌─ Sales and costs by day ────────────────────┐
│ ③ requisitions to approve › │ │  ▇▂▅▇▃▁▆  bars                              │
│ ② transfers to receive    › │ │  ■ Sales ■ Production ■ Wastage             │
│ ① location with no check  › │ └─────────────────────────────────────────────┘
└────────────────────────────┘
┌─ Items to reorder ─── View all ┐ ┌─ Top wasted items ─── View all ┐
│ Item · Location · Qty · Reorder│ │ Item · Quantity · Value        │
│ Tomato  Newtown  0 Kg 5  [Out] │ │ Onion   4 Kg   ₹160.00         │
└────────────────────────────────┘ └────────────────────────────────┘
```
- `PageHeader`: title "Dashboard", refresh button, no description, no About, no breadcrumb trail beyond "Dashboard".
- Filter bar uses `listFilterBarSx`: `SearchableSelect` Location, `DateRangePicker` (draft + Apply, as other reports).
- Cards are clickable where a target exists; hover shows the pointer and a border in the info colour. No arrows, no percentages.
- Each card shows a small caption: "Right now" or "This period". Only allowed cards render, and the row fills the width (`repeat(auto-fit, minmax(170px, 1fr))`).
- If Needs attention is hidden, the chart takes the full width; likewise for the two lists.

## 2. Fields and columns

| Part | Field | Notes |
|---|---|---|
| Filter | Location | Options "All my locations" + my locations; default all; no clear button. With one location it's preselected |
| Filter | Dates | Default this month; max 366 days; future dates can't be picked |
| Items to reorder | Item (left, bold) · Location (left) · Quantity (right, with unit) · Reorder level (right) · Status (centre, badge) | 8 rows |
| Top wasted items | Item (left, bold) · Quantity (right, with unit) · Value (right) | 5 rows |
| Status badge | "Out" `error` · "Low" `warning` | Same as Stock Report |
| Chart | Bars per day; months when range > 62 days | Tooltip: date + money per series; series follow permissions; colours from the theme palette (`info`, `success`, `warning`) |

## 3. Dialogs / forms
None. Nothing is saved from this page.

## 4. Feedback states

| State | Behaviour (exact text) |
|---|---|
| First load | Per-section skeletons in the section's real shape; the page frame appears at once |
| Refresh / filter change | Section keeps its data with a thin progress bar on top |
| Section load error | In that section: "Couldn't load this." + **Retry** |
| Needs attention empty | ✓ icon + "All caught up. Nothing is waiting for you." |
| Items to reorder empty | "No items are low or out of stock." |
| Top wasted items empty | "No wastage in this period." |
| Chart empty | "Nothing to show for these dates." |
| No locations | Banner: "You aren't assigned to any location. Ask your admin to add you in User List." |
| No sections allowed | "Nothing to show for your role yet. Ask your admin for access to reports." |
| Invalid dates | Server message under the date picker via snackbar: "Choose a range of up to 366 days." |

## 5. Confirmations
None.

## 6. Permissions
See README D3 and [02](./02-needs-attention.md). The UI asks `GetDashboardSections` and renders only what comes back true: it doesn't rely on client `can()`, because `viewlist_*` codes are stripped on the client. The page route keeps the existing `view_dashboard` guard.

## 7. Responsive

| | xs (360) | sm (600) | md (900) | lg+ (1200–1920) |
|---|---|---|---|---|
| Filters | stacked, full width | side by side | side by side | side by side |
| KPI cards | 2 per row | 3 per row | 3 per row | up to 6 per row |
| Attention + chart | stacked | stacked | 5 / 7 columns | 4 / 8 columns |
| Two lists | stacked; rows become phone cards | stacked | 7 / 5 columns | 7 / 5 columns |
| Chart height | 220 px, horizontal scroll inside the card for long ranges | 260 px | 300 px | 300 px |

No sideways page scroll at any width.

## 8. Accessibility
Cards that navigate are links with a full name ("Stock value ₹3,42,110.00, right now, open Stock Report"). Attention rows are links. Status never by colour alone (badge text). Chart has an `aria-label` summary and the same totals are readable in the cards. Keyboard order: filters → cards → attention → chart → lists. Loading regions use `aria-busy`.

## 9. Shared components
`PageHeader`, `SearchableSelect`, `DateRangePicker`, `Money`, `useCompanyCurrency`, `useDatePicker`, `react-apexcharts` (as today), `listFilterBarSx`, `Card`.
New in `pages-sections/vendor-dashboard/dashboard/`: `kpi-cards.tsx`, `needs-attention.tsx`, `daily-trend-chart.tsx`, `reorder-items.tsx`, `top-wasted-items.tsx`, `section-state.tsx` (skeleton / error / empty wrapper). Old widgets are deleted once nothing imports them (plan lists them).

## 10. Template impact
None on `src/theme/*` or shared components. Page metadata title becomes "Dashboard - RMS Admin Dashboard".

## UI acceptance criteria
1. Works at 360, 600, 900, 1200 and 1920 px with no sideways page scroll.
2. Every state in §4 shows the exact text.
3. A failed section never shows 0, and other sections still load.
4. Cards say "Right now" or "This period"; changing dates leaves "Right now" figures unchanged.
5. A section the user may not see doesn't flash during loading.
6. Theme tokens only; company currency and date format everywhere.
