# 02 — Room Setup Specification

**Accommodation type:** Room  
**Examples:** Standard Room, Deluxe Twin, Deluxe Double, Executive Suite, Family Room.  
**Default sellable unit:** Entire physical room.

## Primary principle
Rooms generally use a **Floor** location; Building may be present and automatically selected when unique. Room categories are not property types. **Deluxe, Twin, Suite, Single and Double** describe category/configuration, not separate top-level Accommodation Types. Room configuration is stored once on the category.

## First-time prerequisites
The property owner has:
- Completed Property Profile (do not collect its data again).
- Enabled Room under Accommodation Configuration.
- Defined existing buildings and floors, or deliberately selected a floor-less location strategy.
- Saved a floor-based numbering format, typically Floor 1 -> 101–199, Floor 2 -> 201–299.
- Set default rates/amenities/operational policies as applicable.

If the configuration is missing, show a focused "Complete Room Setup" route with return-to-wizard afterward. Do not require owner to configure unrelated Villa or Cottage fields.

## Full first-time Room creation diagram
~~~text
Rooms / Accommodations
  -> + New Accommodation
  -> Select "Room"
  -> Load saved Property Configuration
      -> missing? configure Building/Floor + Numbering Rules -> return
  -> STEP 1: Type [Room selected]
  -> STEP 2: Category
      -> Existing Room Category?
         YES -> Select Deluxe Twin; inherit saved configuration
         NO  -> Create Category
             -> Category Name [Deluxe Twin]
             -> Room Style / Configuration [Twin / Double / Single / Suite / Custom]
             -> Max Adults [2]
             -> Max Children [1]
             -> Bed configuration
                 -> Bed Type [Single]
                 -> Bed Count [2]
             -> Room Size [350 sqft]
             -> Optional bathroom/accessibility/connecting door details
             -> Default Category Photos / Description / Amenities
             -> Base Rate + applicable charge/rate defaults
             -> Location Strategy [Building -> Floor] (from saved defaults)
             -> Save Category
  -> STEP 3: Location + Physical Room Units
      -> Building [Main Building] (auto if sole building)
      -> Floor [1st Floor] (choose configured node)
      -> Saved Format [{floorCode}{sequence:02}]
      -> Mode [Select numbers / Generate N / Range / Manual]
      -> Suggestions [101,102,103,104,105...]
      -> Operator selects [101,103,105]
      -> Check collisions, floor validity, plan limits
  -> STEP 4: Details + Pricing
      -> Load existing category images/amenities/rates
      -> Add optional unit-specific notes/photos/amenity exceptions
      -> Initial operational status [Active / Out of Service / Inactive]
      -> Confirm effective pricing / sellable settings
  -> STEP 5: Review
      -> Type Room
      -> Category Deluxe Twin
      -> Building Main Building / Floor 1
      -> Room IDs 101,103,105
      -> Physical rooms to create: 3
      -> Save Draft OR Create & Publish
  -> Inventory Dashboard
      -> 101 | Deluxe Twin
      -> 103 | Deluxe Twin
      -> 105 | Deluxe Twin
~~~

## Returning operator — shortest flow
~~~text
Dashboard -> + New Accommodation -> Room
  -> Existing Category: Deluxe Twin
  -> Existing Floor: 2nd Floor (Building auto-filled if unique)
  -> Select IDs: 201, 203, 205
  -> Review: 3 new rooms -> Create
~~~

## Category-level fields
| Field | Requirement | Behavior |
| --- | --- | --- |
| Category name | Required | Unique enough to distinguish within the property; e.g. Deluxe Twin |
| Room style | Optional/conditional | Standard, Deluxe, Single, Twin, Double, Suite, Custom |
| Adults / Children maximum | Required by selling policy | Respect fire/safety and property policy |
| Bed arrangements | Required | Type(s) and quantity; can have more than one bed type |
| Size + unit | Optional | sqft/sqm from property preference |
| Bathrooms | Optional | Attached/shared; count when relevant |
| Photos / amenities / description | Inherited / configurable | Category defaults reused for future units |
| Base nightly price | Required for publication if selling rate plan needs it | Currency from Property Profile; rate-plan integration may override |
| Allowed location strategy | Inherited | Recommended Building -> Floor; category override possible before units exist |

## Floor / number engine
~~~text
Configured Building: Main
  Floor 1 -> suggest 101, 102, 103 ... 199
  Floor 2 -> suggest 201, 202, 203 ... 299
  Floor 3 -> suggest 301, 302, 303 ... 399
  ...
  Floor 100 -> derive from owner-configured explicit rule
Manual selection permitted: 101, 103, 105
~~~
- Floors 1/2 do **not** force exactly 99 units; ranges are configurable recommendations.
- Ground, basement, mezzanine and high floors require explicit saved floor codes; do not assume arithmetic concatenation without validating collisions.
- Offer nonconsecutive selection and editable recommended values.
- Reserved/occupied numbers are shown as unavailable or error without mutating existing units.
- If multiple buildings would otherwise produce duplicate "101", choose unique display codes such as MAIN-101 and ANNEX-101, or enforce a property-wide unique number reservation.

## Booking and operational rules
- Each physical room is one whole-unit bookable inventory record in V1.
- Suite may contain multiple spaces but remains one bookable unit.
- Booked / checked-in / held = dated reservation statuses, separate from maintenance and active/inactive status.
- Changing Room type/category/number/location with upcoming bookings requires booking impact check and confirmation or restriction.
- Do not delete booked or audited rooms without a safe lifecycle/archive approach.
- Images, room category amenities and rate default edits should not automatically overwrite specific unit overrides without preview.

## UX / components
Re-use: Type Selector, Category Picker/Editor, Location Selector, Floor Selector, Number Grid / Bulk Range, Amenities Library, Rate Form, Review Panel, Conflict Dialog, Unsaved Changes Confirmation. Keep buttons and step positions consistent with every accommodation type.

## Acceptance cases
1. Owner configures building + floors once; subsequent Room additions only pick existing floors.
2. Select first-floor IDs 101 and 103 without creating 102.
3. Duplicate 101 rejected.
4. One category reused for floors 1 and 2 without reentering beds/capacity.
5. Existing reservations protect identifiers and location edits.
6. 100th floor uses saved format and never conflicts with lower floors.
7. Draft can be incomplete; Publish requires fully validated configuration.

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

**Room-specific example:** Deluxe Twin category gets one YouTube Tour URL. When Room 101, 103 and 105 are created, none asks again for URL. A guest viewing Deluxe Twin sees its representative video and a disclaimer when the exact room is not allocated. Room 103 may override with its own verified walkthrough.
