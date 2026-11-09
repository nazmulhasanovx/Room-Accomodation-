# 00 — Universal Accommodation Creation Flow

**Five canonical stages for all six Core Types and all visible presets** · [Master](../../MASTER-PLAN.md)

| Stage | What appears | Rule |
| --- | --- | --- |
| 1. **Select Type** | 14 friendly cards grouped Standard/Specialty/Other | Map chosen preset to 1 of 6 Core Types |
| 2. **Category** | Existing Category or **Create New** nested editor | Category-wide beds, capacity, shared media, amenities, meals, base rate saved once |
| 3. **Locations & Units** | Saved Building/Floor or Zone/Block full number library | Select 101,103,105 or generate/range/manual; Dormitory nested Bed Spaces |
| 4. **Details & Pricing** | Review inherited Category details and rate plan | Optional **Unit-specific** overrides only, don't ask Category defaults again |
| 5. **Review & Save** | Exact chosen Unit Codes, locations, count, conflicts and totals | Save Setup Draft / Create Units as Draft / Publish with clear effect |

Existing Category quick path: Type → Category → Unit Number Library → Review/Create. The five stages stay consistent but entry of inherited values is skipped. Category editor owns categories, not Stage 4. On error, explain exact duplicate or missing node; preserve back navigation. CTA locations stable: Back left, Save Draft/Continue/Publish right.

Wizard drafts without physical Unit rows **do not reserve numbers**; persisted Unit Draft locks Full Unit Code, but not bookable. Publish rechecks DB uniqueness, location, rate and booking rules transactionally. Same modal search and full Amenities Library for all Types.

Recommended location:
Room/Apartment = Building/Tower/Floor; Cottage/Villa = Zone/optional Block; Dormitory = parent D101 with beds B01..B08; Special Stays = saved applicable zone or none, whole-unit unless proven otherwise.

UX: compact laptop desktop-first, responsive, keyboard-friendly; full library expandable/searchable/virtualized for huge data; consistent button/icons/stepper. [Type catalog](../01-foundations/01-property-and-accommodation-types.md) · [Number library](../01-foundations/03-location-and-numbering.md) · [Media](07-shared-media-and-amenities.md) · [Booking](../03-booking-and-inventory/01-availability-and-allocations.md).
