# 10 — Property Types, Accommodation Mapping, Location Hierarchy & Numbering

> **BinGi HMS · Product / UX / Engineering Specification · V1.1 — 09 November 2026**  
> **Owner:** NEONECY — BinGi Hotel Management Technology  
> **Status:** Product design and implementation handoff. The recommendations below are **not a claim that the feature is already deployed**.  
> **Related sources:** [MASTER-PLAN](MASTER-PLAN.md) · [01 Property Configuration](01-PROPERTY-CONFIGURATION.md) · [08 Validation & QA](08-VALIDATION-AND-QA.md) · [09 Ecosystem Change Impact](09-ACCOMMODATION-ECOSYSTEM-CHANGE-IMPACT.md).

## 1. Product decisions and boundaries

**A Property Type answers "What kind of business/property is this?"** It is a single-select classification on the Property Profile. **An Accommodation Type answers "What physical kind of stay is managed and sold?"** It is a multi-select set of enabled inventory types.

- **10 proposed Property Type choices** for the property profile (section 2).
- **Exactly 6 V1 core Accommodation Types:** Room, Cottage, Villa, Apartment, Dormitory, Other / Custom.
- **Categories are not types.** Standard, Deluxe, Suite, Twin, Double, Family, Studio, 1BR, Pool Villa and Garden Cottage are categories, configurations or attributes.
- **Do not hard-lock compatibility.** Property Type drives recommended Accommodation Type presets but the owner may enable any of the six, provided its inventory and booking rules are supported.
- **One-time configuration** of location nodes, enabled types, per-type/category location strategy, numbering templates and defaults. Subsequent adds reuse saved values.
- **Physical location ≠ accommodation's internal layout.** A two-level Villa is *one villa unit* in a zone; its private internal levels are not hotel Floor inventory.
- **Unique display identifiers** must be validated in the property scope; immutable database IDs are separate. No silent renumbering when templates change.
- **Location hierarchy is a tree, not a mandatory fixed stack.** Building → Floor and Zone → Block are both valid; smaller properties may require neither.

This document expands the existing master plan. If a proposed property label or compatibility preset conflicts with a real client's operational model, use the configurable strategy; do not change the six core accommodation types.

## 2. Property Type library (single-select)

| No. | Property Type | Typical use | Recommended Accommodation Types (defaults only) |
|---:|---|---|---|
| 01 | **Hotel** | City, business, boutique, luxury hotel | Room, Apartment, Dormitory |
| 02 | **Resort** | Beach, hill, eco, leisure resort | Room, Cottage, Villa, Apartment, Dormitory, Other |
| 03 | **Guest House** | Small guest property | Room, Apartment, Dormitory |
| 04 | **Motel** | Roadside / highway stay | Room, Cottage, Apartment |
| 05 | **Hostel** | Shared-bed and budget stays | Dormitory, Room, Apartment |
| 06 | **Homestay** | Hosted home and family stays | Room, Apartment, Cottage, Villa |
| 07 | **Serviced Apartment** | Managed furnished apartment lodging | Apartment, Room, Villa |
| 08 | **Vacation Rental** | Holiday house / unit rentals | Villa, Cottage, Apartment, Room, Other |
| 09 | **Lodge / Retreat** | Nature, wellness and rural stays | Room, Cottage, Villa, Dormitory, Other |
| 10 | **Other / Custom Property** | Unlisted business classification | Room, Cottage, Villa, Apartment, Dormitory, Other |

### 2.1 Property → Accommodation visual tree

~~~text
BINGI PROPERTY PROFILE [single Property Type]
|
+-- 01 HOTEL ----------------> Room | Apartment | Dormitory
+-- 02 RESORT ---------------> Room | Cottage | Villa | Apartment | Dormitory | Other
+-- 03 GUEST HOUSE ----------> Room | Apartment | Dormitory
+-- 04 MOTEL ----------------> Room | Cottage | Apartment
+-- 05 HOSTEL ---------------> Dormitory | Room | Apartment
+-- 06 HOMESTAY -------------> Room | Apartment | Cottage | Villa
+-- 07 SERVICED APARTMENT ---> Apartment | Room | Villa
+-- 08 VACATION RENTAL ------> Villa | Cottage | Apartment | Room | Other
+-- 09 LODGE / RETREAT ------> Room | Cottage | Villa | Dormitory | Other
+-- 10 OTHER / CUSTOM -------> Any of the 6 core types
                             (recommendations, not restrictions)
