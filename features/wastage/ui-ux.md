# UI / UX — Wastage

> Same page pattern as Stock Check: `PageHeader` + filter bar + tabs + one Card table + sticky footer, plus a "Recorded today" card. Theme tokens only.

```
PageHeader  Stock › Wastage              Wastage  ⓘ About  ⟳
Filter bar  [Location ▾] [⌁ Scan barcode] [🔍 Search item or group] [Show ▾]
Tabs        Raw Item | Semi-Finished | Finished
┌ Card ───────────────────────────────────────────────────────────────────────┐
│ Item (pinned) · Group · In stock · Wastage [  ][kg▾] · Reason [▾][+] · After · ✓ │
└─────────────────────────────────────────────────────────────────────────────┘
┌ RECORDED TODAY ─────────────────────────────────────────────────────────────┐
│ 10:42 · Onion · 0.5 kg · Spoiled · Ravi                              [⊘]      │
└─────────────────────────────────────────────────────────────────────────────┘
Sticky footer:  2 items entered                       [Discard] [Save wastage (2)]
```

| # | Rule |
|---|---|
| F1 | Qty blank by default, > 0; unit main / alternate; reason `SearchableSelect` (active reasons), required once a qty is typed; "+" opens the existing Reason of Wastage dialog |
| F2 | After = in stock − wastage (main unit); red with "Only 4 kg in stock" when below 0 |
| F3 | Barcode: scan / type + Enter (or 600 ms pause); found → search set to the item, chip "Barcode 59683636 · 4 kg left ×" on the row; the line is linked to it |
| F4 | Footer save asks to confirm (dialog with the count and location); row ✓ saves at once; spinners; inputs disabled while saving; lines clear on success |
| F5 | Recorded today: newest first; Cancel icon (`delete_wastage`), confirm dialog; empty "Nothing recorded today." |
| F6 | Item and Group pinned; table leaves room for the footer; phone cards below 600 px |
| F7 | Unsaved lines: location / tab change → "Discard entries?"; tab close → browser prompt |
