# Apartment — Type-Specific Accommodation Setup

**Accommodation type:** Apartment  
**Examples:** Studio, One Bedroom, Two Bedroom, Family, Serviced Apartment.  
**V1 inventory:** one sellable unit per entire Apartment.

## Key distinction
Apartment commonly uses **Building / Tower -> Floor**, but individual apartments may include bedrooms, bathrooms, kitchen, living/dining, balcony. Bedrooms are included in the apartment booking, not sold separately in V1.

## Setup prerequisites
- Apartment enabled within Property Configuration.
- Property Profile already completed.
- Towers/Buildings and their Floors configured once.
- Saved numbering pattern: 101 / 201, A101 / A201 or tower-qualified TA-101 / TB-101.
- Existing amenities and pricing policies available for inheritance.

## Full create Apartment flow
~~~text
Rooms / Accommodations
  -> + New Accommodation -> Apartment
  -> Check Property Configuration
     -> If missing Tower/Floor/format, complete focused setup and return
  -> STEP 1: Type [Apartment]
  -> STEP 2: Category
      -> Existing: Studio / One Bedroom / Two Bedroom / Family
      -> OR Create new Apartment Category
          -> Category Name [Two Bedroom Apartment]
          -> Style [Studio/1BR/2BR/3+BR/Custom]
          -> Max Adults [4], Children [2]
          -> Bedroom Count [2]
          -> Bathrooms [2]
          -> Bedroom 1 [King x1]
          -> Bedroom 2 [Single x2]
          -> Kitchen? [Yes/No]
          -> Living Area? [Yes/No]
          -> Dining Area? [Yes/No]
          -> Balcony? [Yes/No]
          -> Apartment Size [1100 sqft]
          -> Media / amenities / meals / default nightly rate
          -> Location strategy [Tower or Building -> Floor]
          -> Save Category
  -> STEP 3: Location & IDs
      -> Building / Tower [Tower A]
      -> Floor [1st Floor]
      -> Auto suggestions A101,A102,A103,A104,A105
      -> Choose A101,A103,A105
      -> Validate duplicate IDs and location rules
  -> STEP 4: Unit Details & Pricing
      -> Pre-fill category defaults
      -> Unit-specific view / kitchen equipment / housekeeping notes
      -> Operational status
  -> STEP 5: Review
      -> Category 2 Bedroom Apartment
      -> Tower A / Floor 1
      -> IDs A101,A103,A105, total 3 apartments
      -> Draft / Publish
  -> Dashboard: A101, A103, A105
~~~

## Returning user
~~~text
Dashboard -> Apartment -> Two Bedroom Apartment
  -> Tower A -> Floor 2
  -> Choose 201,203,205 (or A201,A203,A205 by saved rule)
  -> Review -> Create 3 Apartments
~~~

## Category smart conditions
| Field | Condition |
| --- | --- |
| Category Name | Always required |
| Apartment Style | Studio / 1BR / 2BR / 3+BR / Custom |
| Bedroom count | Studio = 0 **separate** bedrooms; may still contain beds |
| Bed configuration | In Studio, bed(s) in living/sleeping space |
| Bathrooms | Optional/recommended |
| Kitchen | Yes shows kitchen facilities from Amenities library |
| Living / Dining / Balcony | Conditional Yes/No; detailed features in library |
| Apartment area | Optional sqft/sqm |
| Tower / Floor | From saved property location nodes |
| Base rate | Price per whole Apartment/night |
| Custom occupancy | Adults/children limits |

## Numbering format alternatives
~~~text
Floor-based: 101,102,103 | 201,202,203
Prefix: A101,A102,A103 | A201,A202,A203
Tower+Floor: TA-101,TA-102 | TB-101,TB-102
~~~
Apartment codes must remain property-unique. When two towers each have apartment 101, either use TA-101/TB-101 or a different property-wide sequence. For Floor 100+ do not assume three-character numbering. Select gaps such as 101 and 103. Automatically loaded Tower only when there is one valid option; otherwise choose from previously configured options.

## Booking and operations
- Entire Apartment Booked = all included bedrooms/private areas blocked.
- No separate Bedroom Booking in V1.
- Kitchen/amenities can have category defaults and per-unit exceptions.
- Housekeeping, maintenance and service charges may differ by apartment, inherited from policy where possible.
- Any occupied apartment relocation, ID edit or category conversion must trigger review of affected reservations and reports.
- Only published and operationally eligible units become available for booking dates/rate rules.

## Acceptance
1. Studio supports zero separate bedrooms but a valid sleeping arrangement.
2. Tower A Floor 1 and Tower B Floor 1 have unique displayed unit codes.
3. Saved category settings reused for more apartments.
4. A101,A103,A105 creates precisely three whole units.
5. No floor or building creation repeated on every add.
6. Per-bedroom booking is not accidentally exposed.
7. Room and Apartment share numbering infrastructure without forcing identical prefixes.

---

## V1.2 canonical alignment

Whole Apartment, Tower/Floor, Studio may have zero separate bedrooms; Holiday Home may use Apartment if in shared building.

These Stage-2 Category fields (beds, capacity, photos, amenities, meal/base price and optional video) belong to the **nested Category Editor**. Stage 4 **only reviews inherited defaults and optionally changes individual Unit overrides**, never repeats Category-wide entry. Follow [Universal Wizard](00-universal-creation-flow.md) for exact step ownership; [Shared Media/Amenities](07-shared-media-and-amenities.md) for YouTube rules; [Numbering](../01-foundations/03-location-and-numbering.md) for local vs full codes; [Booking](../03-booking-and-inventory/01-availability-and-allocations.md) for reservation safety. No implementation is claimed.
