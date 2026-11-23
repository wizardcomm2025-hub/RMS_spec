# UI / UX — Stock Check

> Count-sheet page: `PageHeader` + filter bar + one Card table + sticky footer. Theme tokens only.

```
PageHeader  Stock › Stock Check            Stock Check  ⓘ About  ⟳
Filter bar  [Location ▾ searchable] [🔍 Search item or group] [Show: All ▾]      Date 03/Nov/2026 (info)
Tabs        Raw Item | Semi Finished | …
┌ Card ──────────────────────────────────────────────────────────────────────────┐
│ Item (pinned) · Group · System stock · Last count · Count [  ] [kg▾] · Variance · Note · ✓ │
└────────────────────────────────────────────────────────────────────────────────┘
Sticky footer:  3 items counted                         [Discard] [Save counts (3)]
```

| # | Rule |
|---|---|
| F1 | Count input blank by default; `0` allowed; unit select lists main and alternate unit (main first). Variance = converted count − system: 0 → "0" (text.secondary), below → error colour with "−", above → warning colour with "+", plus % when system > 0 |
| F2 | Last count: qty + unit, caption "03/Nov/2026 · 10:42 · Ravi" (company date / time format) |
| F3 | Row ✓ saves that row (tooltip "Save this count"; disabled until a count is entered). Footer button saves all entered rows; spinners; inputs disabled while saving |
| F4 | Unsaved counts: changing location / tab / leaving → "Discard counts?"; tab close → browser prompt |
| F5 | Item + Group columns pinned on horizontal scroll; header row sticky |
| F6 | Phone (< 600 px): one card per item (name, group, system, last count, count + unit, variance, note, Save) |
| F7 | States: skeleton on load, no-location / no-items / error messages (use case 01), LinearProgress on reload |

## Times in IST (use case 03, 2026-11-18)
1. **Where:** Stock Check list → Last count ("{date} · {time} · {user}"); Stock Check Report → Counted at (table, phone card, CSV).
2. **How:** a shared helper `utcToIst()` (`src/utils/ist-time.ts`) reads the API value as UTC and returns a date whose clock reads IST; the existing `userDateFormat` / `userTimeFormat` format it. No new text, layout or states.
3. **Responsive / feedback / accessibility:** unchanged.
4. **Template impact:** none; the helper is new and only these two screens use it in the pilot.
