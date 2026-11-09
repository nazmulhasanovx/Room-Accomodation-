# 02 — Decision Log: Locked, Proposed and Open

| Rule | Status |
| --- | --- |
| Property Type ≠ Accommodation Type, one-time Config, 5-step Wizard | LOCKED |
| Six Core Types: Room, Cottage, Villa, Apartment, Dormitory, Other | LOCKED |
| 14 user-facing Stay options mapped into six Core Types | PROPOSED V1.2 catalog |
| Four visible setup screens, advanced settings expandable | PROPOSED simplified UX |
| Local Door Number vs property-unique Full Unit Code | PROPOSED explicit model |
| Unsaved Draft doesn't lock; persisted Unit Draft locks but cannot be booked | PROPOSED operational policy |
| Category-only confirmed reservation consumes availability | REQUIRED; exact safe allocation algorithm OPEN |
| Dormitory bed-space billing against subscription tiers | OPEN |
| Bed-wise inventory through OTA/channel partners | OPEN / partner-dependent |
| Actual current rate-plan SQL, legacy Room IDs, backwards API versions | OPEN; inspect production |
| Custom Pod/Glamping classification overrides after booking | OPEN / migration impact |
| YouTube optional category media and full Amenities Library | LOCKED |

**Approval order:** PM signs off visible tiles and mappings → Engineering audits existing HMS schema and booking consistency → Finance approves Dormitory limits → UX signs off real full-library interaction → QA runs staged tests and migration rollbacks. Documents are product requirements, not proof of implementation.
