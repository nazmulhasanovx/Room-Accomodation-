# 01 — Booking Availability and Reservation Allocation

**P0 — correctness requirement, not a deployed implementation claim.**

- Room/Cottage/Villa/Apartment/default Other = **one bookable whole Unit** per physical Unit.
- Dormitory = one parent Room with bed-space inventory, plus optional exclusive whole-room booking atomically blocking all beds.
- Categories are product templates **not extra physical inventory**; Special Stay presets use mapped Core Type's inventory logic.

## Category-only / Unassigned Booking

```text
Deluxe Category: Room 101,102,103,104
Guest A books 12–14 Nov, Category only, exact Unit unknown
Guest B books 13–15 Nov, Category only
Guest C books Room 101 12–15 Nov
New request 13–15 Nov
  → account for all A/B/C, maintenance, holds, restrictions
  → verify feasible allocation for entire requested stay
  → only confirm when enough sellable Unit capacity remains
```

Confirmed unassigned Category Reservation **consumes** capacity; don't assume it is unsold merely because no Unit assigned. Naive daily subtraction may overstate ability to provide the same room continuously. Use a tested assignment feasibility model across overlapping reservations and each continuous stay; check at confirmation and eventual assignment under transaction/locks or equivalent concurrency-safe guarantee.

## States & dates

Quote → optional Hold (expires) → Confirmed Category Reservation → Assigned Unit/Bed → Check-in/out; cancellation, modification, no-show and release according to policy. Date interval `[check_in,check_out)`. Keep separate publish status, operational status, rate/date restrictions and reservation state. Published/Active ≠ Available all dates.

## Dormitory conflict

D101-B01 sold on dates → only B01 unavailable, B02 may remain sellable. Entire D101 exclusive cannot be sold if any bed booked on overlap. Exclusive booking prevents child sales. Parallel booking attempts cannot double-sell bed or exclusive parent.

## Critical tests

Two staff book last Unit concurrently → max one success; category-only holds reduce availability; booking must be allocatable across full stay; maintenance/OTA updates reconcile; hold expiry releases capacity; exact Room not promised before assignment. Calendar needs unassigned category lane. Preserve existing IDs and invoices. [QA](../05-quality/01-qa-and-acceptance.md).
