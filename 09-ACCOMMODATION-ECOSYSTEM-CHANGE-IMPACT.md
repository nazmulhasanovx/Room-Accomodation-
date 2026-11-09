# 09 — BinGi Accommodation Migration: Ecosystem-Wide Change Impact Note

**Product:** BinGi Hotel Management Technology (NEONECY)  
**Approved on:** 06 November 2026  
**Status:** APPROVED PRODUCT PLAN — NOT YET IMPLEMENTED  
**Coverage:** HMS Web, Guest App, HMS Mobile App, Booking Website, Super Admin, APIs, Database, Finance, Integrations, QA.

## 0. APPROVED NAMING DECISIONS — LOCKED

| Area | Approved naming | Rule |
| --- | --- | --- |
| HMS Web + HMS App sidebar | Dynamic Rooms / Accommodations | Room-only hotel shows **Rooms**; mixed or non-room property shows **Accommodations** |
| Guest App main navigation and listing | **Stays** | Discover Stays, Available Stays, My Stays |
| Universal physical inventory name | **Unit** | HMS internal labels: Unit / Unit Number; guests see actual Room, Villa, Cottage, Apartment or Dormitory Bed |

### Dynamic sidebar — shared deterministic rule

1. If property has any enabled non-Room type, any non-Room category or existing non-Room unit, label is **Accommodations**.
2. If only Room is enabled and there are no non-Room categories/units, label is **Rooms**.
3. If configuration is incomplete and no type is enabled, onboarding label is **Accommodations**.
4. The same shared policy is used by HMS Web and HMS App, including sidebar, page header and breadcrumbs.
5. Do not change route IDs, permission keys or data models when the visible label changes. Do not hide existing non-Room records if a type was disabled.

**Do NOT mechanically replace every instance of "Room".** Keep Room 101, Room Service, Bedrooms, Room Size and other real Room-specific wording.

## 1. Core architecture — old versus new

~~~text
OLD:
Property -> Room Type -> Room Number -> Room Availability -> Reservation

NEW:
Property Profile
   |
   +-> One-Time Accommodation Configuration
         +-> Enabled Types
         +-> Buildings / Towers / Floors / Zones / Blocks
         +-> Per-type or per-category Location Hierarchy
         +-> Numbering Rules and Shared Defaults
   |
   +-> Accommodation Type
         +-> Room / Cottage / Villa / Apartment / Dormitory / Other
   |
   +-> Category (e.g., Deluxe Twin / Pool Villa / Garden Cottage)
         +-> Default beds, capacity, photos, amenities, price, video tour
   |
   +-> Physical Unit (101 / C03 / V01 / A203 / D101)
         +-> Location (Floor or Zone/Block, as applicable)
         +-> Unit-specific details / overrides
   |
   +-> Sellable Inventory
         +-> Whole physical Unit (most types)
         +-> Bed Space (Dormitory: D101-B01, D101-B02, ...)
   |
   +-> Reservation / Rate / Availability / Operations / Reporting
~~~

### Configuration-first rules

- Save Property Profile, enabled types, location nodes, numbering formats and global defaults **once**.
- A saved category reuses its capacity, beds, rate, amenities, photo gallery and optional YouTube Video Tour.
- For repeating add flows ask only **Type -> Existing Category -> Existing Location -> Unit Numbers -> Review/Create**. Do not ask the owner to reconstruct floors/zones or reenter room type details.
- Recommended location paths: Room and Apartment use Building/Tower -> Floor; Cottage and Villa use Zone/Area -> optional Block; Dormitory uses Building -> Floor; Other uses a configured hierarchy.
- Room floor 1 can add 101, 103, 105 without 102. Villa IDs can be V01, V03; Cottage IDs C01, C03; Apartment IDs A101 or TA-101. Owner can customize templates. Floor 100+ must be supported without identifier collisions.
- A Villa or Apartment with multiple bedrooms is still one whole bookable Unit in V1. A Dormitory room may contain multiple individually bookable Bed Spaces.

## 2. Global naming and text changes

