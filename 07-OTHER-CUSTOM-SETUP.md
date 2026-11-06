# 07 — Other / Custom Accommodation Setup

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

**Custom-specific:** Houseboat category can link to a tour, and individual HB01 can override it. The link is optional regardless of the custom display label or booking mode.
