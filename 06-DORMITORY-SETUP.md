# 06 — Dormitory Setup Specification

**Accommodation type:** Dormitory  
**Examples:** AC Dormitory, Non-AC Dormitory, Premium Shared Dormitory, Hostel-style Shared Room.  
**Important inventory:** Physical dormitory room and **bookable beds** are different entities.

## Core decision
Unlike a normal Room, a dormitory commonly sells **individual bed spaces**. D101 can contain B01–B08, so one D101 room equals one physical unit and eight sellable bed spaces. Entire Dormitory exclusive booking is an optional mode which must block all constituent beds over that booking interval. No overlapping sale of bed and entire room.

## Prerequisites — one-time
Owner has completed Property Profile; enabled Dormitory; configured relevant Building/Hostel Block and Floor nodes; configured Dormitory room IDs (e.g. D101,D102) and Bed numbering (D101-B01...). Set property default policies and amenities. Do not rebuild floor or numbering structure when creating each dormitory.

## Full creation flow — 5 shared wizard stages
~~~text
Accommodation Dashboard
  -> + New Accommodation -> Dormitory
  -> Load saved configuration (complete focused Setup if missing)
  -> STEP 1: Type [Dormitory]
  -> STEP 2: Category
      -> Existing Standard/AC/Non-AC/Premium Dormitory
      -> OR Create new Category
         -> Name [AC Dormitory]
         -> Sharing Arrangement [Shared / Private Group Room]
         -> Occupancy Policy [Mixed / Male Only / Female Only]
            (subject to applicable local policy/law and operational rules)
         -> Bed Style [Single / Bunk / Mixed]
         -> Total Bookable Bed Spaces [8]
         -> Layout [8 single spaces OR 4 bunk frames x 2 spaces]
         -> Bathroom [Attached / Shared], room size [500 sqft]
         -> AC [Yes/No]
         -> Default bed-night pricing + amenities + meals + photos
         -> Location Strategy [Building -> Floor]
         -> Save Category
  -> STEP 3: Location, Rooms & Beds
      -> Building [Hostel Block]
      -> Floor [1st Floor]
      -> Generate / select Room IDs D101, D103 (nonconsecutive allowed)
      -> Nested BED CONFIGURATION (same wizard stage)
         -> D101 auto-generate B01 ... B08
         -> D103 auto-generate B01 ... B08
         -> Complete codes D101-B01 ... D101-B08
         -> Complete codes D103-B01 ... D103-B08
         -> For bunks, map Upper/Lower to a physical frame
         -> Optional bed label/status/overrides
         -> Check each bed code unique within its parent + property policy
  -> STEP 4: Details + Pricing
      -> Inherit shared room photo/amenities and per-bed nightly rate
      -> Individual bed overrides (where permitted)
      -> Initial operational statuses
  -> STEP 5: Review
      -> Category AC Dormitory
      -> Building Hostel Block / Floor 1
      -> Dormitory Rooms: D101, D103 = 2 physical rooms
      -> Beds: 8 each = 16 bookable bed spaces
      -> Save Draft / Create & Publish (atomic parent + child creation)
  -> Dormitory Dashboard -> Room list / Bed map / Availability
~~~

## Returning operator fast add
~~~text
Dashboard -> Dormitory -> Existing AC Dormitory (8 Beds)
  -> Hostel Block -> Floor 2
  -> Choose D201,D203
  -> Generate 8 beds per room automatically
  -> Review: 2 Rooms, 16 Bed Spaces -> Create
~~~

## Conditional fields
| Field | Rule |
| --- | --- |
| Category Name | Required |
| Sharing Arrangement | Shared bed-wise default; Private Group/Entire Room optional |
| Occupancy Policy | Mixed/Male/Female category policy (where applicable); enforce consistently |
| Beds per room | Required positive integer, from actual sleeping positions |
| Bed Type | Single, bunk, mixed |
| Bunk Configuration | Each physical bunk frame has exactly the declared number of positions; e.g., upper/lower |
| Bathroom | Attached/Shared, optionally count |
| Room Size | Optional sqft/sqm |
| Parent Location | Saved Building -> Floor |
| Bed Rate | Per bed per night for bed-wise mode |
| Entire Room Rate | Optional rate plan used only in exclusive mode |
| Bed-specific status | Available operationally / out of service; reservations are date-based |
| Shared Amenities | Inherit from category; limited per-bed exceptions |

## ID generation
~~~text
Room D101 (8 bed spaces)
  D101-B01
  D101-B02
  D101-B03
  D101-B04
  D101-B05
  D101-B06
  D101-B07
  D101-B08

Room D103
  D103-B01 ... D103-B08

Optional bunk display:
  D101-01-U (upper)
  D101-01-L (lower)
  D101-02-U
  D101-02-L
~~~
Room and bed code policies are stored one time. No duplicate room ID; no duplicate bed code within a room. Physical bed IDs should not silently change after bookings. A shared Room ID isn't a single rentable Room in bed-wise mode.

## Availability logic (critical)
~~~text
For date range [check-in, check-out):
  Bed D101-B01 booked -> only that bed unavailable for those dates
  Bed D101-B02 unbooked -> can remain available
  Entire Dormitory D101 booked -> all its beds blocked for those dates
  Any existing booked bed overlapping dates
     -> deny exclusive-room booking OR require supported conflict resolution
~~~
- Date intervals use property local calendar with consistent timezone rules; check-out day typically not occupied after standard departure timing.
- Atomic/transaction-safe booking allocation and database constraints/locks prevent parallel double-bookings.
- Bed count changes must not invalidate existing bookings: disabling/removing booked beds needs protected migration/reassignment.
- In room-exclusive mode don't charge a simultaneous independent bed rate as if more inventory exists.
- Operational status is not the same as reservation status.

## Required interfaces
- Category / Unit wizard shared with other types.
- Inline Bed Configuration (nested in Location & Units).
- Bed grid/list with labels, upper/lower grouping and maintenance badges.
- Individual bed details and dated availability.
- Review summary with physical room count AND sellable bed count.
- Booking picker respecting shared vs entire room conflicts.
- Audit history and protected destructive changes.

## Subscription accounting (requires product decision)
Track **physical dormitory rooms** and **sellable bed spaces** separately. Subscription tier limits must explicitly choose which quantity is billed, rather than count eight beds as eight hotel rooms without a policy.

## QA acceptance
1. Two dormitory rooms with eight beds each create 2 parent rooms and 16 unique sellable beds.
2. D101 and D103 can be chosen without D102.
3. Bed D101-B01 booking does not block D101-B02.
4. Entire-room booking blocks all eight beds for same date range.
5. Entire-room booking rejected when an overlapping bed booking already exists.
6. Upper/lower labels map to physical bunk positions.
7. Category settings reused; Property floor definitions not asked again.
8. Concurrent booking attempts cannot double-book bed.
9. Editing booked bed count triggers safe impact/conflict UI.
10. Entire dormitory configuration fits the same 5-stage wizard.
