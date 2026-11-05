# Vendor — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_ADMIN`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `VND-02-D01` | **Cross-company attach (D1)** | default | `AddEditVendor` with `distributorId: V-B` | "Vendor not found"; no `DistributorContact` row added / changed for V-B |
| `VND-02-D02` | **Contact overwrite (D2)** | default | edit V-NEW with `distributorContactId: C-B` | C-B unchanged; V-NEW's own contact updated |
| `VND-02-D03` | Over-posting (D3) | default | body `createdBy`, `companyId`, `isActive: false` | Ignored; UpdatedBy = token user |
| `VND-02-D04` | Duplicates (D4) | default | name of V-USED; GSTIN of V-GST | Dup messages |
| `VND-02-D05` | Validation | default | empty name; 101-char name; bad GSTIN; 9-digit phone; bad email; PIN "12" | Field messages |
| `VND-02-D06` | Concurrency | default | stale / missing `updatedAt` on edit | Stale message |
| `VND-02-D07` | Permission (D9) | `TOKEN_U_STAFF` | save; status; delete | Permission message |
| `VND-04-D01` | Delete in use (Q1) | default | `DeleteVendor` V-USED; V-NEW; V-B | In use; ok; not found |
| `VND-03-D01` | Status | default | `ChangeVendorStatus` V-NEW off; V-B off | ok; not found |
| `VND-01-D01` | Cross-company read | default | `GetVendorById` V-B | Not found |
| `VND-02-D08` | Auth | no token; `TOKEN_SA` | every endpoint above | 401 |
