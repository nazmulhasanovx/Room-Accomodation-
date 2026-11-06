# BinGi HMS — Full Accommodation Management Master Plan

**Specification:** V1.0 · 06 November 2026  
**Owner/product:** NEONECY · BinGi Hotel Management Technology  
**Target:** Bangladesh-first hotels, resorts, guest houses, serviced apartments and dormitory-style stays  
**Status:** Product blueprint for developer, designer and QA handoff; proposed behavior, not implemented code.

## 1. What we are solving
Existing Room / Category / Unit workspaces must evolve into an easy accommodation inventory engine. The owner should **describe the property once**: buildings, floors, wings, zones, blocks, preferred numbering, capacities and relevant defaults. The recurring task of adding a Room, Cottage, Villa or Apartment must not ask for redundant property-level inputs again.

Users first select one of exactly six entry types:
1. **Room** — Standard, Deluxe, Twin, Suite etc. (as category/configuration).
2. **Cottage** — Garden, Family, Lake Cottage etc.
3. **Villa** — Private, Pool, Garden Villa etc.
4. **Apartment** — Studio, 1BR, 2BR, Serviced Apartment etc.
5. **Dormitory** — Shared accommodation sold primarily by bed.
6. **Other / Custom** — limited configurable type for exceptions.

**Property type ≠ accommodation type.** A Resort property can contain Room, Cottage, Villa and Apartment simultaneously.

## 2. Terms and data boundaries
- **Property**: hotel/resort organization and settings boundary.
- **Property configuration**: persistent, versioned building/floor/zone hierarchy, enabled accommodation types, default policies and numbering templates.
- **Location node**: physical Building, Tower, Floor, Level, Zone, Area, Block, Wing or custom node; a parent-child tree with type and display order.
- **Accommodation type**: one of the six top-level choices.
- **Category**: commercial template, e.g., Deluxe Twin, Garden Cottage or Two-Bedroom Apartment; contains standardized beds, capacity, images, amenities, default pricing and applicable location strategy.
- **Physical unit**: one real room/cottage/villa/apartment/dormitory room with its own stable identifier and status.
- **Bookable inventory**: whole unit for most types; individual bed-space for a dormitory in bed-wise mode.
- **Bed-space**: individually identifiable, potentially bookable bed within a dormitory room. Beds inside a Villa/Apartment are descriptive, not separately bookable in V1.
- **Published** means ready for sale subject to operational status, date, restrictions, availability and channel sync—not automatically available on every date.

## 3. Master text diagram
~~~text
BINGI HMS
  |
  +-- PROPERTY PROFILE (existing; do not ask twice)
  |      |
  |      +-- Property type / contact / property details
  |
  +-- ACCOMMODATION CONFIGURATION (first-time; editable later)
  |      |
  |      +-- Select enabled types [Room, Cottage, Villa, Apartment, Dormitory, Other]
  |      +-- Define location tree [Buildings/Towers -> Floors; Zones -> Blocks]
  |      +-- Define applicable location path PER accommodation type/category
  |      +-- Set default formats [101, 201; C01; V01; A101; D101-B01]
  |      +-- Set default currency, amenities and operational policies
  |      +-- Validate / Save version
  |
  +-- ACCOMMODATIONS DASHBOARD
         |
         +-- Categories | Units | Locations | Configuration
         |
         +-- + New Accommodation
                |
                +-- 1. TYPE [one of six]
                +-- 2. CATEGORY
                |      +-- Select existing [defaults inherited]
                |      +-- Create new [type-specific fields]
                |
                +-- 3. LOCATION + UNITS
                |      +-- Load saved category location path
                |      +-- Select existing location (only where ambiguous)
                |      +-- Auto-generate / choose / bulk-add unique unit identifiers
                |      +-- Dormitory: generate bed-space IDs and layout
                |
                +-- 4. DETAILS / RATES
                |      +-- Inherit photos, amenities, capacity, base rate, services
                |      +-- Edit essential unit-level overrides if needed
                |
                +-- 5. REVIEW / DRAFT / PUBLISH
                       +-- Validate conflicts, duplicates, capacity, plan limits
                       +-- Atomic creation -> visible units -> booking inventory
