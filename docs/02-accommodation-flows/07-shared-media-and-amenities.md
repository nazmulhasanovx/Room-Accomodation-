# 07 — Shared YouTube Video, Gallery and Amenities Library

**One canonical shared requirement for all six engines / 14 visible Stay options.** · [Wizard](00-universal-creation-flow.md)

## Category Media

Category Create/Edit → Media & Gallery holds optional YouTube URL, optional title, Photos, Preview and Remove. Saved once per Category and inherited by every Unit. Unit override mode = **inherit / replace / hide** (not just blank URL). Example: Room 101 inherit Category; Room 103 verified exact video; Room 105 hide; guests browsing Category with unassigned exact Unit see "Actual room layout/view may vary".

Accept safe YouTube watch/short links only from allowed hosts via URL parser; canonical ID, reject unknown domains, arbitrary embeds/HTML and unsafe iframes. Lazy thumbnail, click-to-play, **no autoplay**, privacy-enhanced player where supported, meaningful fallback to photos and Open on YouTube for blocked/unavailable/geo-restricted videos. A valid link need not be publicly playable; unlisted ≠ private. Use only authorized media. **Video optional: never block publication.** OTA does not automatically inherit video support.

## Full Amenities Library

Open existing searchable/filterable categorized Amenities catalog with visible full list, keyboard controls, multi-select, selected-count, and Save. Reuse property/category selections without re-creating amenities; per-Unit changes only for actual differences, with explicit precedence. Examples Wi-Fi/AC/Kitchen/Private Pool/Parking/Breakfast; category fields depend on type.

## QA

Valid/invalid/unavailable URL, missing optional URL, representative warning, inherit/replace/hide, lazy mobile player, no autoplay, accurate unit-specific tour, amenities deduplication and multi-select across every core/preset.
