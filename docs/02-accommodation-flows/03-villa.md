# Villa — Type-Specific Accommodation Setup

**Accommodation type:** Villa  
**Examples:** Private Pool Villa, Beachfront Villa, Garden Villa, Family Villa.  
**Default sellable unit:** Entire Villa.

## Design principle
Multiple bedrooms, bathrooms or internal levels do **not** automatically mean multiple bookable rooms. The unit is the Villa itself. Most villas belong to a preconfigured Zone / Area; optional Block and special standalone location are allowed.

## Saved property prerequisites
- Property Profile and Accommodation Configuration completed once.
- Villa enabled.
- Existing Villa Zone/Area/Block available if used by property.
- Villa numbering saved, recommended V01, V02...
- Property-wide amenities, meals, tax and policies inherited from existing setup.

## Complete flow
~~~text
Rooms / Accommodations
  -> + New Accommodation -> Villa
  -> Load existing Property Settings (or finish setup then return)
  -> STEP 1: Type [Villa]
  -> STEP 2: Category
      -> Select existing Pool Villa / Garden Villa
      -> OR Create Villa Category
          -> Category Name [Private Pool Villa]
          -> Style [Standard / Family / Luxury / Pool / Custom]
          -> Max Adults [4] / Max Children [2]
          -> Bedroom count [2] / Bathroom count [2]
          -> Bedroom 1 [King x1]
          -> Bedroom 2 [Single x2]
          -> Internal Levels [1] (NOT a property floor)
          -> Optional Private Pool [Yes/No]
          -> Optional Living Area [Yes/No]
          -> Optional Kitchen [Yes/No]
          -> Villa Size [1800 sqft]
          -> Default Location Strategy [Zone -> optional Block]
          -> Photos / amenities / description / default nightly rate
          -> Save Category
  -> STEP 3: Location + Villa IDs
      -> Zone [Beachfront Zone]
      -> Block [Block A] (only if configured)
      -> Suggested IDs [V01,V02,V03,V04,V05]
      -> Choose [V01,V03,V05] / Generate N / Manual
      -> Duplicate, policy and plan validation
  -> STEP 4: Villa Details
      -> Saved default rates and media loaded
      -> Optional unit-level changes and special pool status
      -> Initial operational status
  -> STEP 5: Review & Save
      -> Private Pool Villa / Beachfront Zone / Block A
      -> IDs V01,V03,V05; 3 Whole-Villa Units
      -> Draft or Publish
  -> Dashboard: V01, V03, V05
~~~

## Return-user fast path
~~~text
Dashboard -> Villa -> Private Pool Villa
  -> Beachfront Zone -> Block A (if configured)
  -> V06, V07, V09 -> Review -> Create
~~~

## Villa-specific category config
| Field | Expected behavior |
| --- | --- |
| Category name | Required; "Private Pool Villa" |
| Villa style | Presets but allow custom title |
| Adults/children | Occupancy maximum set once |
| Bedrooms and per-bedroom beds | Multiple distinct bedroom layouts |
| Bathrooms | Optional/recommended count |
| Internal levels | Optional descriptive floor/storey count |
| Private Pool | If Yes, optionally show pool-related amenity options |
| Kitchen / Living Area | Boolean switches; detailed equipment via Amenities Library |
| Villa size | Optional sqft/sqm |
| Zone / Block location | Reuse saved configuration |
| Rate | Price per Villa per night |
| Unit notes and exceptions | Unit-level overrides where needed |

## Villa ID formats
~~~text
Simple (recommended): V01, V02, V03
Zone based: BF-V01, G-V01
Block based: A-V01, B-V01
Custom: configured prefix + padding
~~~
- Never use internal Level 1/2 to generate a separate Villa Unit automatically.
- Nonconsecutive IDs allowed and collision checked.
- Same visible ID cannot be reused in different zones without a disambiguating code.
- Owner can change future template without changing identifiers of live/booked villas.

## Booking / status / restrictions
- Default Entire Villa Booking = **one villa = one sellable unit**.
- Individual bedroom booking is **out of scope V1**. Do not quietly generate bookable Bedroom inventory.
- Category-level private pool doesn't imply all physical Villas have working pools; allow unit-level facility/status exceptions.
- A whole-villa booking blocks that Villa across the booking interval, regardless of bedroom count.
- Maintenance or out-of-service intervals require validation against future reservations.
- Changes to bedroom count, category, display code or location must respect existing bookings and audit metadata.

## Acceptance tests
1. Existing Villa category can be reused without reentering bedrooms or rates.
2. Villa location uses Zone/Block, not compulsory Floor.
3. Selecting V01,V03,V05 creates three whole-unit inventories.
4. Multi-level Villa creates one sellable unit.
5. Duplicate Villa ID rejected.
6. Conflict to future booking shown before location or inventory edits.
7. Same 5-stage Wizard layout/CTA pattern as Room and Cottage.

---

## V1.2 canonical alignment

Whole Villa, Zone/Block, internal levels not separately sellable; Holiday Home preset may map here.

These Stage-2 Category fields (beds, capacity, photos, amenities, meal/base price and optional video) belong to the **nested Category Editor**. Stage 4 **only reviews inherited defaults and optionally changes individual Unit overrides**, never repeats Category-wide entry. Follow [Universal Wizard](00-universal-creation-flow.md) for exact step ownership; [Shared Media/Amenities](07-shared-media-and-amenities.md) for YouTube rules; [Numbering](../01-foundations/03-location-and-numbering.md) for local vs full codes; [Booking](../03-booking-and-inventory/01-availability-and-allocations.md) for reservation safety. No implementation is claimed.
