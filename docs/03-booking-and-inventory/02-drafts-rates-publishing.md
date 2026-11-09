# 02 — Wizard Drafts, Number Ownership, Rate Plans and Publishing

**P0 rules** · [Wizard](../02-accommodation-flows/00-universal-creation-flow.md)

| State | Reserves Unique Full Unit Code? | Bookable? |
| --- | --- | --- |
| Unsaved wizard selection | **No** | No |
| Saved Setup Draft (form data only) | **No**; validate later | No |
| Persisted Physical Unit Draft (actual Unit row) | **Yes** | **No** |
| Published eligible Unit | **Yes** | Only on eligible date/rate |

Use explicit actions **Save Setup Draft**, **Create Units as Draft**, **Publish**; don't call two different behaviors the same Draft. If Staff A selects 101 without saving, Staff B can create 101; Staff A later gets exact conflict on submit. Enforce property-wide unique Full Codes in DB, atomic bulk operations; never overwrite or silently skip.

## Category ownership vs Unit Override

Category owns name, beds/capacity, photos, amenities, YouTube URL, default meal rules, base price and location strategy. Unit owns exact leaf Location, Local Number, Full Code, immutable UUID and only real Unit-specific differences. Inheritance: Property → Core/Preset → Category → explicit Unit override. Category updates require impact review (new-only vs existing), never erase Unit overrides or historical prices.

## Rate model

Property currency/tax policy → Category base rate → date-wise Rate Plan, restrictions (minimum nights, channel, cancellation/meal rules) → supported Unit-level price adjustments → fees/promotions/discounts → quoted/confirmed price snapshot. Category base rate ≠ complete bookable rate; verify current HMS Rate Plan schema before migrations.

## Publish validation

Property access, active enabled Core Type, valid Category, required sleep/occupancy, location strategy, unique Full Unit Code, subscription capacity, Rate Plan readiness, no unsafe inventory/booking conflict. YouTube link optional. Operational and date-wise restrictions can render Published Unit not currently available. Booked Unit/category/number/location edits require audit and impact preview; no destructive changes to linked reservations, invoices or OTA IDs.