| Previously | New target | Exception |
| --- | --- | --- |
| Rooms (navigation) | Dynamic **Rooms** or **Accommodations** | Approved policy above |
| Room Management | Rooms or Accommodation Management | Match actual property |
| Add Room | **+ New Accommodation** | Optional contextual + Add Room shortcut in room-only screens |
| Room Type | **Accommodation Category** | Room-specific editor may say Room Category |
| Room Type selector | **Accommodation Type selector** | Six main types |
| Room Number | **Unit Number / Unit ID** | Guest: Room 101, Villa V01 |
| Room List | Units / All Units | Room-only lists may use Rooms |
| Room Details | Unit Details | Dynamic title e.g. Villa V01 |
| Room Setup | Accommodation Configuration | One-time setup |
| Room Status | Unit Status | Distinguish operational from booked state |
| Room Location | Location | Floor / Tower / Zone / Block by type |
| Room Assignment | Unit Assignment | Dormitory: Bed Assignment |
| Room Inventory | Accommodation Inventory | Physical Unit and Bed Space counts separate |
| Total Rooms | Total Units | Room-only may show Total Rooms |
| Available Rooms | Available Units | Guest App: Available Stays |
| Room Amenities | Amenities | Shared library |
| Room Gallery | Photos & Video Tour | Optional category-level YouTube, Unit override |
| Room Price | Nightly Rate (Per Unit / Per Bed) | Correct booking mode |
| No rooms available | No stays available | Guest App |
| Rooms & Suites (generic website listing) | Stays / Rooms & Stays | Theme-specific, no missing Villas |

**Protect domain words:** Room Service, Bedrooms, room occupancy, room size and Hotel Room must stay meaningful. UI label changes do not authorize renaming database tables or API endpoints without compatibility work.

## 3. HMS WEB — change impact

**P0 = required for data/booking integrity; P1 = required for full rollout; P2 = polish.**

| Module / Screen | Change | Priority |
| --- | --- | --- |
| Main navigation | Dynamic sidebar, breadcrumbs and stable routes | P0 |
| Property Settings | One-time accommodation config; reuse Property Profile | P0 |
| Room Dashboard | Universal Category/Unit dashboard, type filters and type-wise counts | P0 |
| Add Room / Room Setup | One shared five-step New Accommodation Wizard | P0 |
| Room Type Editor | Category templates, dynamic type-specific fields, media and default policies | P0 |
| Building / Floor setup | Tree supports Building, Tower, Floor, Zone, Block and custom where needed | P0 |
| Room Number modal | Saved numbering engine; single, bulk, manual and nonconsecutive selection | P0 |
| Room Details | Dynamic Unit Details, true type/identifier/location/overrides | P0 |
| Booking Calendar | Mixed unit rows; Dormitory bed inventory and unassigned lane | P0 |
| Reservations | Type/category selection; whole Unit or Bed assignment and modification | P0 |
| Rates & Inventory | Per Unit vs Per Bed price, date-wise restrictions and availability | P0 |
| Front Desk / Check-in/out | Correct Unit or Bed assignment, arrivals/departures, changes and conflicts | P0 |
| Maintenance | Unit and Bed maintenance; protect affected reservations | P0 |
| Housekeeping | Villa, Cottage, Apartment, Room and Dormitory cleaning tasks | P1 |
| In-house Guests | Correct accommodation label, category and location | P1 |
| Guest Profile / History | Display accommodation type and real assigned Unit/Bed | P1 |
| Finance & Revenue | Correct charge line items, receipts, folio, rates and taxes | P1 |
| Reports | New type-wise analytics; no accidental bed-count inflation | P1 |
| Staff Tasks | Locations as Building/Floor or Zone/Block | P1 |
| Notifications | Type-specific messages and correct unit ID | P1 |
| Settings, Permissions | Configure Location/Category/Units/Bed separately by role | P1 |
| Audit Events | Track config, category, unit and bed changes | P1 |
| Import / Export | Legacy Rooms CSV compatibility plus new fields | P1 |
| Shared search | Find by category, Unit ID, location, type and status | P1 |
| Forms, printouts, empty states | Rename semantically, with consistent translations | P2 |