~~~

**UX:** Show property options as visual icon + title + one-line description. Store stable enum/slug independently of the display label. Allow "Other" to capture a safe custom property label. Possible future subtypes such as *Beach Resort*, *Eco Resort*, *Boutique Hotel*, *Business Hotel* belong under the parent Property Type; do not bloat the V1 list.

### 2.2 Configuration example: one property, multiple unit kinds

~~~text
PROPERTY: Azure Bay Grand Resort
Property Type: Resort
|
+-- Enabled: [x] Room     -> Deluxe Twin, Sea View
+-- Enabled: [x] Cottage  -> Garden Cottage, Lake Cottage
+-- Enabled: [x] Villa    -> Pool Villa
+-- Enabled: [ ] Apartment
+-- Enabled: [ ] Dormitory
+-- Enabled: [ ] Other / Custom
|
+-- Room category and units are distinct:
    Deluxe Twin [CATEGORY]
       -> Main Building / Floor 1 -> units 101, 103, 105
       -> Main Building / Floor 2 -> units 201, 203
~~~

When the property type is changed later, **do not automatically delete or disable existing types, categories or units**; show an impact review and keep booked inventory intact.

## 3. The six accommodation types (multi-select)

| Core Type | Example categories / configurations | Bookable inventory in V1 |
|---|---|---|
| **Room** | Standard, Deluxe, Suite, Twin, Double, Family | One whole physical room |
| **Cottage** | Garden, Lake View, Family, Premium | One whole cottage |
| **Villa** | Pool, Garden, Private, Beachfront | One whole villa, not its internal bedrooms |
| **Apartment** | Studio, 1BR, 2BR, Penthouse | One whole apartment, not its internal bedrooms |
| **Dormitory** | Shared, mixed, male/female policy where applicable | Bed-space in bed-wise mode; exclusive whole-room option must block all beds |
| **Other / Custom** | Tent, Dome, Treehouse, Cabin, Houseboat | Configured whole-unit mode; per-bed only through supported Dormitory engine/policy |

**Naming:** HMS sidebar shows **Rooms** for a room-only property and **Accommodations** for mixed / non-room properties. Guest browse label is **Stays**. Generic inventory noun is **Unit**; type-specific labels should read "Room 101", "Villa V01", and so on. See [09 Ecosystem Change Impact](09-ACCOMMODATION-ECOSYSTEM-CHANGE-IMPACT.md).

## 4. Location type library and hierarchy

### 4.1 Reusable node types

| Location Node Type | Common aliases / example | Meaning | Usually used for |
|---|---|---|---|
| **Building** | Main Building, Building A | Physical structure | Room, Apartment, Dormitory |
| **Tower** | Tower A, Tower B | Tall / named building variant | Apartment, Hotel rooms |
| **Block** | Block A, Block B | Subdivision/group | Resort and hotel |
| **Wing** | East Wing, West Wing | Wing of an existing building | Room, Apartment |
| **Floor** | Ground, 1, 2, 10, 100 | Building storey used for physical-unit location | Room, Apartment, Dormitory |
| **Level** | Mezzanine, B1, Level 1 | Configurable floor-equivalent only if the property uses it | Floor-oriented unit types |
| **Zone** | Garden Zone, Beachfront Zone | Outdoor spatial zone | Cottage, Villa |
| **Area** | Lake Area, Pool Area | Spatial region | Cottage, Villa, Custom |
| **Section** | Section A | Optional grouping within a configured region | Any, where useful |
| **Cluster** | Cottage Cluster A | Group of standalone units | Cottage, Villa, Custom |

**Implementation recommendation:** Render paired aliases as one configured kind where feasible (Building/Tower, Block/Wing, Floor/Level, Zone/Area, Section/Cluster); keep a stable machine-readable kind plus user-facing preferred label. Do not force every property to use each kind.

### 4.2 Not every property follows the same stack