~~~

## 4. First-time configuration
Detailed spec: **01-PROPERTY-CONFIGURATION.md**.

**Step A — Reuse Property Profile.** Show name/type and any already-held facts read-only or editable through Profile; never request again inside the room-add wizard.

**Step B — Enable Types.** Present six predefined cards; only enabled choices show in quick-add. Other/Custom is opt-in.

**Step C — Define Organization.** Saved location tree may contain:
- **Hotel rooms:** Main Building -> Floor 1, Floor 2...; also floor-less small properties.
- **Resort cottages/villas:** Garden Zone -> Block A (optional), Beach Zone, Lake Zone.
- **Apartments:** Tower A -> Floor 1; Tower B -> Floor 1.
- **Dormitories:** Hostel Building -> Floor 2.
Location types are reusable and not artificially mutually exclusive. A property can have 100+ levels; never cap the data model at a UI sample.

**Step D — Location rule per type/category.** Recommended template:
| Type | Suggested hierarchy | Fallback |
| --- | --- | --- |
| Room | Building -> Floor | Floor only / zone-based / no location |
| Cottage | Zone / Area -> Block (optional) | Standalone Area |
| Villa | Zone / Area -> Block (optional) | Villa Wing / standalone |
| Apartment | Building / Tower -> Floor | Standalone Building or Zone |
| Dormitory | Building -> Floor | Floor-only |
| Other | Configurable location path | No location |

A category inherits the default location strategy but may choose another configured strategy before its first units are created. Staff select existing nodes when necessary, **not create the whole building hierarchy each time**.

**Step E — Numbering policies.** Recommended, all configurable:
- Room floor 1: 101–199; floor 2: 201–299; choose nonconsecutive 101, 103.
- Cottage: C01, C02...; optionally G-C01 or A-C01.
- Villa: V01, V02...; optionally BF-V01 or A-V01.
- Apartment: 101 or A101; optionally TA-101 / TB-101.
- Dormitory: D101 room; D101-B01 through D101-B08 bed spaces.
- Other: prefix + sequence or free label with uniqueness check.

Important: format rules specify **prefix, separators, floor code, number padding, sequence start, scope and allowed overrides**; visible IDs remain unique within the property. Floor 100+ may require a longer format; never naively concatenate floor 100 with 01 if colliding with a different floor's format. Existing codes are not silently renumbered if an owner edits templates.

## 5. Consistent creation wizard
All types use same header, steps, footer actions and confirmation patterns:
1. **Type**: 6 cards; choose one; first-time configured settings load.
2. **Category**: select previously saved or create one using type-specific conditional fields; edit parent template rather than rewriting on every new unit.
3. **Location & Units**: choose saved location; preview suggestions; individual, automatic or bulk input. Dormitory bed-space setup appears as a nested section here.
4. **Details & Pricing**: shared categories of images, descriptions, amenities, meals, rates, restrictions, status; defaults copied/inherited deliberately.
5. **Review**: preview exact unit list and effective configuration; Save Draft or Publish.

**Returning-user fast path:** Type -> Existing Category -> Location (only if multiple) -> Unit IDs -> Review/Create. Do not force repeating five long pages when everything is configured.

**Navigation:** Next/Back and Save Draft positions consistent across every type. Leaving an unsaved wizard warns about unsaved changes. A stepper should preserve entries when navigating backward. User sees immediate duplicate/reservation conflicts.

## 6. Dynamic fields and inheritance
**Shared fields** (category template, unless indicated otherwise): name, description, occupancy (adults/children), beds, size/unit, bathrooms, accessible features (optional), photos, amenities, room facilities, meals, default nightly rate, tax/charge configuration by property settings, check-in/out policies, rate-plan restrictions and availability state.