### HMS special rule

Dashboard occupancy/ADR/RevPAR/Total Rooms semantics must be reassessed. A 2-bedroom Villa is **one whole Unit**, not two Rooms. Dormitory bed occupancy must be measured separately from physical room occupancy. Do not silently mix denominators in historical reports.

## 4. GUEST APP — approved browsing name: STAYS

| Guest App feature | Change needed |
| --- | --- |
| Home and Search | **Explore Stays**, dates, location, guests and availability |
| Listings | Universal Stays listing, real category/type cards |
| Filters | Room, Cottage, Villa, Apartment, Dormitory, Other |
| Category cards | Category title, type, adults/children, bedroom/bed count, photos and prices |
| Stay Details | Type-specific features: private pool, kitchen, shared bathrooms or bed position |
| Photo Gallery | Optional **Watch Video Tour** next to Photos (category default, optional Unit override) |
| Availability | Whole-unit or bed-wise date availability; no double-book |
| Rates and fees | State **per night / entire Unit** or **per Bed per night** clearly |
| Capacity | Respect category adults/children and booking unit capacity |
| Checkout and Cart | Preserve type, category, selected/allocated Unit or Bed, pricing/fees |
| My Stays | Correct category, assigned unit/bed, dates, guest services |
| Booking confirmation | Display exact Unit only when assigned; otherwise display booked category |
| Modifications and cancellations | Release/allocate the right whole Unit or Bed |
| Digital Check-in | Show correct assigned Room, Villa, Apartment, Cottage or Dormitory Bed |
| Reviews | Review relevant property/category; preserve history |
| Empty state | **No stays available** for requested dates |
| Room Service / requests | Keep recognizable service names; route request to correct Unit |

### Guest-facing illustration

~~~text
STAYS
 -> [Filter: Villa]
 -> Private Pool Villa
 -> Photos | Watch Video Tour (when configured)
 -> Bedrooms: 2 | Max Guests: 6 | Private Pool
 -> Price: per entire Villa per night
 -> Select Dates -> Check Availability -> Book
 -> My Stays -> Private Pool Villa
     -> Villa V01 (show only if this exact unit has been assigned)
~~~

**Video Tour requirement:** Link is optional at Category Create/Edit -> Media & Gallery. Existing Units inherit it without asking owner for a link repeatedly; a genuinely different Unit can override or hide it. A representative category video cannot be advertised as the guaranteed exact assigned unit. Video is lazy loaded after click; unavailable videos fall back to photos.

## 5. HMS MOBILE APP — staff operations

| App surface | Required change |
| --- | --- |
| Sidebar / Navigation | Same approved **dynamic Rooms / Accommodations** logic as HMS Web |
| Dashboard | Unit counts, type mix and separate Dormitory bed-space counts |
| Reservations | Actual type, category and assigned Unit/Bed |
| Check-in / Check-out | Whole-unit or bed-aware workflows |
| My Tasks | Attach cleaning, maintenance, deliveries to correct Unit or Bed |
| Housekeeping | Correct templates and complete physical location |
| Maintenance | Room/Villa/Cottage/Apartment/Dormitory bed issues, conflict handling |
| Unit Details | Dynamic title and type-specific features, status, notes, media |
| Search | Search by Unit ID, Category, type, Building/Floor/Zone/Block |
| Notifications | e.g., Garden Zone > Cottage C03; Main Building > Floor 2 > Room 203 |
| Offline sync/cache | Type/category/location/Unit/Bed schema support |
| Permissions | Only authorized staff can create/edit config or inventory |
| Deep links | Old Room links resolve to stable mapped Unit IDs |

Do not display a forced Floor field for a Cottage/Villa when its category uses Zone/Block. Keep Room Service if that feature means guest service, not physical inventory.

## 6. PUBLIC HOTEL/RESORT WEBSITE + THEMES + BOOKING WIDGET

