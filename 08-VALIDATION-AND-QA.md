# 08 — Validation, Edge Cases, QA and Release Checklist

## Scope
This file describes expected QA results for BinGi HMS Accommodation Management redesign. It is **not** a report of tests already executed.

## Cross-cutting validation rules
| Area | Required |
| --- | --- |
| Property scope | Every location, category, unit and bed belongs to exactly the authorized property |
| RBAC | Property admins manage configuration; other roles require explicit permissions |
| Category | Required name/type, occupancy, consistent beds/capacity, valid location strategy |
| Location | Existing active saved node; valid parent-child hierarchy; optional when policy permits |
| Numbering | Property-unique physical unit display ID; saved template + manual validation |
| Bulk add | Exact preview of every unit; no silent skips/overwrites |
| Concurrency | Atomic insert and database uniqueness constraints for simultaneous creation |
| Dormitory | Unique bed code, valid parent room, consistent bed count and layout |
| Inventory | One whole-unit sellable item per standard unit; per-bed records for dormitory |
| Booking overlap | No overlapping allocations for same bed/unit; entire dormitory exclusion |
| Maintenance | Dated block and operational status cannot silently conflict with future bookings |
| Draft | Incomplete allowed; not bookable |
| Publish | Validated, operationally eligible, rate plan and restrictions checked |
| Edit | Impact review before modifying booked units/bed counts/location |
| Audit | Persist actor, date, before/after and origin of config changes |
| Usability | Shared five-stage wizard, stable button placement, responsive layout |

## Test suite A — One-time config
- A01 New property enters Accommodation Setup before first publish.
- A02 Existing Property Profile name/type prefilled and not retyped.
- A03 Save one building with floors 1–3; later Room addition reuses them.
- A04 Save Garden and Lake Zones; later Cottage/Villa addition reuses them.
- A05 If one Building exists, auto-select it; if several, show existing choices.
- A06 If no Block exists, block selector hidden.
- A07 Changing config prefix applies only to future codes unless explicit safe migration.
- A08 Editing/removing Zone with booked Villas surfaces affected inventory.
- A09 100+ floor labels and aliases are stored and selectable.
- A10 Disabling a type doesn't delete its inventory.

## Test suite B — Room
- B01 New Deluxe Twin category (capacity/2 beds/amenities/rate) saved once.
- B02 Reuse Deluxe Twin without reentering category defaults.
- B03 Floor 1 permits exact nonconsecutive selection 101,103,105.
- B04 Duplicate 103 rejected and existing room unchanged.
- B05 Floor 2 numbers 201/203 use saved template.
- B06 Multiple buildings with same floor cannot create ambiguous codes.
- B07 Room with active booking resists destructive location/category changes.
- B08 Draft cannot be accidentally booked.

## Test suite C — Cottage
- C01 Zone is default location; Floor not compulsory.
- C02 Optional Block shown only when configured.
- C03 Save C01,C03,C05 exactly.
- C04 Category beds/bedrooms saved; entire Cottage still 1 inventory item.
- C05 Unique code policy across Zones works.
- C06 Maintenance/booking conflict warns correctly.

## Test suite D — Villa
- D01 Create Pool Villa with 2 bedrooms and private pool.
- D02 Villa internal levels do not create separate Hotel Floors or sellable units.
- D03 Zone/Block reused with V01,V03,V05.
- D04 One Villa reserved blocks its complete unit dates.
- D05 Bedroom booking unavailable in V1.
- D06 Category and unit overrides remain distinguishable.

## Test suite E — Apartment
- E01 Studio permits 0 separate bedrooms with beds in common space.
- E02 Tower A / Tower B floor 1 use unique prefixes where necessary.
- E03 Floor-based 201,203,205 allowed without 202.
- E04 Kitchen/living/balcony switches reveal expected options.
- E05 Entire Apartment booking blocks its included bedrooms.
- E06 No repeated Tower/Building creation during unit add.

## Test suite F — Dormitory
- F01 D101 + D103 with 8 spaces each produce exactly 2 rooms + 16 beds.
- F02 Bunk upper/lower labels map correctly.
- F03 Bed D101-B01 booking does not block D101-B02.
- F04 Exclusive D101 booking blocks every bed for overlapping range.
- F05 Exclusive booking fails if one bed already booked in overlap.
- F06 Concurrent bed booking attempts cannot sell the same space twice.
- F07 Bed count reduction with an active reservation requires conflict handling.
- F08 Category rate per bed inherited correctly.
- F09 Housekeeping/maintenance state distinct from dated bed booking.
- F10 Subscription physical-unit count and bed-space count both displayed.

## Test suite G — Other / Custom
- G01 Houseboat type label saved under stable internal Other type.
- G02 No Floor needed if location policy doesn't use it.
- G03 Custom prefix HB01 and manual gaps validated.
- G04 Per-bed mode either uses Dormitory engine or remains disabled.
- G05 Dynamic extra properties do not require uncontrolled schema changes.

## Test suite H — Wizard UX / permissions / migration
- H01 Same primary/secondary CTA position and step labels for all six types.
- H02 Back navigation preserves form state.
- H03 Unsaved Changes dialog protects draft state.
- H04 Keyboard control and mobile narrow viewport usable.
- H05 Error messages name the problematic unit/location and explain correction.
- H06 Amenities Library picker opens for all relevant types without duplicating items.
- H07 Access to another property's location/category is denied.
- H08 Duplicate bulk selections are detected before writing.
- H09 Entire unit creation and nested bed creation are transactional.
- H10 Existing Room Management records migrate/match without breaking reservation IDs.

## Implementation handoff / release gates
**Gate 1 — design:** final field map, UX steps, location/numbering preview and existing HMS component reuse approved.
**Gate 2 — schema:** category/unit/bed boundaries, unique indexes, booking constraints, location tree, migration and RBAC verified.
**Gate 3 — functional:** six flows pass acceptance tests, with Room/Cottage/Villa/Apartment first and bed booking when ready.
**Gate 4 — regression:** reservations, room assignment, calendar, maintenance, rate/inventory, reporting, channel bookings and subscription limits checked.
**Gate 5 — rollout:** feature flag, migration/backfill, duplicate detection, rollback plan and audit/troubleshooting notes.

## Non-goals of this documentation
- Does not claim production implementation, integration test results, or screenshots.
- Does not specify unverified current backend entity names as fact.
- Does not automatically migrate existing records or modify live hotel data.
- Does not claim every Accommodation Type has a separate live published interface; they share the universal wizard.