**Room**: room configuration (single/double/twin/suite); bed types/count; building/floor.
**Cottage**: cottage style, bedrooms, bathrooms, zone/area, optional block and internal levels.
**Villa**: villa style, bedroom-by-bedroom beds, bathrooms, optional private pool/kitchen/living, internal levels, zone/block.
**Apartment**: studio/1BR/2BR/custom, room-by-room bed setups, bathrooms, kitchen/living/dining/balcony, tower/floor.
**Dormitory**: mixed/male-only/female-only occupancy policy if legally/operationally applicable, bed count, bed style (single/bunk), upper/lower bed positions, shared bathrooms, per-bed nightly pricing, parent dormitory room.
**Other**: friendly name, whole-unit or per-bed policy and limited applicable fields; nonstandard custom fields deferred to later phase.

**Precedence:** Property defaults -> type defaults -> category defaults -> unit overrides. Effective values are explicit; edits to shared category defaults must show whether they apply to **new units only** or also **existing units** after impact review. No implicit destructive mass updates.

## 7. Number selection and generation
~~~text
Category: Deluxe Twin
  -> Saved strategy: Building -> Floor
  -> Building: Main Building (auto if sole option)
  -> Floor: 1 (selected)
  -> Saved format: {floorCode}{sequence:02}, start 01
  -> Suggestions: 101, 102, 103, 104, 105
  -> Operator picks: 101, 103, 105
  -> Validation: no other units share these IDs
  -> Create exactly 3 units
~~~
Offer **Single**, **Generate N**, **Range**, **Manual list** and **Selectable preview**. Nonconsecutive values are valid. Preview warns on conflicts, never silently replaces existing units. Optional "skip taken numbers" is explicit and displays the final generated list. Choose unit-ID uniqueness policy with property-wide identifier enforced by default, even when display names are similar across buildings. Database UUID remains independent of editable display code.

**Floor formats:** configurable labels for Ground/Mezzanine/Basement and floor aliases, padding and upper-floor ranges; no hardcoded limit of 99 floors or 99 units. Format becomes a stored policy, not a formula pasted into form logic.

## 8. Booking and inventory model
- Room, Cottage, Villa and Apartment: one physical unit = one bookable whole unit in V1.
- Villa/Apartment bedrooms are descriptive subspaces; not separately sellable in V1.
- Dormitory bed-wise mode: one physical dormitory room contains N bed-space inventory items. For an entire-room exclusive booking, all beds are jointly blocked for overlapping dates. For mixed bookings, a bed cannot be reserved twice on overlapping intervals.
- Inventory availability is **per date range**, not a permanent property of a unit.
- Operational status (active / out of service / maintenance / inactive) is distinct from occupancy/reservation status (available / held / booked / checked-in).
- Maintenance conflicts: notify, do not mark a booked unit unavailable for a date range without an explicit conflict resolution flow.
- Each unit links one category, one leaf location (or null when permitted), one numbering rule snapshot and audit metadata.

## 9. Suggested persistence model (not an enforced database migration)
~~~text
properties
  id, name, property_type, profile_data
property_accommodation_config
  property_id, enabled_types, default_options, version, completed_at
location_nodes
  id, property_id, parent_id, kind, name, code, order, active
location_strategies
  id, property_id, type, category_id?, allowed_node_path, default
numbering_rules
  id, property_id, accommodation_type, location_strategy_id,
  prefix, separator, floor_token, padding, sequence_start, uniqueness_scope, version
accommodation_categories
  id, property_id, type, name, config_fields, occupancy, beds,
  amenities, pricing_defaults, media, location_strategy_id, active
accommodation_units
  id, property_id, category_id, location_node_id?,
  display_code, internal_status, publish_status, overrides, created_at
bed_spaces
  id, property_id, dormitory_unit_id, display_code, position,
  operational_status, publish_status, overrides