~~~text
PROPERTY ROOT
|
+-- HOTEL EXAMPLE
|   +-- Main Building [BUILDING]
|       +-- East Wing [WING] (optional)
|       |   +-- Floor 1 [FLOOR] -> Rooms 101, 103
|       |   +-- Floor 2 [FLOOR] -> Rooms 201, 203
|       +-- Floor 3 [FLOOR]     -> Rooms 301, 303
|
+-- RESORT EXAMPLE
|   +-- Garden Zone [ZONE]
|   |   +-- Block A [BLOCK] -> Cottages C01, C03
|   |   +-- Block B [BLOCK] -> Villas V01, V02
|   +-- Lake Zone [ZONE]   -> Cottages L-C01, L-C02
|
+-- APARTMENT PROPERTY EXAMPLE
|   +-- Tower A [TOWER]
|   |   +-- Floor 1 [FLOOR] -> Apartments TA-101, TA-102
|   +-- Tower B [TOWER]
|       +-- Floor 1 [FLOOR] -> Apartments TB-101, TB-102
|
+-- HOSTEL EXAMPLE
    +-- Hostel Building [BUILDING]
        +-- Floor 1 [FLOOR]
            +-- D101 [DORMITORY UNIT]
                +-- D101-B01 [BED SPACE]
                +-- D101-B02 [BED SPACE]
~~~

The examples illustrate **separate possible structures**, not a single mandatory hierarchy. Each location node needs immutable ID, property ID, parent ID, kind, display name, optional code, sort order and active status; parents and children must form a valid acyclic tree.

### 4.3 Type → suggested location path

| Accommodation | Recommended saved location path | Optional / alternative | Never force |
|---|---|---|---|
| **Room** | Building → Floor | Building → Wing → Floor; Floor only; location-free if configured | Creating a new Building on each add |
| **Cottage** | Zone → Block | Zone only; Area → Cluster; standalone | Hotel Floor |
| **Villa** | Zone → Block | Zone only; Area → Cluster; standalone | Hotel Floor for internal villa levels |
| **Apartment** | Tower/Building → Floor | Building → Wing → Floor; standalone | Zone if irrelevant |
| **Dormitory** | Building → Floor | Floor only; configured valid alternative | Making each bed a Room category |
| **Other / Custom** | Chosen path per custom kind | Zone, Building, none | Fixed universal path |

Store a **default location strategy per Accommodation Type**, optionally overridden by Category at category setup. When adding a unit, load the saved strategy and show only actual configured nodes.

## 5. Numbering engine — six visible templates

| Numbering Mode | Examples | Recommended for |
|---|---|---|
| **Floor-based** | 101, 102, 201, 202 | Room, Apartment |
| **Sequential** | 001, 002, 003 | Cottage, Villa |
| **Prefix-based** | C01, V01, A101, D101 | Cottage, Villa, Apartment, Dormitory |
| **Block-based** | A-101, B-101 | Hotels / large resorts |
| **Zone-based** | G-C01, L-C01, BF-V01 | Resort Cottage / Villa |
| **Custom / Manual** | SKY-501, HB01 | Other and exceptions |

Each saved template should contain: accommodation type, applicable location strategy, prefix/suffix, separator, location token(s), floor token and label, sequence start, sequence width, reserved values, uniqueness scope, allowed operator overrides, rule version and effective date if needed.

### 5.1 Recommended Floor-based numbering

~~~text
Floor 1   -> 101 - 199   (suggest 101, 102, 103, ...)
Floor 2   -> 201 - 299
Floor 3   -> 301 - 399
Floor 4   -> 401 - 499
Floor 10  -> 1001 - 1099
Floor 100 -> 10001 - 10099
~~~