- Update room-only pages, menus, cards and home booking bar to support all six accommodation types.
- For generic browse sections choose **Stays** or theme-suitable **Rooms & Stays**, without hiding Villa/Cottage/Apartment inventory.
- Existing website themes (e.g., NEST, CITY, ESCAPE, GRAND) need a type-aware design/content audit.
- Category pages display suitable features: beds, bedrooms, kitchen, pool, shared/private bathrooms, guests and night/bed price.
- Media gallery supports optional **YouTube Video Tour** on Category/Unit details; not every external channel will support video.
- Search, calendar availability, promotional offers, rate restrictions, checkout and booking confirmation must use the new shared inventory engine.
- SEO labels, metadata, structured data, links, share cards and empty states should reflect actual accommodation.
- Exact Unit number should not be promised on category-only bookings where assignment happens later.

## 7. BACKEND, API, DATABASE, BOOKING ENGINE — mandatory audit

**No blind destructive SQL renames. No existing reservations, invoices, maintenance records or audit links should be broken.**

### Proposed conceptual entities (adapt to existing production schema)

~~~text
Property
 +-> AccommodationConfiguration
 +-> LocationNode (Building/Tower/Floor/Zone/Block)
 +-> NumberingRule
 +-> AccommodationCategory (type, capacity, beds, photos, video, pricing)
       +-> PhysicalUnit (immutable ID, unique display code, location)
             +-> BedSpace (Dormitory only)
 +-> ReservationAllocation (sellable unit or sellable bed + dates)
 +-> Rates / Restrictions / Calendar / Housekeeping / Maintenance
 +-> Guest Stays / Invoice / Audit Events
~~~

| Component | Migration and compatibility work |
| --- | --- |
| Legacy Room table | Map into Room-type physical Units; preserve stable identity/mapping |
| Legacy Room Types | Map to Categories of type Room |
| Room Number | Maintain as legacy alias while exposing generic Unit display code |
| Location fields | Building/Floor/Zone/Block tree, saved category strategy |
| Number generators | Property-wide uniqueness, prefix, floor token, separators and bulk preview |
| Reservations | Unit/Bed allocation and category-only unassigned records supported |
| Calendar | Unit vs Bed inventory, accurate conflict checking and availability |
| Rates | Category defaults, Unit overrides, per-unit or per-bed billing |
| Maintenance | Correct Unit/Bed link, dated out-of-service blocks |
| Housekeeping | Type-specific cleaning targets and saved location |
| Invoice/Folio/Refund | Preserve historical values, make new labels dynamic |
| Reports | Reconcile historical room statistics and new Unit/Bed counts |
| API/Webhooks | Add compatible fields; maintain older clients during migration |
| Permissions | Property-level access and separate Configure/Create/Assign privileges |
| Search/Import/Export | Support both legacy Room and new Unit fields |
| Subscription | Track physical units and Dormitory beds separately until commercial policy approved |
| Audit | Record actor/time/before/after on sensitive changes |
| Migration/rollback | Backups, staged backfill, ID mapping, feature flags, reconciliation |

### Booking integrity rules

- Room, Cottage, Villa, Apartment and default Other = **one physical Unit is one sellable whole unit**.
- Dormitory = **one physical room with multiple independently sellable Bed Spaces** when bed-wise mode is enabled.
- Entire Dormitory booking must block all Bed Spaces for overlapping date ranges, and fail if any bed is already sold.
- Atomic booking allocations and real DB constraints/locking are required; UI preview alone does not prevent concurrency conflicts.
- Operational active/maintenance/inactive state is distinct from booked/available status for a date range.
- Historic reservation, payment, invoice and OTA identifiers remain resolvable.

## 8. SUPER ADMIN, FINANCE, REPORTS, NOTIFICATIONS AND INTEGRATIONS

### Super Admin
- Monitor enabled accommodation types, category counts, physical Unit counts, Bed Space counts and setup completion per property.
- Manage plan-based access and physical-unit capacity quotas without accidentally counting bed spaces as whole rooms.
- Show migration warnings, missing mappings and auditing.

### Finance, Revenue and Reports
- Correct folio/invoice/receipt nomenclature (Room-night, Villa-night, Cottage-night, Apartment-night, Bed-night).
- Retain existing invoices, tax/fee calculations, refunds, payments and booking-related meals/services.
- New type-wise inventory, occupancy, revenue and average rates; keep Dormitory bed metrics separate.
- Check daily sales and finance exports with both old Room and new accommodation bookings.

