# 01 — Property and Accommodation Type Library

**Product taxonomy · V1.2** · [Master](../../MASTER-PLAN.md)

## Property Types — 10 single-select options

| Property Type | Initial suggested core accommodation choices (never hard restricted) |
| --- | --- |
| Hotel | Room, Apartment, Dormitory |
| Resort | Room, Cottage, Villa, Apartment, Dormitory, Other |
| Guest House | Room, Apartment, Dormitory |
| Motel | Room, Cottage, Apartment |
| Hostel | Dormitory, Room, Apartment |
| Homestay | Room, Apartment, Cottage, Villa |
| Serviced Apartment | Apartment, Room, Villa |
| Vacation Rental | Villa, Cottage, Apartment, Room, Other |
| Lodge / Retreat | Room, Cottage, Villa, Dormitory, Other |
| Other / Custom Property | Any Core Type |

Store stable Property Type slug and optional custom label. A property changing its label must not destroy enabled accommodation inventory.

## Six stable backend Core Accommodation Types

ROOM (whole physical room); COTTAGE (whole cottage); VILLA (whole villa); APARTMENT (whole apartment); DORMITORY (parent room + bed spaces); OTHER (whole unit default, per-bed only via tested Dormitory engine). These are **engine identities** and determine inventory/booking semantics. Suite, Deluxe, Twin, Double, Studio, 1BR, Penthouse are Category/configuration, not new Core Types.

## Expanded UI — 14 visible Accommodation cards

| No. | Visible Stay Choice | Default internal Core Type | Suggested numbering |
| ---: | --- | --- | --- |
| 01 | **Room** | ROOM | 101, 201 |
| 02 | **Cottage** | COTTAGE | C01 |
| 03 | **Villa** | VILLA | V01 |
| 04 | **Apartment** | APARTMENT | A101 |
| 05 | **Dormitory** | DORMITORY | D101, D101-B01 |
| 06 | **Bungalow** | COTTAGE | B01 |
| 07 | **Cabin** | COTTAGE | CB01 |
| 08 | **Chalet** | COTTAGE | CH01 |
| 09 | **Treehouse** | OTHER | TH01 |
| 10 | **Tent / Glamping** | OTHER | GL01 |
| 11 | **Houseboat** | OTHER | HB01 |
| 12 | **Capsule / Pod** | OTHER by default | CP01 |
| 13 | **Holiday Home** | VILLA by default | H01 |
| 14 | **Other / Custom** | OTHER | Custom |

Extra cards **are presets, not seven new backend booking engines**. Store core_type + preset_slug/friendly label; allow Property Admin to adjust suggested mapping before publishing a Unit if its actual building/booking structure differs. Example: Holiday Home inside a shared tower may use APARTMENT; a shared capsule bed inside a dormitory should use DORMITORY bed inventory rather than untested per-bed OTHER.

## Visual picker

```text
ACCOMMODATION TYPE
├─ Standard Stays: Room | Cottage | Villa | Apartment | Dormitory
├─ Specialty Stays: Bungalow | Cabin | Chalet | Treehouse
│                    Tent/Glamping | Houseboat | Capsule/Pod | Holiday Home
└─ Other / Custom
```

Icon-based responsive equal-size cards, short descriptions, search, expand/collapse Specialty Stays. A property can enable multiple Core Types. Do not display irrelevant preset cards as mandatory one-time setup steps.

## Guards

Category may inherit preset defaults and then change fields; special preset can be disabled without deleting existing bookings. Full Unit Code property-unique across all types; changing Core Type for existing Unit with reservations requires impact review. Availability, pricing and reporting use Core Type plus explicit preset metadata, not display text as primary key.
