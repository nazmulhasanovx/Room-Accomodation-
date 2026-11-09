# 01 — One-Time Property & Accommodation Configuration

**Critical product requirement:** A hotel/resort owner describes property organization **before** adding units. The software must reuse these settings in every later Accommodation creation.

## Goals
- Avoid repeating existing Property Profile information.
- Configure actual location structure and enabled types once.
- Associate each accommodation type/category with a suitable location hierarchy, **not always a floor**.
- Save smart numbering rules; offer automatic suggestions without disallowing intentionally missing numbers.
- Allow revisions in Settings with booking-impact safety.

## Setup entry conditions
~~~text
Property created / onboarded
  -> Accommodation configuration completed?
      NO -> Guided Setup (mandatory before first Publish)
      YES -> Accommodation Dashboard
  -> Owner may visit Rooms > Configuration to update settings
~~~

## Step 1 — Property context (load, not ask again)
Show property name, property type (Hotel/Resort/Guest House/Serviced Apartment etc.), timezone Asia/Dhaka as appropriate, default BDT currency, and address from Property Profile. Link to Edit Property Profile for corrections. Do not duplicate storage of previously collected profile data.

## Step 2 — Enable Accommodation Types
Fixed six entry choices: Room, Cottage, Villa, Apartment, Dormitory, Other/Custom. Turn on only relevant types; re-enable later without database migration. A disabled type with active bookings cannot have its existing inventory erased.

## Step 3 — Create physical location tree
The property owner configures reusable nodes (examples only):
~~~text
Azure Bay Grand Resort
  |
  +-- Main Building [building]
  |     +-- Ground Floor [floor, code=G]
  |     +-- Floor 1 [floor, code=1]
  |     +-- Floor 2 [floor, code=2]
  |     +-- Floor 3 [floor, code=3]
  |
  +-- Tower A [building/tower]
  |     +-- Floor 1 [floor]
  |     +-- Floor 2 [floor]
  |
  +-- Garden Zone [zone]
  |     +-- Block A [block]
  |     +-- Block B [block]
  |
  +-- Beachfront Zone [zone]
  +-- Lake Zone [zone]
  +-- Hostel Block [building]
        +-- Floor 1 [floor]
~~~
Each node has a type, label, optional short code (G, BF, A, TA etc.), parent, sequence and active flag. Siblings should not share an indistinguishable code where numbering depends on it. Support 100+ floors/levels and changing configurations without losing existing inventory.

## Step 4 — Save type-to-location strategies
Allow the owner to accept suggested defaults, change available hierarchy per type, or override an individual category:
| Accommodation | Suggested strategy | Required when configured |
| --- | --- | --- |
| Room | Building -> Floor | Select Floor; Building auto when unique |
| Cottage | Zone -> optional Block | Zone if property uses Zones |
| Villa | Zone -> optional Block | Zone if used |
| Apartment | Tower/Building -> Floor | Select Floor |
| Dormitory | Building -> Floor | Select Floor |
| Other | configurable path or no location | as chosen by owner |

**Do not show nonexistent choices.** If a property has one building, auto-select it; if zero floors are configured and category is floor-based, take user back to a focused location-setup action. For one zone, auto-select. Block field hidden if no blocks exist. A single cottage may contain internal floors, but they do not define the location strategy or unit numbering.

### Category-wise location decision
~~~text
Select Accommodation Type
  -> Load saved default location strategy
  -> Category uses default?
      YES -> Show existing node choices (only ambiguous ones)
      NO  -> On Category Create/Edit: choose another valid strategy
  -> Store category strategy
  -> Next unit additions reuse category strategy automatically
~~~
The owner must not select the entire location taxonomy on every add. Category-specific overrides are saved, not one-off repetitive prompts.

## Step 5 — Numbering rules
The rule is saved separately per type or strategy, optionally per location:
| Type | Default suggestion | Example |
| --- | --- | --- |
| Room | Floor code + 2-digit index | Floor 1 -> 101, 102; Floor 2 -> 201 |
| Cottage | C + 2-digit sequence | C01, C02 |
| Villa | V + 2-digit sequence | V01, V02 |
| Apartment | Floor code + sequence or A prefix | 101, A101, TA-101 |
| Dormitory room | D + floor + sequence | D101, D102 |
| Dormitory bed | Room code + -B + index | D101-B01, D101-B02 |
| Other | owner prefix + sequence or manual | HB01, T01 |

Settings to expose: sequence start, padding, separator, prefix, hierarchy token, scope (normally property), reserved numbers, default generation count, manually editable code permitted, skip-already-taken switch. Use format preview based on real configured location, not generic placeholders.

**Floor numbering principle:** for 1st floor suggest 101–199, 2nd 201–299, 3rd 301–399. This is a recommendation, not hardcoded database behavior. If floor codes exceed two/three digits (including 100+ floors) or capacity >99 units on a floor, allow wider padding, separators or custom rules. Format validator must reject collisions before Publish.

**Property-wide uniqueness:** reserve exact display code uniquely across active physical units, even if location differs; or automatically include building short code to disambiguate (TA-101 / TB-101). Bed-space codes must uniquely map to an actual parent room and avoid clashes.

## Step 6 — Shared defaults (optional, not repetitive)
- Default currency and unit of measure (sqft/sqm).
- Default check-in/out and base operational policies from Profile.
- Amenities library defaults and media policy.
- Tax/charges/meal settings shared from existing HMS configuration.
- Initial unit operational status (Active, Inactive, Out of Service).
- Category rate/occupancy defaults; actual values belong to categories.

## Step 7 — Review and save
Check orphan nodes, duplicate sibling codes, missing floor hierarchy for floor-based strategies, overlapping numbering previews and incompatible existing rules. Save a configuration version with actor/timestamp and send user to Accommodation Dashboard.

## Adding an accommodation after setup
~~~text
New Accommodation
  -> Room
  -> Existing Category "Deluxe Twin"
  -> Main Building (auto if unique)
  -> Floor 1
  -> Select 101, 103, 105
  -> Preview + validate + create

New Accommodation
  -> Villa
  -> Existing Category "Pool Villa"
  -> Beachfront Zone
  -> Block A (only if configured)
  -> Select V01, V03
  -> Preview + validate + create
~~~

## Safe configuration edits
- Reordering/renaming labels must not change stable node IDs.
- Changing a display prefix does not silently rename already booked units.
- Deleting a used floor, zone or category requires reassignment/no conflicting reservations.
- Parent changes must prevent loops; moving Building/Tower must validate all descendants.
- Show a preview of affected units/rate plans/reservations and request an appropriate confirmation.
- Version changes and unit override decisions audited.

## Developer acceptance
- A previously configured category can be added without reentering property name, building definition, room bed defaults, amenities or global settings.
- Changing Room category location strategy requires explicit transition handling once units exist.
- Zone/Block Cottage and Villa additions do not require a floor.
- Nonconsecutive numbers are accepted; duplicates are rejected.
- 100+ levels and nonnumeric floor codes work with configured formats.
- Mobile/narrow UI displays one stage at a time with stable actions and preserved state.

## Media Defaults for All Categories
YouTube Video Tour URL is **category-specific**, so it is entered **once while creating/editing the category**, not during first-time Property Configuration and never demanded for every new unit. The Property Configuration stage may show that the optional video feature is enabled globally, but must not ask for a YouTube link there. Individual unit overrides and guests' category-vs-exact-unit context must be respected. See MASTER-PLAN.md → YouTube Video Tour.