### Communications / Documents
- Review SMS, email, push, WhatsApp and guest confirmations for dynamic type/category/Unit/Bed placeholders.
- Review ID/registration cards, arrival lists, internal housekeeping slips, printable receipts, invoices and vouchers.
- Keep service names like Room Service intact where meaningful.

### OTA / Channel Manager
- Audit existing external Room Type and Rate Plan mappings (Booking.com, Expedia and other channels).
- Map internal Category to partner room/rate-type identifiers; do not assume partners use internal Unit codes.
- Preserve existing mapping IDs, imported reservations, cancellation/modification flows and availability sync.
- Assess Dormitory bed-level channel support separately; do not expose untested granular inventory.
- Existing OTA/API support must be verified independently; this document does not claim it is already implemented.
- YouTube videos displayed on BinGi website/Guest App need not automatically appear on external OTA channels.

## 9. Example copy by context

| Situation | Correct display |
| --- | --- |
| Only Rooms enabled | HMS sidebar: **Rooms** |
| Room + Villa enabled | HMS sidebar: **Accommodations** |
| Only Villas enabled | HMS sidebar: **Accommodations** |
| Guest App navigation | **Stays** |
| Guest empty state | **No stays available** |
| Generic HMS inventory | **Total Units** |
| Specific assigned Room | **Room 101** |
| Specific assigned Cottage | **Cottage C03** |
| Specific assigned Villa | **Villa V01** |
| Specific assigned Apartment | **Apartment A203** |
| Specific Dormitory bed | **Dormitory D101 · Bed B03** |
| Dormitory 2 rooms x 8 beds | **2 Physical Units · 16 Bed Spaces** |
| Category booked, unit not allocated | **Deluxe Twin Room · Unit assigned later** |

IDs/counts above are examples, not records to insert into real hotels.

## 10. DEVELOPMENT MIGRATION PLAN

### Phase 0 — Discovery / safeguards
- [ ] Audit actual HMS Web, Guest App, HMS Mobile, website themes and current endpoints.
- [ ] Find Room-only labels, screen IDs, SQL schema, reservation relationships and reports.
- [ ] Snapshot and back up all data; document rollback and risk of live client disruption.
- [ ] Decide legacy Room ID mapping and API backward-compatibility window.

### Phase 1 — Data foundation (P0)
- [ ] Implement Property Configuration reused once per property.
- [ ] Implement location tree + category strategies + numbering templates.
- [ ] Implement Accommodation Type, Category, Unit and Dormitory Bed entities.
- [ ] Backfill Room Types / Rooms into new categories / Room units.
- [ ] Preserve all historical and live reservations, maintenance, guest and invoice relationships.
- [ ] Expose backward-compatible APIs and feature flags.

### Phase 2 — HMS Web (P0)
- [ ] Dynamic Rooms / Accommodations navigation.
- [ ] Unified 5-step wizard with same button positions and conditional fields.
- [ ] Category media with optional YouTube URL; Unit override.
- [ ] Mixed-type dashboard, Room/Unit Calendar, inventory, rates, reservation and front desk changes.
- [ ] Operational status, draft/publish, duplicate and booking-conflict protection.

### Phase 3 — Guest App / public booking (P0/P1)
- [ ] **Stays** navigation, listing, filters and detail pages for all types.
- [ ] Price per unit vs per bed, correct booking/checkout/payment/invoice.
- [ ] Booking confirmations and My Stays, cancellation/modify and digital check-in.
- [ ] Video Tour optional playback and fallback.
- [ ] Update all website themes and booking widget behavior.

### Phase 4 — HMS Mobile App (P1)
- [ ] Dynamic navigation, search and correct Unit/Bed labels.
- [ ] Housekeeping, maintenance, front desk, staff tasks and notifications.
- [ ] Offline sync and deep-link compatibility.

