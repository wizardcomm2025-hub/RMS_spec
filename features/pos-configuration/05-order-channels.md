# 05 — Order channels (screen only)

## Summary
Users manage the tables and counters a cashier picks on the POS. The screen is rebuilt; it keeps calling the PosPro API (`/Api/Setting/GetOrderChannelList`, `GetOrderChannelTypes`, `GetOrderChannelById`, `AddEditOrderChannel`, `UpdateOrderChannelStatus`) with the same requests.

## Main flow
1. POS Configuration → Order Channels. Loads on open.
2. Filters: search (from the first letter, 300 ms) · Outlet ("All outlets" + Outlet-type locations, Q4).
3. Columns: Name · Type · Outlet · Seats (right; "—" for non-seated types) · Active (switch, centred) · Show online (switch, centred) · Actions (Edit, right).
4. **Add channel** → standard dialog: Channel type * · Outlet * · Name * · Seats * (seated types only, D7) · Active · Show online · Description.
5. Turning **Active** off asks "Turn off Table 5? Cashiers won't see it on the POS." Turning it on, or changing **Show online**, saves straight away.

## Alternative flows
Empty: "No order channels yet." · No match + Clear filters: "No order channels match these filters." · Error + Retry: "We couldn't load order channels." · No outlet locations: info banner "Add an Outlet-type location in Setting → Location to create tables." · Save / status failure: the API's message, or "Couldn't save the order channel. Please try again."

## Acceptance criteria
1. Search works from one letter (C1).
2. Seats is asked only for seated types; Takeaway, Delivery and similar save without it (C1, D7).
3. Switching a channel off asks first; a failed switch puts the switch back and shows the error (C1).
4. Load errors show with Retry instead of an empty table (C1).
5. Outlet filter and form list only Outlet-type locations (Q4).
6. Help text says what the screen does, with no claim about tax by channel type (T7).
7. Works at 360–1920 px with all feedback states (`ui-ux.md`).

## Open questions
None.
