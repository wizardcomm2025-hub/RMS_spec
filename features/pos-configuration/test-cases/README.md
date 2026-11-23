# Test cases — POS Configuration

Feature code `POS`. Fixtures: CO-A with tax groups *GST 5%* (default; CGST 2.5, SGST 2.5), *GST 18%* (active; CGST 9, SGST 9), *VAT 12%* (inactive). CO-B with *GST 5%* (its own). Outlet-type locations Main Hall and Patio; store location CK (type Warehouse). Order channels: Table 5 (Main Hall, 4 seats), Takeaway (Main Hall). Users: U-ADMIN (all tax and channel permissions), U-VIEW (only `viewlist_tax_setting`), U-NONE.

| Files | |
|---|---|
| [`pos-configuration.tester.md`](./pos-configuration.tester.md) | UI cases |
| [`pos-configuration.developer.md`](./pos-configuration.developer.md) | API / DB cases |
