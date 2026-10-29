# UI / UX — Vehicle Master

> Follows `RMS_API/specs/UI_UX_STANDARDS.md` and the §12 list recipe (reference: Setting → Location). Only what differs is spelled out. Text the user sees is written here **verbatim**.

Route: `RMS_NextJS/src/app/(admin-dashboard)/admin/(setting)/vehicle/` · View: `src/pages-sections/vendor-dashboard/vehicle-master/`.

## 1. Page anatomy

- `PageHeader`: breadcrumb "Setting › Vehicle Master", title "Vehicle Master", ⓘ About, ⟳ Refresh, **＋ Add Vehicle** (`create_vehicle_master`).
- Description: none (omitted to avoid duplicating the About panel).
- About: *"Vehicles used for delivery and stock transfers between locations. Register your fleet by registration number and brand, and manage active vehicles available for transfers."*
- Filter bar: Search ("Search by registration number or manufacturer...") · Status ▾ (All / Active / Inactive) · "Clear filters".
- Card list; footer "Showing X of Y vehicles"; **no pagination** (returns full company list).
- Browser tab "Vehicle Master - RMS".

## 2. Columns / fields

Alignment (UI_UX_STANDARDS §7, strict): badge columns (status, type, requisition) are centred; Actions is the last column and right-aligned.

| Column | Priority | Content |
|---|:-:|---|
| Registration Number | P1 | Registration number, monospace-like weight 600, uppercase |
| Car Manufacturer Name | P1 | Manufacturer / make name |
| Status | P1 | "Active" (`success`) / "Inactive" (`default`) chip |
| Actions | P1 | Edit icon (`edit_vehicle_master`) · Active switch (`edit_vehicle_master`, tooltip "Deactivate" / "Activate") |

Phone (xs): card — Registration Number, status chip, Manufacturer line, Edit and switch at the bottom.

| Field | Required | Rule / hint | Error text |
|---|:-:|---|---|
| Registration Number | ✔ | Max 20, letters and digits only, uppercase normalized as typed, live duplicate check | "Registration number is required" · "Registration number can only contain letters and numbers" · "Registration number already exists" |
| Car Manufacturer Name | ✔ | Max 100, counter, letters/numbers/spaces and `& - ( ) ' / . ,` | "Car manufacturer name is required" · "Car manufacturer name can only contain letters, numbers, spaces and & - ( ) ' / . ," |

## 3. Dialog / form

- Module dialog in Location's style (dark title bar with Close): "Add Vehicle" / "Edit Vehicle — <regNo>"; `maxWidth="xs"`, `fullScreen` on xs.
- Two fields: Registration Number (full width, counter "0/20"), Car Manufacturer Name (full width, counter "0/100").
- No Active toggle in the dialog (status lives on the list switch).
- Buttons: **Cancel** · **Add Vehicle** / **Save changes**; Enter submits; spinner while saving; X / Esc / backdrop disabled while saving; "Discard changes?" when closing a changed form.
- Edit loads behind `LoadingOverlay`; "Vehicle not found" → toast, list refresh, dialog does not open.

## 4. Feedback states

| State | Behaviour (exact text) |
|---|---|
| First load | Skeleton rows |
| Refresh | `LinearProgress` on the card |
| Empty | "No vehicles yet. Add your first vehicle for deliveries and stock transfers." + Add button |
| No match | "No vehicles match your filters." + Clear filters |
| Load error | "We couldn't load your vehicles. Check your connection and try again." + **Retry**; toast "Failed to load vehicles" |
| Save | "Vehicle created successfully" / "Vehicle updated successfully"; failure: API message |
| Status | "Vehicle deactivated" / "Vehicle activated"; failure: API message, switch returns |

## 5. Confirmations

- Deactivate: title "Deactivate vehicle"; "Deactivate **<regNo>**? Staff won't be able to assign it to delivery or stock transfers. Past transfers keep this vehicle. You can activate it again at any time." — Cancel · **Deactivate**.
- Activate: title "Activate vehicle"; "Activate **<regNo>**?" — Cancel · **Activate**.

## 6. Permissions

No `create_vehicle_master` → Add button hidden. No `edit_vehicle_master` → Edit icon and switch hidden. No delete anywhere. The API enforces the same permissions.

## 7. Responsive

| | xs | sm | md+ |
|---|---|---|---|
| Header | Add full width | Inline | Inline |
| Filters | Stacked | One row | One row |
| List | Card list | Table | Table |
| Dialog | Full screen | xs | xs |

No horizontal page scroll; table height from `ScrollableTableContainer`.
