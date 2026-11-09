# 04 — Villa Setup Specification

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

**Villa-specific:** A Pool Villa tour may show bedrooms, pool, and living space; V03 can have its own walkthrough. Entire Villa remains a single bookable unit. Do not create video inputs for each internal bedroom in V1.
