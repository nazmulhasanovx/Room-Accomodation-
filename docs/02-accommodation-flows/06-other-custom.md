# Other / Custom — Type-Specific Accommodation Setup

**Accommodation type:** Other / Custom  
**Examples (optional):** Houseboat, special lodge, nonstandard rental unit, glamping structure.  
**Principle:** Provide a flexible escape hatch without inventing a separate full UI for every uncommon type in Bangladesh.

## Configuration prerequisites
- Enable Other / Custom in Property Configuration.
- Set allowed custom naming/numbering and optional location strategy.
- User can choose existing Zone/Building/Block or explicitly allow no formal location.
- Reuse property tax/amenities/media defaults; avoid repeating Property Profile.

## Five-stage creation
~~~text
Rooms / Accommodations
  -> + New Accommodation -> Other / Custom
  -> Check saved Property Configuration
  -> STEP 1: Type [Other]
  -> STEP 2: Category
      -> Select Existing Custom Category
      -> OR Create
          -> Display Type Label [Houseboat]
          -> Category Name [Lake Houseboat]
          -> Booking Mode [Entire Unit / Per Bed] (only if bed engine enabled)
          -> Max Adults/Children [configurable]
          -> Bedrooms [optional]
          -> Bathrooms [optional]
          -> Beds [optional, conditional]
          -> Size [optional]
          -> Feature Flags [Kitchen / Pool / Living Area as applicable]
          -> Photos, Amenities, Meals, Rate Defaults
          -> Location Strategy [Zone / Building / custom path / None]
          -> Save Category
  -> STEP 3: Location + Units
      -> Select saved location if configured (e.g., Lake Zone)
      -> Generate IDs via saved format [HB01, HB02, HB03]
      -> Select nonconsecutive IDs / manual / bulk
      -> If Per Bed, explicitly create bed spaces using Dormitory bed engine
  -> STEP 4: Details + Pricing
      -> Auto-load saved category data and apply limited overrides
  -> STEP 5: Review
      -> Display Type Houseboat
      -> Category Lake Houseboat
      -> Selected Units HB01, HB03
      -> Draft / Publish
~~~

## Booking mode restrictions
- Entire Unit is default: one physical unit equals one sellable unit.
- Per Bed requires the same tested parent/bed allocations and double-book protection as Dormitory; **do not implement arbitrary per-bed sale without the bed engine**.
- Custom fields are limited to existing standard/optional attributes in V1. An open-ended custom database schema builder is deferred.
- A custom display label (e.g., Houseboat) remains classified internally as "Other" with a stable type key and category-specific label; changing a label should not break reporting.

## Location and numbering
Recommended custom rule: configurable prefix + sequence (HB01, HB02, HB03). Optional zone-aware prefix L-HB01. Operator can manually choose a unique unit name/code. Property-wide uniqueness applies; do not let user bypass it by switching to Custom. Avoid requiring Hotel Floors for unusual accommodation.

## Category fields
| Field | Notes |
| --- | --- |
| Custom display label | Required e.g., Houseboat |
| Category name | Required e.g., Lake Houseboat |
| Booking mode | Entire Unit default, Per Bed only with supported engine |
| Occupancy | Required for publication |
| Bed configuration | Conditional |
| Bedrooms/bathrooms/area | Optional |
| Amenities/media/meals | Reuse shared library |
| Pricing | Per unit/night or per bed/night based on mode |
| Location strategy | Chosen from configured strategies or None |
| Unit code | Property-unique |

## Returning user fast path
~~~text
Dashboard -> Other -> Existing Lake Houseboat Category
  -> Lake Zone (auto if unique)
  -> Select HB04,HB06
  -> Review -> Create 2 Entire Unit inventories
~~~

## Acceptance
1. Creating Houseboat does not require floor.
2. Existing Custom Category can be reused.
3. Duplicate HB01 fails.
4. Custom Display Type Label appears in UI without changing immutable internal type key.
5. Per-bed custom mode uses tested Dormitory booking constraints or is unavailable.
6. Unconfigured optional attributes are not shown as redundant compulsory fields.

---

## V1.2 canonical alignment

Whole Unit default, stable OTHER Core Type; Treehouse/Tent/Houseboat/standalone Capsule may be saved as named presets. Per-bed gated to tested Dormitory engine.

These Stage-2 Category fields (beds, capacity, photos, amenities, meal/base price and optional video) belong to the **nested Category Editor**. Stage 4 **only reviews inherited defaults and optionally changes individual Unit overrides**, never repeats Category-wide entry. Follow [Universal Wizard](00-universal-creation-flow.md) for exact step ownership; [Shared Media/Amenities](07-shared-media-and-amenities.md) for YouTube rules; [Numbering](../01-foundations/03-location-and-numbering.md) for local vs full codes; [Booking](../03-booking-and-inventory/01-availability-and-allocations.md) for reservation safety. No implementation is claimed.
