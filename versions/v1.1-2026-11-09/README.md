# BinGi HMS — Accommodation Management Blueprint

**Product:** BinGi Hotel Management Technology (by NEONECY)  
**Market:** Bangladesh-first; scalable to multi-property hospitality operations  
**Documentation:** Planning specification, not implemented production code  
**Version:** V1.0 | 06 November 2026

## Purpose
A single, consistent management flow for six accommodation options: **Room, Cottage, Villa, Apartment, Dormitory, Other / Custom**. Property owners configure their property structure and number formats once; staff subsequently create accommodation categories and physical inventory without reentering the same data.

## Read in this order
1. [MASTER-PLAN.md](MASTER-PLAN.md) — complete architecture, rules, flows, screen map, booking logic and rollout.
2. [01-PROPERTY-CONFIGURATION.md](01-PROPERTY-CONFIGURATION.md) — one-time property setup, locations, hierarchy and numbering.
3. [02-ROOM-SETUP.md](02-ROOM-SETUP.md) — room categories and floor-based room numbers.
4. [03-COTTAGE-SETUP.md](03-COTTAGE-SETUP.md) — zones, optional blocks and cottage IDs.
5. [04-VILLA-SETUP.md](04-VILLA-SETUP.md) — private villas, multiple bedrooms and whole-villa inventory.
6. [05-APARTMENT-SETUP.md](05-APARTMENT-SETUP.md) — apartment types, towers, floors and unit IDs.
7. [06-DORMITORY-SETUP.md](06-DORMITORY-SETUP.md) — rooms, bed spaces, per-bed pricing and bookings.
8. [07-OTHER-CUSTOM-SETUP.md](07-OTHER-CUSTOM-SETUP.md) — constrained, configurable nonstandard accommodation.
9. [08-VALIDATION-AND-QA.md](08-VALIDATION-AND-QA.md) — acceptance criteria and regression checklist.
10. [09-ACCOMMODATION-ECOSYSTEM-CHANGE-IMPACT.md](09-ACCOMMODATION-ECOSYSTEM-CHANGE-IMPACT.md) — approved migration impact checklist across HMS Web, Guest App, HMS Mobile App, booking websites, APIs, finance and integrations.

## Approved cross-platform naming (06 Nov 2026)
- **HMS Web & HMS Mobile sidebar:** dynamic **Rooms** for room-only properties; **Accommodations** for mixed or non-Room properties.
- **Guest App browse/listing:** **Stays**.
- **Generic physical inventory:** **Unit / Unit Number**, with natural type-specific guest labels such as Room 101 and Villa V01.
- See the [full ecosystem migration note](09-ACCOMMODATION-ECOSYSTEM-CHANGE-IMPACT.md) before renaming other modules or changing schemas.

## Locked V1 design decisions
- Property type is separate from accommodation type; Suite, Deluxe, Twin, Double are **categories / attributes**, not six new top-level types.
- **Configure once → reuse always**: property profile, buildings, floors, zones, blocks, location strategy, codes and defaults.
- Categories define product characteristics; physical units define inventory; dormitory beds are independently sellable spaces.
- One familiar 5-stage wizard with conditional fields. Dormitory Bed Configuration is nested inside Units & Inventory, not an unrelated user experience.
- A category chooses an applicable location hierarchy; **Floor is not imposed on Villas / Cottages**.
- Auto-suggest IDs but support nonconsecutive numbers (e.g., 101, 103) and deliberate overrides with validation.
- Every property-scoped physical unit identifier must be unique under the saved code policy. Database IDs remain immutable.
- Prevent destructive changes affecting active bookings, bed allocations, maintenance, rates or linked reports.

## Operating idea
~~~text
Property Profile
   -> One-Time Accommodation Configuration
   -> Accommodation Dashboard
   -> Select Type
   -> Select Existing Category OR Create Category
   -> Select Preconfigured Building/Floor or Zone/Block
   -> Generate or Select Unit IDs
   -> Review Inherited Defaults
   -> Draft / Publish
   -> Booking Inventory
~~~

The repository is a **product/design/developer handoff**, not a claim that these features already exist in the deployed HMS. Technical implementation choices should be validated with the current backend schema before migration.
