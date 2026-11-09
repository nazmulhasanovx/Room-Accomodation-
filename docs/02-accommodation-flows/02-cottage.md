# Cottage — Type-Specific Accommodation Setup

**Accommodation type:** Cottage  
**Examples:** Garden Cottage, Family Cottage, Lake Cottage, Hill Cottage.  
**Default sellable unit:** Entire cottage, even with multiple bedrooms.

## Key distinction
Cottage location is commonly **Zone / Area**, with optional **Block**. Do not require Hotel Floor. A cottage's internal level or bedrooms are **subspaces**, not a property-wide location hierarchy or independently sellable units in V1.

## Configuration prerequisites
- Property Profile and Accommodation Setup already completed.
- Cottage enabled as a type.
- Zone/Area nodes configured once, e.g. Garden Zone, Lake Zone, Hill Zone; optional Block A/B.
- Saved cottage ID rule e.g. C01, C02, C03, or Zone-aware G-C01.
- Saved category-default rate policy/amenities from existing library.

## Full new Cottage diagram
~~~text
Rooms / Accommodations
  -> + New Accommodation -> Cottage
  -> Check saved property configuration
      -> missing Cottage location/numbering? focus setup -> return
  -> STEP 1: Type [Cottage]
  -> STEP 2: Category
      -> Select existing Garden Cottage / Family Cottage
      -> OR Create new Cottage Category
          -> Category Name [Garden Cottage]
          -> Cottage Style [Standard / Family / Custom]
          -> Max Adults [2], Max Children [2]
          -> Bedrooms [1], Bathrooms [1]
          -> Bedroom/Beds [King x1] (allow multiple arrangements)
          -> Internal Levels [1] (optional; NOT Hotel Floor)
          -> Size [500 sqft]
          -> Saved Zone/Area location strategy
          -> Photos / amenities / description
          -> Default price per cottage/night
          -> Save Category
  -> STEP 3: Location + Cottage Units
      -> Zone [Garden Zone]
      -> Block [Block A] (only if configured)
      -> Numbering from setup: C01,C02,C03,C04,C05...
      -> Select [C01,C03,C05]
      -> Validate exact IDs and available inventory quota
  -> STEP 4: Details + Pricing
      -> Inherit category photos/amenities/meals/base rate
      -> Optional per-cottage view, notes and special features
      -> Initial operational state
  -> STEP 5: Review
      -> Cottage / Garden Cottage / Garden Zone / Block A
      -> Selected units C01,C03,C05 (3 cottages)
      -> Save Draft or Create & Publish
  -> Dashboard inventory
      -> C01 Garden Cottage
      -> C03 Garden Cottage
      -> C05 Garden Cottage
~~~

## Returning user
~~~text
Dashboard -> Cottage -> Existing Garden Cottage
  -> Garden Zone -> Block A (if relevant)
  -> select C06,C07,C09 -> Review -> Create 3 Cottages
~~~

## Cottage category fields
| Field | Need | Notes |
| --- | --- | --- |
| Category Name | Required | e.g., Garden Cottage |
| Cottage style | Optional | Standard/Family |
| Maximum occupants | Required | Adults/children policy |
| Bedroom count and bed arrangements | Required when bedrooms exist | Could be single-space studio-like cottage |
| Bathroom count/type | Optional/recommended | attached/shared |
| Internal levels | Optional | Not a property floor for unit IDs |
| Cottage area | Optional | sqft/sqm |
| Zone/location strategy | From Property Configuration | Zone usually recommended |
| Images/amenities/meals | Inherited | Category defaults |
| Base rate | For publish/sellable rates | Entire cottage/night |

## Location and number strategy
- Simple default: C01, C02, C03, ...
- Zone-based optional: G-C01, G-C02; L-C01, L-C02.
- Block-based optional: A-C01, B-C01.
- The **full displayed ID** must not collide with another physical unit in the same property.
- If two Zones both use "C01", either sequence globally or include zone code. Never silently accept ambiguous duplicate IDs.
- Selectable gaps are valid: C01 and C03 with no C02.
- No Block nodes -> Block field hidden; only one Zone -> auto-select.
- Zone changes for a booked cottage require impact assessment and audit.

## Booking policies
- Whole cottage = one bookable unit. Bedrooms are informational only.
- Category may specify shared garden/near lake/private yard as features; model these as amenities/attributes, not separate Room types.
- Future split-room booking is outside V1 unless a later explicit inventory model supports it.
- Maintenance and operational state are separate from date-based booking availability.
- Unit-level variations (special lake view/size/temporary status) are overrides without re-creating category.

## Developer/QA acceptance
1. Cottage creation never forces Floor.
2. First-time property setup persists Zones/Blocks and ID template.
3. Existing Garden Cottage category inherits beds, occupancy, amenities, rate.
4. C01,C03,C05 create exactly 3 cottages.
5. Duplicate C03 blocked; reserved numbers visible.
6. Multi-bedroom cottage still counts as one unit.
7. Safe edits respect booking/maintenance conflicts.

---

## V1.2 canonical alignment

Whole Cottage, Zone/optional Block. Bungalow, Cabin, Chalet are optional Cottage-engine presets.

These Stage-2 Category fields (beds, capacity, photos, amenities, meal/base price and optional video) belong to the **nested Category Editor**. Stage 4 **only reviews inherited defaults and optionally changes individual Unit overrides**, never repeats Category-wide entry. Follow [Universal Wizard](00-universal-creation-flow.md) for exact step ownership; [Shared Media/Amenities](07-shared-media-and-amenities.md) for YouTube rules; [Numbering](../01-foundations/03-location-and-numbering.md) for local vs full codes; [Booking](../03-booking-and-inventory/01-availability-and-allocations.md) for reservation safety. No implementation is claimed.