These ranges assume **up to 99 units per floor with a two-digit unit sequence 01–99**. They are only preset examples, not a hard cap. For floors with more than 99 units or special floor codes, use a separator, a sufficiently wide fixed sequence, or another explicitly configured template to avoid collisions. E.g., \`F100-001\`, \`F1-001\`, \`B1-01\`, \`G-01\`. Never rely on naive concatenation when multiple token widths could produce the same string.

### 5.2 Dormitory parent/child numbers

~~~text
Dormitory Unit: D101
  Bed 01 -> D101-B01
  Bed 02 -> D101-B02
  Bed 03 -> D101-B03
  Bed 04 -> D101-B04

Dormitory Unit: D102
  Bed 01 -> D102-B01
~~~

A Bed ID belongs to one Dormitory Unit. Distinguish **bed inventory** from a parent room and prevent overlapping exclusive-room and bed-wise bookings.

### 5.3 Identifier uniqueness and collision examples

~~~text
ONE PROPERTY, TWO BUILDINGS:
  Tower A / Floor 1 / Room 101
  Tower B / Floor 1 / Room 101   <- ambiguous display code

RECOMMENDED DISPLAY CODES:
  TA-101 and TB-101              <- valid, property-unique

DIFFERENT UNIT TYPES, ONE PROPERTY:
  C01 (Cottage), V01 (Villa)    <- valid and distinguishable
  101 (Room), 101 (Apartment)   <- reject unless renamed to unique IDs
~~~

- Validate uniqueness **property-wide** for physical-unit display codes, regardless of type and location; physical UUID remains the primary identity.
- Bed-space IDs must map unambiguously to their parent; enforce uniqueness under the saved bed-code policy.
- Prevent conflicting simultaneous creates using transactional inserts/database constraints, not only UI checks.
- An edit to an existing numbering rule affects **future suggestions** by default. Do not rewrite booked units, external channel references or historical invoices silently.
- Reserved, unavailable and already-used numbers appear clearly; there is no implicit overwrite.

## 6. Selectable number library — key BinGi UX

**Preserve the simple existing interaction:** Instead of making the operator type every location and code, open a **full visual library** of preconfigured blocks/floors/available unit slots. The operator can choose nonconsecutive numbers from several saved locations in one session.

~~~text
+ ADD ROOM  |  Category: Deluxe Twin
  Saved strategy: Building -> Floor  |  Number template: 101
  |
  +-- LOCATION LIBRARY (saved during one-time configuration)
  |   +-- Main Building (already exists)
  |   |   +-- Floor 1
  |   |   |   [x] 101  [ ] 102  [x] 103  [ ] 104  [x] 105
  |   |   +-- Floor 2
  |   |       [x] 201  [ ] 202  [x] 203
  |   +-- Annex Building
  |       +-- Floor 1
  |           [ ] A-101 [ ] A-102 [ ] A-103
  |
  +-- SELECTED UNITS: 101, 103, 105, 201, 203
  +-- [Generate N] [Range] [Manual IDs] [Clear]
  +-- [Back]                              [Save & Continue]
~~~

The same reusable picker switches its grouping context for Cottage (**Zone → optional Block**) and Villa (**Zone → optional Block**), Apartment (**Tower → Floor**) and Dormitory (**Building → Floor**, followed by bed setup). The selected numbers always map to the exact saved location ID, not just a text label.

**Interaction rules:**
1. Show the whole relevant saved hierarchy in a compact, searchable, scrollable library; expand/collapse groups and allow multi-select across groups where codes are unique.
2. A room number option clearly distinguishes **available to create**, **already in inventory**, **reserved**, **selected**, and **invalid/conflicting**. "Available to create" does not mean available to book for all dates.
3. Selecting a number immediately updates the selected list; allow deselection and per-location group actions.
4. One existing Building/Zone is preselected; multiple existing choices are selectable. Never reopen the entire location setup on each add.
5. Offer quick actions **Single / Generate N / Range / Manual List / Visual Selection**; allow gaps such as 101, 103, 105.
6. If a code conflicts, explain which code/location causes the conflict and allow a safe correction. Optional **Skip taken** must preview skipped IDs before saving.
7. Amenities use a **full reusable library picker** with search, categories, multi-select and selected-count summary; do not require repetitive manual reentry.
8. Keep stepper, Back, Save Draft and primary CTA positions consistent for all six types, with desktop-first compact, clean and responsive UI. Category template data is inherited; show only differences/overrides.

### 6.1 Type-specific numbering UX examples

~~~text
ADD COTTAGE
  Category: Garden Cottage
  Zone: Garden Zone
  Block: A (only if this zone has blocks)
  Number suggestions: [x] C01 [ ] C02 [x] C03
  -> 2 cottages, each a separately bookable whole unit

ADD VILLA
  Category: Pool Villa
  Zone: Beachfront Zone
  Number suggestions: [x] V01 [ ] V02 [x] V03
  Internal levels: 2 (category detail, NOT location Floor)
  -> 2 whole-villa units, not 4 units

ADD APARTMENT
  Category: 2BR Apartment
  Tower: A
  Floor: 2
  Number suggestions: [x] TA-201 [x] TA-202
  -> 2 whole-apartment units

ADD DORMITORY
  Category: Shared 8 Beds
  Building: Hostel Building
  Floor: 1
  Dormitory Number: D101
  -> Bed layout/numbering: D101-B01 to D101-B08
  -> 1 parent unit, 8 bed-space inventories
~~~

## 7. Complete setup and add flow

~~~text
PROPERTY PROFILE
   |
   +-- [1] Select Property Type (ONE)
   |       e.g., Resort
   |
   +-- [2] Enable Accommodation Types (MULTIPLE)
   |       [x] Room   [x] Cottage   [x] Villa
   |       [ ] Apartment [ ] Dormitory [ ] Other
   |
   +-- [3] Configure Location Library (ONE-TIME)
   |       Buildings / Towers / Wings
   |       Floors / Levels, Ground / Basement
   |       Zones / Areas / Blocks / Clusters
   |
   +-- [4] Configure Type/Category Location Strategies
   |       Room -> Building / Floor
   |       Cottage -> Zone / Block
   |       Villa -> Zone / Block
   |
   +-- [5] Configure Numbering Templates
   |       Room 101; Cottage C01; Villa V01
   |
   +-- [6] Review & Save Property Configuration
   |
   v
ACCOMMODATIONS DASHBOARD
   |
   +-- ADD ACCOMMODATION (REUSABLE 5-STAGE WIZARD)
       |
       +-- Stage 1: Type
       +-- Stage 2: Category (existing or create)
       +-- Stage 3: Location & Units
       |           -> Open saved full location/number library
       |           -> Choose multiple blocks/floors when applicable
       |           -> Select / generate unit numbers
       |           -> Dormitory: nested bed-space configuration
       +-- Stage 4: Details, Amenities, Media, Meals & Rates
       |           -> inherit saved category defaults
       |           -> optional YouTube video tour at category level
       +-- Stage 5: Review -> Save Draft / Publish
                     -> validate collisions and booking constraints
                     -> create exact requested inventory atomically
~~~

**Returning-user fast path:** Type → existing Category → saved Location Library → choose Numbers → Review/Create. Do not force all long form stages when applicable data already exists. **First-time setup and the five-stage creation wizard are different flows.**

## 8. Engineering contract (suggested; adapt to current HMS schema)

~~~text
property
  id, property_type_slug, custom_property_type_label?, existing_profile_fields

property_accommodation_config
  property_id, enabled_accommodation_types[], defaults, version, saved_at

location_node
  id, property_id, parent_id?, kind, display_name, code?, sort_order, active

location_strategy
  id, property_id, accommodation_type, category_id?,
  ordered_allowed_node_kinds[], required_node_kinds[], default

numbering_rule
  id, property_id, accommodation_type, location_strategy_id?,
  prefix?, separator?, floor_token?, suffix?, sequence_width,
  sequence_start, reserved_numbers[], manual_override_allowed,
  uniqueness_scope, version

accommodation_category
  id, property_id, accommodation_type, name, location_strategy_id,
  category_defaults, pricing_defaults, media, video_tour_url?

accommodation_unit
  id [immutable], property_id, category_id, leaf_location_node_id?,
  display_code [property-wide unique], operational_status, publish_status

dormitory_bed_space
  id [immutable], property_id, parent_dormitory_unit_id, display_code,
  bed_position, operational_status, publish_status
~~~

This is a **conceptual data contract**, not a migration to run without inspecting the current database.

### 8.1 Rules that must be enforced server-side

- Authorized property scoping for every configuration, location node, category, unit and bed.
- Active node/valid parent-child relationships; cycle prevention on node moves; no orphaning booked units.
- Category-specific location strategies must be valid for that accommodation type.
- Property-wide unique physical-unit display code, enforced by DB uniqueness + transaction.
- Multi-location bulk create creates *exactly* the previewed items or fails safely; rollback on partial errors.
- No overlapping reservation allocations for the same unit or bed; exclusive Dormitory reservation covers all beds.
- Operational/maintenance status is separate from reservation-date availability.
- Changing configuration, deactivating types, removing nodes or changing identifiers with active bookings requires impact review, permission and audit.
- Follow plan capacity limits and RBAC; save partial configuration as draft where supported.
- Optional category-level YouTube video tour can be inherited/overridden by a unit and never blocks publication solely because the link is absent.

## 9. Acceptance tests for developer and QA

| ID | Scenario | Expected result |
|---|---|---|
| PT-01 | Create Resort property | Show relevant type recommendations; all six remain optionally selectable |
| PT-02 | Create Hotel property with Villa | Allowed by deliberate owner selection; not blocked by hard-coded compatibility |
| PT-03 | Select "Suite" as Accommodation Type | Not shown as top-level type; available as Room category/configuration |
| LH-01 | Save Main Building + floors 1 and 2 | Same location nodes reused on every later Room add |
| LH-02 | Add Cottage in Garden Zone | No unrelated Floor field |
| LH-03 | Villa with two internal levels | Creates one Villa inventory record per villa, not units per level |
| LH-04 | Add from Tower A + Tower B | Distinct exact location references and unique display identifiers |
| LH-05 | Configure floor 100 | Handles labels / token widths safely without code collisions |
| NM-01 | Select Room IDs 101, 103, 105 | Creates exactly three nonconsecutive units |
| NM-02 | Try Room 103 again | Rejects collision without overwriting existing unit |
| NM-03 | Existing 101 in Tower A, requested 101 in Tower B | Warns; suggest TA-101 / TB-101 or another unique code |
| NM-04 | Bulk choose units from two floors | One review shows all selected location/code pairs, all created atomically |
| NM-05 | Change prefix after booked inventory exists | Old IDs remain unchanged until explicit controlled migration |
| NM-06 | D101 with eight beds | Creates D101-B01…D101-B08, with proper bed inventory and booking protection |
| UX-01 | Add second Room in existing category | No repeated property profile, amenities, category or building creation |
| UX-02 | Open visual location / number library | Grouped existing hierarchy visible; selectable, searchable, compact |
| UX-03 | Open amenities library | Full searchable multi-select library appears, reuses prior saved items |
| UX-04 | Navigate Back / save Draft | Form state preserved and buttons remain consistent across types |

## 10. Scope, dependencies and handoff

- **P0:** Property Type catalog/preset recommendations, location tree, numbering rules, unique ID enforcement, property configuration.
- **P1:** Room/Cottage/Villa/Apartment multi-unit picker and reusable wizard.
- **P2:** Dormitory bed inventory and overlap-safe booking.
- **P3:** Other/Custom and advanced strategies; migration/compatibility hardening.
- **P4:** 100+ floors, concurrency, impact review, end-to-end HMS and guest booking regressions.
- **Not included:** implementing screens/API/database migrations, changing live booking data or testing production services.

### Existing detailed specifications

- [Master plan](MASTER-PLAN.md)
- [Property configuration](01-PROPERTY-CONFIGURATION.md)
- [Room](02-ROOM-SETUP.md) · [Cottage](03-COTTAGE-SETUP.md) · [Villa](04-VILLA-SETUP.md) · [Apartment](05-APARTMENT-SETUP.md)
- [Dormitory](06-DORMITORY-SETUP.md) · [Other / Custom](07-OTHER-CUSTOM-SETUP.md)
- [Validation & QA](08-VALIDATION-AND-QA.md)
- [Cross-platform change impact](09-ACCOMMODATION-ECOSYSTEM-CHANGE-IMPACT.md)

**Final principle:** **Select Property Type once → enable multiple Accommodation Types → configure existing physical locations once → save numbering rules once → reuse a visual full-library picker when adding exact, validated physical units.**
