# 05 — Apartment Setup Specification

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

**Apartment-specific:** The category tour may show a 2BR layout, kitchen and living area. Exact floor/view varies between A101/A103/A105; use an accurate representative-video notice unless the specific unit is guaranteed.
