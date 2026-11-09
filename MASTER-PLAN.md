# BinGi HMS — Accommodation Management Master Plan

**V1.2 — 09 November 2026** · NEONECY / BinGi Hotel Management Technology  
**Status:** Canonical product architecture and UX handoff; actual HMS production behavior must be verified separately.

## 1. Architecture — one property, many stays

```text
PROPERTY PROFILE (one of 10 property types)
  -> ONE-TIME ACCOMMODATION CONFIGURATION
      -> Enabled Core Types (ROOM/COTTAGE/VILLA/APARTMENT/DORMITORY/OTHER)
      -> Location Tree (Building/Tower/Floor/Zone/Block/Cluster)
      -> Category Location Strategies, numbering rules, shared defaults
  -> ACCOMMODATION TYPE OR PRESET (14 visible choices)
      -> CATEGORY (Deluxe Twin, Garden Cottage, Pool Villa, etc.)
          -> PHYSICAL UNIT (UUID, Local Number, property-unique Full Code)
              -> BOOKABLE INVENTORY (whole Unit, or Dormitory Bed Space)
                  -> RATE PLAN, DATE-WISE AVAILABILITY, RESERVATION ALLOCATION
```

Six Core Types preserve the backend inventory model; **14 user-facing cards** include Room, Cottage, Villa, Apartment, Dormitory, Bungalow, Cabin, Chalet, Treehouse, Tent/Glamping, Houseboat, Capsule/Pod, Holiday Home and Other. Specialty cards use saved presets mapping to an existing Core Type, not a new engine. Categories such as Suite, Double, 1BR and Penthouse are not new Core Types. [Full taxonomy](docs/01-foundations/01-property-and-accommodation-types.md).

## 2. First-time setup — four UI stages

1. Enable applicable Core Accommodation Types (multi-select, Property Type-informed, not restricted).
2. Configure actual saved Locations (Building/Tower/Floor or Zone/Block; no mandatory Floor for Cottage/Villa).
3. Configure Location Strategy + Numbering (101,201; C01; V01; TA-101; D101-B01; custom).
4. Review and save configuration version with ownership/validation.

Existing Property Profile, currency, tax and defaults are reused; advanced settings are expandable. Owner can edit configuration later with booking-impact review. [Details](docs/01-foundations/02-one-time-configuration.md).

## 3. Add Accommodation — one consistent five-stage wizard

```text
Dashboard → + Add Accommodation
  1. TYPE: choose a visible card → map to Core Type
  2. CATEGORY: existing or new nested Category Editor
     → save Category beds/occupancy/amenities/photos/video/base rate ONCE
  3. LOCATION & UNITS: full saved Building/Floor/Zone/Block number library
     → select 101,103,105 / generate N / range / manual
     → Dormitory: nested parent unit & Bed Spaces
  4. DETAILS & PRICING: review inherited Category fields
     → edit only optional Unit-specific differences
  5. REVIEW / SAVE SETUP DRAFT / CREATE UNIT DRAFT / PUBLISH
     → validate plan limits, codes, pricing, location & booking safety
```

**Returning user fast path:** Existing Type/Category → Location Library → exact Unit Numbers → Review & Publish. Keep same stepper and footer CTA positions across Types; do not repeat input for inherited fields. [Wizard](docs/02-accommodation-flows/00-universal-creation-flow.md).

## 4. Physical location and numbering

- Room: Building → Floor; 101,103 on Floor 1; 201 on Floor 2.
- Cottage: Zone → optional Block; C01, C03.
- Villa: Zone → optional Block; V01, V03. Internal Villa levels are descriptive.
- Apartment: Tower → Floor; A101/TA-101.
- Dormitory: Building → Floor → Unit D101 with Bed Spaces B01–B08.
- Special Stays: configured Zone/Area/Block or none, with saved preset prefix (TH01/HB01 etc.).

**Three IDs:** Local Door Number (101) may repeat by Building; unique **Full Unit Code** (TA-101 vs TB-101) property-wide; immutable internal UUID used in all relationships. Numbering supports 100+ floors, gaps, prefixes and nonconsecutive bulk selection. [Full library](docs/01-foundations/03-location-and-numbering.md).

## 5. Booking safety

Room, Cottage, Villa, Apartment and default Other are **whole-unit inventory**; Dormitory supports individually booked beds and optional exclusive whole-room reservations that block all beds. Confirmed **Category-only bookings without exact Unit assignment consume capacity**, and must not be lost when calculating date availability. Validate every overlapping assigned/unassigned reservation, hold and maintenance block for feasible full-stay allocation. Booking confirm/assign/modify and OTA imports need concurrency-safe checks. [Availability](docs/03-booking-and-inventory/01-availability-and-allocations.md).

Unsaved wizard selection does not reserve a code; a persisted physical Unit Draft owns its property-unique code but is **not bookable**. "Published" means eligible for sale only when date-specific operational/rate restrictions allow. Category base rate is distinct from Rate Plan/date-wise price. [Draft/Publishing](docs/03-booking-and-inventory/02-drafts-rates-publishing.md).

## 6. UI, media and consistency

Premium compact desktop-first interface, consistent icon/card/button positions; search and selected count. Number Library can multi-select units across floors/zones, with lazy rendering for large hotels. Full Amenities Library multi-select and YouTube URL saved **at Category level once**, optional Unit override; video never mandatory. [Media/Amenities](docs/02-accommodation-flows/07-shared-media-and-amenities.md).

## 7. Delivery

**P0:** Audit existing HMS Room/Category/Booking/Rate schema, stable identifiers, location tree, unique codes and transactional booking safety.  
**P1:** Configuration, Room/Cottage/Villa/Apartment, shared wizard, bulk numbers and migration.  
**P2:** Dormitory bed booking after policy/QA; Special Stays presets and Other/Custom engine as supported.  
**P3:** Guest Stays, website themes, mobile/HMS, billing/reports and OTA partner verification before staged release.

No destructive migration, no unit renumbering or duplicate booking. Preserve historical invoice/reservation/OTA references. [Migration](docs/04-ecosystem/01-migration-and-impact.md) · [QA](docs/05-quality/01-qa-and-acceptance.md) · [Open decisions](docs/05-quality/02-open-decisions.md).
