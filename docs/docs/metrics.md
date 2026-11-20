# Delay per lane: SHP06 Coastline Fashion

Source: `sessions/03-batch-streams-and-architecture-patterns/starter/delay_per_lane.sql` with `shipper_id = 'SHP06'`, run in BigQuery on the October 2026 data (bookings joined to schedules on `schedule_id`). On time = arrived no more than 24 hours after the planned arrival.

Reliability target (from `project/shippers.md`): **60% on every ocean lane, 90% on every air lane.**

| Mode | Lane | Trade lane | Shipments | Avg delay (h) | On time | Target | Meets? |
|---|---|---|---|---|---|---|---|
| OCEAN | SGSIN → BEANR | Asia-North Europe | 10 | 50.4 | 50.0% | 60% | ❌ No |
| OCEAN | GBFXT → CNSHA | North Europe-Asia | 15 | 46.4 | 73.3% | 60% | ✅ Yes |
| OCEAN | DEHAM → USNYC | Transatlantic | 12 | 42.5 | 75.0% | 60% | ✅ Yes |
| OCEAN | CNSHA → ITGOA | Asia-Mediterranean | 26 | 22.2 | 76.9% | 60% | ✅ Yes |
| AIR | SIN → LHR | Asia-Europe | 25 | 2.5 | 96.0% | 90% | ✅ Yes |
| AIR | ICN → FRA | Asia-Europe | 27 | 0.7 | 100.0% | 90% | ✅ Yes |
| AIR | PVG → FRA | Asia-Europe | 18 | 1.1 | 100.0% | 90% | ✅ Yes |

Total: 133 shipments. Bookings whose sailing departs after October drop out of the join.

## What the numbers say

- **One lane misses the target: SGSIN → BEANR (Singapore → Antwerp), 50% on time against 60%.** It's also the lane with the longest average delay, about two days.
- On that lane the late shipments come from **ATL (3 of 3 late)** and **ORS (2 of 3 late)**; KST and BLC arrived on time. ATL is the cheapest ocean carrier on Asia-North Europe and the least reliable overall (35% on time across all its sailings in session 2), so Coastline's cost focus is costing it reliability on this lane.
- The three other ocean lanes pass, but with average delays of 1–2 days, so a few more late sailings would push them under 60% too.
- All three air lanes pass comfortably: average delay under 3 hours.
- Small samples: 10–27 shipments per lane in one month. A lane can swing by 10 percentage points with one or two late shipments, so these numbers need more months before they drive a contract decision.
