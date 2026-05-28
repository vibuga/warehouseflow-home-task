## Bug 1 — Shipment Allocation

**What was wrong:** The app tried to allocate items using only the `on_hand_quantity`. It forgot to subtract the items that were already reserved. Because of this, it took items from a bin even if they were not free.
**Root cause:** In `server/src/services/shipmentService.js`, the `available` variable was calculated incorrectly.
**Fix:** I changed `const available = bin.on_hand_quantity;` to `const available = bin.on_hand_quantity - bin.reserved_quantity;`
**Test:** (will be added later)