reservation_allocations
  reservation_id, sellable_kind [unit|bed], sellable_id,
  check_in, check_out, allocation_status
audit_events
  actor_id, property_id, object_kind, object_id, action, before, after, timestamp
~~~
Database must enforce property scoping, unique unit display code, unique bed code within a room/property policy, valid parent-child relationships, and transaction-safe creation. Do not infer schema from the examples; adapt to existing HMS entities/migrations.

## 10. Screen architecture
**One reusable page set** rather than unrelated screens:
- Accommodation Dashboard (categories, units, counts, status and filters).
- First-time Property Configuration (a multi-step setup experience; settings editable later).
- Category List / Category Create-Edit (conditional form per type).
- New Accommodation Wizard (5 stages with conditional sections).
- Location / Number Preview and Bulk Editor (inside wizard).
- Unit Details / Status / Restriction / Maintenance.
- Dormitory Bed Map and Bed Details (type-specific).
- Review / Confirmation / Unsaved-Changes / Conflict dialogs.
- Amenities Library picker and Pricing/Meal selectors reusable from current system.

Use premium desktop responsive behavior, compact controls, consistent side panels/dialog layouts, clear step progress, unchanged button positions, progressive disclosure and keyboard-friendly fields. Do not force oversized forms on small screens.

## 11. Validation and permissions
**Before Publish:** category valid; selected location path permitted; exact unit identifiers unique; occupancy and beds valid; positive/zero price according to policy; no over-plan inventory; images if required by publication policy; room/bed inventory creation atomic; no overlapping inventory references. Draft can be incomplete.

**While Editing:** no unintended data loss; warn on affected future/active bookings; prevent invalid deletion or structural relocation; maintain immutable IDs and audit history.

**Access:** owner/property admin configures property, permitted managers create categories/units, front-desk roles see/manage inventory according to permissions; support custom RBAC. Cross-property access disallowed.

## 12. Phased delivery
**P0 Foundations:** property structure, location strategies, numbering engine, category + unit boundaries, migrations, permissions.
**P1 Standard Accommodation:** Room, Cottage, Villa, Apartment; wizard, presets, bulk add, draft/publish, details.
**P2 Dormitory:** per-bed inventory, bunk mapping, exclusive-room protection and booking allocation logic.
**P3 Custom:** constrained Other flow, advanced rates/channel-sync consistency and polish.
**P4 Hardening:** edge cases (100+ floors, concurrent creates, existing bookings, backfills, inaccessible locations), analytics, audit and regressions.

## 13. Core acceptance outcomes
1. Setup saved one time; adding subsequent units never asks to recreate a building or repeat global details.
2. A category selects a **location kind/hierarchy**; Villa can use Zone/Block while Room uses Floor.
3. Floor 1 can generate 101, 103, 105 nonconsecutively, and a duplicate 103 cannot be created.
4. Room category Deluxe Twin can be reused on multiple floors with unchanged saved category defaults.
5. Cottage IDs C01, C03 and Villa IDs V01, V03 can be selected independently; codes never collide within their configured property scope.
6. Tower A and Tower B can both have floor 1 without mistaken cross-tower allocation; display IDs remain unique via policy.
7. Dormitory D101 with 8 bed spaces has 8 individually sellable inventories, and an exclusive booking blocks all overlapping bed sales.
8. Edits to occupied units are safely validated and auditable.
9. Five wizard stages and primary action placement remain consistent.
10. Every mode supports responsive UI, useful error messages, draft save and predictable navigation.

For per-type detailed field-level flows and test scenarios, follow the links in README.md.

## YouTube Video Tour — Accommodation Category / Unit (V1 addition, 06 Nov 2026)

### Product requirement
A guest should be able to **watch an authentic video walkthrough before booking**. The hotel admin can paste a **YouTube video URL** when creating or editing an Accommodation Category. It is optional: a missing or unavailable video must never prevent creating or publishing an otherwise valid category. The same reusable media feature is available for Room, Cottage, Villa, Apartment, Dormitory and Other / Custom.

