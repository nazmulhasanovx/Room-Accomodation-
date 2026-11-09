# 03 — Cottage Setup Specification

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

**Cottage-specific:** Garden Cottage category video is inherited by C01/C03/C05; C03 may override with a tour of its lake-facing outdoor area if this differs. Avoid advertising individual views as guaranteed when booking category only.