### Phase 5 — Cross-system / QA / staged release (P1)
- [ ] Reports, tax/finance, notifications, imports/exports and Super Admin.
- [ ] OTA/channel mapping, webhooks, subscription counts and external dependencies.
- [ ] Regression tests and staged data reconciliation on current paying properties.
- [ ] Feature-flagged rollout, monitoring, clear rollback and audit trail.

## 11. RISK REGISTER / REQUIRED SAFETY

| Risk | Required control |
| --- | --- |
| Changing Room label everywhere | Context-aware copy review; do not break Room Service etc. |
| New schema breaks old Room IDs | Stable immutable IDs and legacy aliases/mapping |
| Identical 101 in multiple Towers | Property-wide unique display code / Tower prefixes |
| Configuration edit renumbers live Units | Preserve existing IDs; preview and audit impact |
| Bed vs room double booking | Transactional availability engine and conflict locking |
| Old mobile app cannot parse new API | Compatibility adapters / staged client rollout |
| Mixing Dormitory beds into Total Rooms | Separate counts, denominator definitions and reports |
| OTA category mapping drift | Verified partner mappings and sync tests |
| Invalid YouTube URL / private video | Validated link, lazy embed, photos fallback |
| Misleading exact unit guarantee | Show Unit only when assigned; category video may be representative |

### Open technical/commercial questions (not part of approved naming)

- Exact production migration SQL, database field names, API versioning and fallback lifecycle depend on current real codebase.
- Subscription limits: physical Units and Bed Spaces tracked separately; pricing policy to be decided.
- OTA bed-wise support and channel mapping per partner must be validated.
- Supported mobile app versions, website-theme rollout scope and cut-over timing require engineering review.

## 12. RELEASE ACCEPTANCE CHECKLIST

- [ ] Room-only HMS Web and HMS App show **Rooms**, mixed / non-Room show **Accommodations**.
- [ ] Guest App lists are named **Stays**; actual item titles still identify Room/Cottage/Villa/etc.
- [ ] HMS generic physical inventory calls each physical item a **Unit**; guest sees natural labels.
- [ ] Old Room 101, Room Type, bookings, folios and audit/maintenance references remain intact.
- [ ] Once property location and numbering are configured, repeated Unit additions don't ask for setup again.
- [ ] Room Floor 1 can add 101 and 103, skip 102 and block duplicates.
- [ ] Cottage/Villa Zone/Block workflow does not require Floor.
- [ ] Apartment Tower/Floor IDs remain unambiguous and unique.
- [ ] Dormitory Bed booking and entire-room booking are mutually conflict-safe on overlapping dates.
- [ ] Calendar, availability, front desk, housekeeping, maintenance, finance and reports work after migration.
- [ ] Guest Stays, photo/video gallery, checkout, confirmation and My Stays work for all supported types.
- [ ] Permission scoping, historical data, mobile sync and OTA mappings pass staging regression.
- [ ] No change is deployed until verified against current production records with rollback ready.

## 13. Supporting documentation

- [MASTER-PLAN.md](MASTER-PLAN.md) — universal architecture and setup wizard.
- [01-PROPERTY-CONFIGURATION.md](01-PROPERTY-CONFIGURATION.md) — first-time setup, locations and numbering.
- [02-ROOM-SETUP.md](02-ROOM-SETUP.md) — Hotel Rooms.
- [03-COTTAGE-SETUP.md](03-COTTAGE-SETUP.md) — Cottages.
- [04-VILLA-SETUP.md](04-VILLA-SETUP.md) — Villas.
- [05-APARTMENT-SETUP.md](05-APARTMENT-SETUP.md) — Apartments.
- [06-DORMITORY-SETUP.md](06-DORMITORY-SETUP.md) — Dormitory rooms and bed inventory.
- [07-OTHER-CUSTOM-SETUP.md](07-OTHER-CUSTOM-SETUP.md) — Other/Custom.
- [08-VALIDATION-AND-QA.md](08-VALIDATION-AND-QA.md) — QA and edge cases.

**Handoff:** This is an approved migration **plan and impact checklist**, not a production implementation report. The changes must be designed, implemented, migrated and tested before launch.