### Where the field appears
- **Category Create / Edit → Media & Gallery**: optional field **YouTube Video Tour URL** with placeholder \`https://www.youtube.com/watch?v=...\`, **Preview**, **Remove**, optional Video Title (default: “Watch Video Tour”), and clear saved/invalid state.
- **Unit Details → Media**: optional **Unit-Specific YouTube Video Tour** override when an individual unit has a genuinely different view/layout. If empty, category tour is inherited. Do not request the link for every unit.
- **Guest Room/Accommodation Detail → Gallery**: show a distinct **Watch Video Tour** thumbnail/tile/button next to photos, then play inside a dismissible modal or open YouTube. The booking CTA remains usable. Responsive on desktop and mobile.

### Priority and accuracy
\`\`\`text
Property Accommodation Category (e.g., Deluxe Twin)
  -> Saved category YouTube URL
  -> Added units 101, 103, 105 inherit category tour
  -> Unit 103 has its own video? YES -> show 103 video when unit 103 is actually represented
                                NO  -> show category video
  -> Guest browses a category without exact assigned unit?
       -> Show representative category tour + “Actual room layout/view may vary”
  -> Invalid/missing/unavailable video?
       -> Photos-only gallery; no broken player or blank box
\`\`\`
**Never imply a category video depicts the exact assigned room unless that room has been verified/allocated and its video matches.** A property may use the same category for units with different views.

### Developer field model (adapt to actual backend)
\`\`\`text
accommodation_categories
  video_tour_url?: string       // canonical validated YouTube link
  video_tour_video_id?: string  // extracted for embed/thumbnail
  video_tour_title?: string
  video_tour_enabled?: boolean

accommodation_units
  video_tour_url_override?: string | null
  video_tour_enabled_override?: boolean | null

effective_tour = active valid unit override ?? active valid category tour ?? none
\`\`\`
Store the URL and derived video ID as metadata; do not store iframe/embed HTML from the operator. Unit override semantics should distinguish **inherit / replace / hide**, rather than treat an intentionally hidden video as merely missing.

### Validation / safety / performance
1. Accept supported **YouTube video links** (\`youtube.com/watch?v=...\`, \`youtu.be/...\`; \`youtube.com/shorts/...\` if supported by the embed component). Extract canonical video ID using an actual URL parser and a strict allowed-host check. Reject arbitrary domains, unsafe embed HTML and nonvideo YouTube URLs.
2. Render a **lazy-loaded thumbnail** and only load the YouTube player after a guest clicks; **no autoplay**. Prefer privacy-enhanced \`youtube-nocookie.com\` embed where available, with an appropriate video-provider/privacy notice.
3. Warn if the URL format is invalid. A valid-looking link is not proof the video is public, playable, available in region or still online; handle playback errors with **Open on YouTube** and photos fallback.
4. Escape titles, restrict video ID format and use safe iframe permissions/sandbox/referrer configuration appropriate to the player. Never concatenate untrusted raw URLs into HTML.
5. Video may be unlisted but accessible by link; never claim it is private to a single guest. Ask owner to use only videos they are authorized to share.
6. Tour is **optional** and should not block draft, publish, booking, room creation, channel listing or existing photo display.
7. The room type / category tour must be used consistently on the BinGi guest-facing website/booking experience; each channel's own support for video can be handled separately.

### Guest-facing wireflow
\`\`\`text
Guest explores Accommodation Category
  -> Room / Villa / Cottage / Apartment detail
  -> Gallery: Photos | [Watch Video Tour ▶] (only if link available)
  -> Click -> modal player -> close
  -> Review features, price and booking terms -> Continue Booking
\`\`\`

**Implementation placement:** Show the YouTube URL input in STEP 4 — Details & Pricing / Media for first-time category creation, or on Category Edit → Media; returning operators selecting existing categories do not need to enter it again.
