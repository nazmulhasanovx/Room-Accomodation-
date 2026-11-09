# 02 — One-Time Accommodation Configuration

**V1.2** · [Master](../../MASTER-PLAN.md) · [Type catalog](01-property-and-accommodation-types.md)

## Four UI steps

1. **Enable Core Accommodation Types:** multi-select Room, Cottage, Villa, Apartment, Dormitory and Other. Preset options are suggested later in Add Accommodation (not 14 mandatory settings); owner may enable Core Type on choosing a new Special Stay preset.
2. **Configure Location Library:** existing Building/Tower → Floor/Level, Zone/Area → Block/Cluster, optional Wing/Section. Save name, code, parent node, order, unique immutable node IDs. No mandatory Floor for Villa/Cottage; internal villa storeys are descriptive.
3. **Location Strategy & Numbering:** defaults by Core Type/Category, with preview (101/201, C01, V01, A101, D101-B01, Custom). Format tokens, prefixes, reserved numbers, separators, starting sequence and manual overrides. Unique Full Unit Codes, distinct from Local Unit Number.
4. **Review & Save:** validate missing configured nodes, conflicting codes, cycles, property permissions, version; save once and open Accommodation Dashboard.

**Reuse existing Property Profile:** Property Name, Property Type, address, timezone, currency, policies, taxes and contact details should appear prefilled/read-only where helpful, linked to actual Profile editor—not retyped. Optional advanced defaults for Amenities, meals and measurement units are expandable rather than extra required steps.

## One-time example

```text
Resort: Room + Cottage + Villa
  Main Building → Floor 1 → Room 101, 103
  Garden Zone → Block A → Cottage C01, C03
  Beach Zone → Villa V01
```

If no Floor exists and Category requires Floor, open focused Add Floor panel, save and return to original wizard with selections preserved. If only one eligible Building/Zone exists, auto-select. Hide unused Block field.

Changing numbering policy affects future suggestions, not booked Unit Codes; changing used location nodes must show booking impact, respect permissions and audit. Cannot erase active units by disabling their type.
