# BinGi HMS — Accommodation Management Documentation

**NEONECY · BinGi Hotel Management Technology** · **V1.2 · 09 November 2026**  
**Status:** Product/UX/engineering specification, **not deployed software**.

## Canonical reading order

1. [Master Plan](MASTER-PLAN.md) — complete end-to-end flow.
2. [Property and Accommodation Types](docs/01-foundations/01-property-and-accommodation-types.md) — **10 Property Types, 6 backend Core Types and 14 visible accommodation options**.
3. [One-Time Setup](docs/01-foundations/02-one-time-configuration.md) — saved locations, formats and policies.
4. [Locations and Numbering](docs/01-foundations/03-location-and-numbering.md) — Floor/Block/Zone, full library picker, unique codes.
5. [Universal Creation Flow](docs/02-accommodation-flows/00-universal-creation-flow.md) — consistent five-stage wizard.
6. [Booking and Availability](docs/03-booking-and-inventory/01-availability-and-allocations.md) — unassigned reservations and conflict safety.
7. [Draft, Rate and Publishing](docs/03-booking-and-inventory/02-drafts-rates-publishing.md) — draft ownership, price/availability.
8. [QA Acceptance](docs/05-quality/01-qa-and-acceptance.md) — tests and release gates.
9. [Open Decisions](docs/05-quality/02-open-decisions.md) — approvals required.

## Repository map

```text
README.md
MASTER-PLAN.md
CHANGELOG.md
docs/
  01-foundations/
    01-property-and-accommodation-types.md
    02-one-time-configuration.md
    03-location-and-numbering.md
  02-accommodation-flows/
    00-universal-creation-flow.md
    01-room.md
    02-cottage.md
    03-villa.md
    04-apartment.md
    05-dormitory.md
    06-other-custom.md
    07-shared-media-and-amenities.md
  03-booking-and-inventory/
    01-availability-and-allocations.md
    02-drafts-rates-publishing.md
  04-ecosystem/
    01-migration-and-impact.md
  05-quality/
    01-qa-and-acceptance.md
    02-open-decisions.md
```

## Type-specific documents

[Room](docs/02-accommodation-flows/01-room.md) · [Cottage](docs/02-accommodation-flows/02-cottage.md) · [Villa](docs/02-accommodation-flows/03-villa.md) · [Apartment](docs/02-accommodation-flows/04-apartment.md) · [Dormitory](docs/02-accommodation-flows/05-dormitory.md) · [Other / Custom](docs/02-accommodation-flows/06-other-custom.md) · [Shared Video / Amenities](docs/02-accommodation-flows/07-shared-media-and-amenities.md) · [Ecosystem Migration](docs/04-ecosystem/01-migration-and-impact.md).

**Visible accommodation tiles (14):** Room, Cottage, Villa, Apartment, Dormitory, Bungalow, Cabin, Chalet, Treehouse, Tent / Glamping, Houseboat, Capsule / Pod, Holiday Home, Other / Custom.

**Internal stable Core Types (6):** ROOM, COTTAGE, VILLA, APARTMENT, DORMITORY, OTHER. Extra visible cards are **presets**, not additional booking engines; owner can adjust the preset's appropriate Core Type before publishing. Suites/Deluxe/Twin/Studio/Penthouse remain Category/Configuration.

Property Type is single-select; Accommodation Types multi-select. Set Property location/numbering once, reuse forever. Generic physical inventory is Unit; Staff UI uses Rooms if Room-only or Accommodations otherwise; Guest App uses Stays. Local Door Number may repeat in different buildings, but a Full Unit Code is unique per property and a UUID is immutable.

Legacy numbered files 01–10 remain as bookmark redirects. The canonical rules are in this docs/ hierarchy. Never migrate a live Hotel schema directly from a proposal without reconciling current Room/Booking/OTA data.

## Historical versions and original file preservation

**V1.1 original (09 Nov 2026):** [Open full untouched 12-file archive](versions/v1.1-2026-11-09/README.md). The archive includes the original [MASTER-PLAN](versions/v1.1-2026-11-09/MASTER-PLAN.md), [README](versions/v1.1-2026-11-09/README.md) and all 10 numbered detailed specs. The original root filenames `01-...` through `10-...` have also been **restored to their full prior content**, not shortened to redirect notices.

**V1.2 current:** this README, [MASTER-PLAN](MASTER-PLAN.md) and the organized `docs/` hierarchy. Treat canonical V1.2 files as implementation guidance; read V1.1 to preserve context and compare historical decisions. Existing detail is never removed merely because a new revision supersedes it.
