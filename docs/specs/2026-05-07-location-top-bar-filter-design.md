# Location & Sublocation top bar — Edit / Plus / Filter row + filter-active mode

**Date:** 2026-05-07
**Scope:** `mobile-bulk-add-cameras.html`, panels `s0` (Administrative Center 1) and `sSublocation`.
**Figma references:** [filter icon](https://www.figma.com/design/hAm76igqNopdkAq9S66v6Z/?node-id=283-62389), [filter-active top bar](https://www.figma.com/design/hAm76igqNopdkAq9S66v6Z/?node-id=283-62392).

## Problem

The location and sublocation screens currently expose search and filtering as an always-visible inline strip below the top bar (magnifier button + Status / Type / Tags chips). The strip occupies vertical real estate even when not in use, the magnifier is a tertiary affordance buried inside the chips row, and there is no single, scannable place to express "I want to filter this list."

## Decision summary

1. The top-right of `s0` and `sSublocation` houses three plain icon buttons: **Edit · Plus · Filter**.
2. The always-visible search row (`s5-search-row`) and chips row (`s5-filters`) are removed from the normal state of both screens.
3. Tapping the Filter icon enters a **filter-active mode** that morphs the top bar to match the Figma reference: `[back arrow] [full-width search pill]` with the chips row directly below.
4. A small primary-blue dot appears on the Filter icon when any filter is applied (search has text, or any chip is non-default).
5. Filter state persists per-screen across mode toggles and screen switches.

## Detailed design

### Normal state top bar

Top-bar contents from left to right:

- Back arrow — navigates to the previous screen (existing behaviour).
- Title — `Administrative Center 1` on `s0`, sublocation name on `sSublocation`.
- Right-aligned trio: **Edit** (pencil), **Plus** (`+`), **Filter** (funnel).

Icon button styling — uniform across all three:

- Hit target: 40×40 (10px padding).
- Default colour: `text-subtle`. Hover/press: `text-default`.
- No fills. The grey-fill circle currently on the Plus button is **removed**.
- Filter icon glyph matches Figma node `283:62389` (three horizontal lines tapering down).

Below the top bar in normal state: nothing. The body (sublocations + devices sections) sits directly under the top bar.

### Filter badge dot

A 6px primary-blue dot (`--color-background-button-primary-default`) anchored to the top-right corner of the Filter icon (`top: 6px; right: 6px;` inside the 40×40 button container). Visible only when at least one filter is non-default.

A filter is considered non-default when:

- The search input contains any text, **or**
- Any chip (Status, Type, Tags) has a non-default selection — using the same "active" criterion the chips already use today via their existing per-chip sheets.

The dot is recomputed on:
- Search input `input` event.
- Each filter sheet close (Status / Type / Tags).
- `Clear filters` action if/when one exists in those sheets.

### Filter-active mode

Triggered by tapping Filter. Implemented as a class on the active panel (e.g. `.panel.filter-active`).

**Top bar (replaces normal contents):**

- Back arrow — taps clear `.filter-active` and revert to the normal state of the same panel. Does **not** navigate to a parent screen.
- Full-width search input pill — height 40px, radius 16px, background `#F2F4F7`, leading 16px search icon, placeholder text:
  - `s0`: *"Search locations and cameras"* (matches Figma).
  - `sSublocation`: *"Search devices"*.
  - Body font: Satoshi Regular 14/24.

**Chips row (directly below the top bar):**

- Status · Type · Tags chips, same style and behaviour as today (`s5-chip` rounded-pill outlined, 36px height).
- Tapping a chip opens the existing per-chip popover sheet (`openFilterPopover` with `statusFilterSheet` / `typeFilterSheet` / `tagsFilterSheet`). No changes to those sheets.

**Body underneath:** unchanged from normal state. The list of sublocations and devices remains in place. (Filter and search remain visual-only in the prototype.)

**Mode transition:** A short cross-fade (≈120–150 ms) on the top-bar contents, matching the existing `.panel.search-on` swap pattern. No additional animation framework.

### State persistence

Each panel (`s0`, `sSublocation`) maintains its own filter state:

- `filter-active` flag — toggled on Filter tap and on back-in-mode.
- Search input value — persists across mode toggles.
- Chip selections — persist across mode toggles.

Navigating between `s0` and `sSublocation` does **not** leak state from one panel to the other.

### Removal of existing inline patterns

The following markup and JS hooks are removed from both panels (or repurposed):

- `s5-search-row` block (search pill + collapse button) — replaced by the new search pill inside filter-active mode.
- `s5-filters` block (magnifier + chips trio) — replaced by the chips row inside filter-active mode.
- `openDevicesSearch` and `closeDevicesSearch` functions — removed if not referenced elsewhere; otherwise scoped to the panels still using them.
- The `.panel.search-on` class and its CSS rules — removed if no other panel uses them; otherwise left in place.

### Acceptance checklist

- [ ] Top-right of `s0` and `sSublocation` shows Edit, Plus, Filter as three plain icon buttons; Plus has no grey-fill.
- [ ] Normal state shows no search row and no chips row below the top bar; body sits directly under.
- [ ] Tapping Filter swaps the top bar to `[back] [search pill]` and shows the chips row beneath.
- [ ] Tapping back inside filter-active mode reverts the top bar (does not navigate away).
- [ ] Search input value and chip selections persist across mode toggles within the same panel.
- [ ] Filter badge dot appears when search has text or any chip is non-default; clears when all filters are cleared.
- [ ] Per-chip popover sheets open and behave exactly as before.
- [ ] Filter state on `s0` is independent from filter state on `sSublocation`.
- [ ] No regressions to back-navigation, sub-section toggle, success banner, or the Add menu sheet on either screen.

## Out of scope

- Functional filter / search logic (still visual-only).
- The Devices entry screen.
- Any new animations beyond a top-bar cross-fade.
- Changes to the Add menu sheet contents (it still opens via the Plus icon).
