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

## Bug 3 — Blank Aisle Filter

**What was wrong:** Searching inventory with a blank Aisle field returned zero results. The system was trying to find an aisle literally named "" (empty string) instead of just ignoring the filter.
**Root cause:** In `server/src/repositories/inventoryRepository.js`, the code checked `if (filters.aisle !== undefined)`, which allowed empty strings to pass through to the SQL query.
**Fix:** I changed the condition to `if (filters.aisle)` so it ignores empty strings.
**Test:** (will be added later)

## Bug 4 — Stale Discrepancy Review Count

**What was wrong:** The metric card showing the number of open discrepancy reviews didn't update when a review was approved. It was using a static total value instead of the actual number of rows currently in the queue.
**Root cause:** In `client/src/pages/CycleCountsPage.jsx`, the `MetricCard` used `cycleState.totals.openReviewCount`, which fell out of sync with the displayed rows.
**Fix:** I changed the value to `openReviewRows.length` so it always perfectly matches the actual number of open items in the table.
**Test:** (will be added later)

## Bug 5 — Split Vendor Reorder Summaries

**What was wrong:** The vendor summary table was paginating raw low-stock rows *before* grouping them. This caused single vendors to appear multiple times across different pages if their items spanned a page boundary.
**Root cause:** In `server/src/repositories/reorderRepository.js`, the SQL query applied `LIMIT` and `OFFSET` in a subquery, and grouped the results of that subquery.
**Fix:** I simplified the SQL query to group by `vendor_name` directly on the `bins` table first, and applied the `LIMIT` and `OFFSET` at the very end.
**Test:** (will be added later)




