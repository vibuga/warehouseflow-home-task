## Bug 1 - Shipment Allocation

**What was wrong:** The app tried to allocate items using only the `on_hand_quantity`. It forgot to subtract the items that were already reserved. Because of this, it took items from a bin even if they were not free.
**Root cause:** In `server/src/services/shipmentService.js`, the `available` variable was calculated incorrectly.
**Fix:** I changed `const available = bin.on_hand_quantity;` to `const available = bin.on_hand_quantity - bin.reserved_quantity;`
**Test:** (will be added later)

## Bug 2 - Shipment Dashboard Timezones

**What was wrong:** The dashboard didn't show a late-night shipment. The database was filtering by the UTC date and ignoring the local timezone of the warehouse. Because of this, a shipment that was actually "tomorrow" in local time was missed.
**Root cause:** In `server/src/repositories/shipmentRepository.js`, the SQL query used `DATE(scheduled_at_utc)` without converting the timezone first.
**Fix:** I changed the query to convert the time first using `CONVERT_TZ`: `DATE(CONVERT_TZ(scheduled_at_utc, 'UTC', warehouse_local_timezone)) = ?`.
**Test:** (will be added later)
