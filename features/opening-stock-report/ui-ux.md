# UI / UX — Opening Stock Report (stock movement)

Same anatomy as the Stock Report (`UI_UX_STANDARDS.md` §12): `PageHeader` "Report › Opening Stock Report" (About links: Stock Report, Stock Check Report) with **Export CSV**; type tabs with counts; filter bar: date range (DateRangePicker, draft + Apply, default today–today) · search · Location · Show; summary "n items · opening ₹x → closing ₹y"; info notice when the range starts before the ledger.

Table (no sideways scroll at 1280 px): ▸ · Item · Opening · In (success colour, "+") · Out (error colour, "−") · Count adj. (signed) · Closing (bold; error colour when negative) · Closing value ₹ · Actions (**Movements** text button). Location lines indented on a subtle background.

Movements dialog: title "Item · Location · range"; table Date · Location · Type chip · Document · Quantity (signed, coloured) · Balance after · By; opening and closing lines bold; scrolls inside the dialog; full screen below `sm`.

Phone: card per item — name + closing, "Opening x · In +y · Out −z · Adj ±w", Movements button. Theme tokens only.
