# 03 — Location Hierarchy, Numbering and Full Unit Library

**V1.2** · [Master](../../MASTER-PLAN.md)

## Configurable location kinds

Building / Tower (Main, Tower A), Block / Wing (Block B, East Wing), Floor / Level (Ground, B1, 1st, 100th), Zone / Area (Garden, Lake, Beach), Section / Cluster. Store property ID, immutable node ID, parent ID, kind, code, name, order and active state; validate tree paths and cycles. There is **no mandatory global Building→Block→Floor→Zone chain**.

| Accommodation | Suggested location | Example |
| --- | --- | --- |
| Room | Building → optional Wing → Floor | 101, 103 |
| Cottage / Bungalow / Cabin / Chalet | Zone → optional Block | C01, CB01 |
| Villa / Holiday Home | Zone → optional Block | V01, H01 |
| Apartment | Tower/Building → Floor | TA-101 |
| Dormitory | Building → Floor | D101 → D101-B01 |
| Treehouse / Glamping / Houseboat / Pod | Zone/Area or custom/none | TH01/HB01 |

## Six numbering templates

Floor-based (101,201); Sequential (001,002); Prefix-based (C01,V01); Block-based (A-101,B-101); Zone-based (G-C01,L-C01); Custom/Manual (HB01,SKY-501). Floor 1 generally 101–199, Floor 2 201–299, Floor 100 10001–10099 **only with configured two-digit sequence**. For >99 units/floor or complex levels, use explicit collision-safe e.g. F100-001 or B1-01.

### Three identifiers

- **Local Door/Unit Number:** 101; allowed to recur across different Buildings if property physically uses that label.
- **Full Unit Code:** TA-101, TB-101; unique property-wide across types and locations.
- **Internal Unit UUID:** immutable DB identity used in reservations and history, never based on current display code.

## Full visual number library (must preserve old easy UX)

```text
New Room | Deluxe Twin | Select existing location / numbers
 Main Building
  Floor 1  [x]101 [ ]102 [x]103 [ ]104 [x]105
  Floor 2  [x]201 [ ]202 [x]203
 Annex Building
  Floor 1  [ ]A-101 [ ]A-102

Selected: 101,103,105,201,203 (5)
[Single] [Generate N] [Range] [Manual]  → Review
```

Select across saved Floors/Blocks/Zones in one operation; every picked number includes exact location node. Modes: single, generate N, range, nonconsecutive visual choice, manual list. Show selected count, existing/invalid/reserved/available-to-create statuses, search and group collapse. "Available to create" does **not** mean available to book on dates.

For 100+ Floors/10,000 units use lazy load/pagination/virtualization, not 100K chips at once. Unsaved selection doesn't reserve code; persisted Unit Draft owns unique Full Code. Database uniqueness plus atomic bulk insert prevents concurrent duplicate IDs. No silent partial creates/overwrites or retroactive renumbering.
