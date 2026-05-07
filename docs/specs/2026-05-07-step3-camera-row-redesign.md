# Step 3 (Selection) — promote connection + registration to the visible row

**Date:** 2026-05-07
**Scope:** `mobile-bulk-add-cameras.html`, panel `s2` (Add cameras flow, step 3 — Selection / scan results).

## Problem

Today, each scan-result row shows only `[dot] Brand` on its secondary line. The two facts that actually drive the user's decision — connection quality (Good / Reasonable / Limited) and registration status (Unregistered / Registered / Unknown) — are buried inside the expanded area. Installers have to expand every row to see what they're looking at.

The expanded area itself uses a key-value layout that doesn't match the chip-row pattern used elsewhere on similar screens (Core > Cameras), creating visual inconsistency.

## Decision summary

1. The visible secondary line gets the connection text and a registration label, alongside the existing brand: `[dot] Reasonable · [cam-icon] Unregistered · Axis`.
2. The expanded area drops the connection KV and the registration title, keeping only the ping diagnostics — restyled as a Core > Cameras-style titled chip block.
3. `Unknown` devices show no camera icon (they may not be cameras); `Unregistered` and `Registered` devices show a 14px outline camera icon before the label.

## Detailed design

### Visible row (`.cam-row-fig`)

| Slot | Content | Notes |
|--|--|--|
| Line 1 | `[checkbox] IP MAC` | Unchanged |
| Line 2 | `[dot] [connection-label] · [reg-label] · [brand]` | Revised |
| Trailing | `[chev]` | Unchanged |

**Line 2 segment formats:**

- Connection: existing `.cam-conn-dot.${c.connection}` paired with `connectionLabel(c.connection)` → `Good` / `Reasonable` / `Limited`.
- Registration: depends on `c.status`:
  - `unregistered` → `[cam-icon] Unregistered`
  - `registered` → `[cam-icon] Registered`
  - `unknown` → `Unknown` (no icon)
- Brand: `c.brand` plain text.

Bullet separators (`·`) between the three segments.

**Camera icon:** 14×14 outline SVG, `currentColor` so it inherits `text-subtle`. Reuses the existing camera path already in the prototype (e.g., the rect-plus-trapezoid camera glyph used in filter sheets).

**Truncation behavior under horizontal pressure:**

- The line-2 wrapper is a flex row with `align-items: center; min-width: 0`.
- Dot, connection label, registration label, separators: `flex: 0 0 auto` (full size, never truncated).
- Brand span: `flex: 1 1 auto; min-width: 0;` with `overflow: hidden; text-overflow: ellipsis; white-space: nowrap;` so brand truncates first.

### Expanded area (`.cam-details`)

Single titled chip block in the Core > Cameras style (`cc-stream-block` + `cc-chip-row` + `cc-stat-chip`):

- Block title: `Ping (ms)` (the unit lives in the title so chips stay compact).
- Chip row: four `cc-stat-chip` elements:
  - `[Min · 12]`
  - `[Max · 48]`
  - `[Avg · 23]`
  - `[Std · 5]`

Padding and spacing follow the existing `cc-details` / `cc-stream-block` rules verbatim so the visual treatment matches Core > Cameras.

What is removed from the expanded area:

- The status title (`statusTitle(c.status)`) — relocated to the visible line.
- The Connection KV row — relocated to the visible line.
- The four Min / Max / Avg / Std KV rows — replaced by the chip row above.

What is **not** added:

- No "Edit camera" link (no edit semantics at the discovery step).
- No second block.

### Function changes

- `statusTitle(s)` is repurposed to return the short label only:
  - `registered` → `Registered`
  - `unknown` → `Unknown`
  - default (`unregistered`) → `Unregistered`
- A new helper `statusIconHTML(s)` returns the inline 14×14 camera SVG for `registered` / `unregistered`, and an empty string for `unknown`.
- `connectionLabel(c)` is unchanged.

### CSS changes

- New rule for the secondary line wrapping behaviour: `.cam-row-secondary { display: flex; align-items: center; gap: 6px; min-width: 0; }`. Existing `.cam-row-secondary` already exists; tighten its layout to enforce truncation order.
- New rule on the brand element inside line 2: `.cam-row-secondary .cam-brand { flex: 1 1 auto; min-width: 0; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }`.
- New rule on the registration label segment: `.cam-row-secondary .cam-reg { display: inline-flex; align-items: center; gap: 4px; flex: 0 0 auto; }`.
- The expanded area reuses `cc-details`, `cc-stream-block`, `cc-stream-title`, `cc-chip-row`, `cc-stat-chip`. No new styles needed unless the s2 panel context requires a small padding override (verify in the build).

### Acceptance checklist

- [ ] Each row's secondary line shows the colored connection dot, the connection label, the registration label (with camera icon for registered/unregistered), and the brand, separated by bullet middle dots.
- [ ] `Unknown` rows show the registration label without the camera icon.
- [ ] Long brand names truncate with ellipsis; connection and registration labels never truncate.
- [ ] Expanded area shows a single titled block "Ping (ms)" with four `cc-stat-chip` chips for Min / Max / Avg / Std.
- [ ] No status title, no Connection KV, no Min/Max/Avg/Std KV rows remain in the expanded area.
- [ ] Visual padding inside the expanded area matches Core > Cameras's expanded card.
- [ ] The chevron expand/collapse toggle still works; the row's checkbox tap behaviour is unchanged.
- [ ] No regressions to filter chips, sort, master checkbox, or the Selection step's selection counter.

## Out of scope

- Step 4 (Authentication) and Step 5 (Configure) row layouts.
- New diagnostic data (signal strength, RTSP probe results) — would become a second block when added.
- Changes to `c.connection` or `c.status` data shape.
- Functional filter logic.
