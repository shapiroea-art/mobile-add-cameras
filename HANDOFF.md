# Mobile Add-Cameras — Dev Handoff

This is a single-file HTML/CSS/JS prototype (`mobile-bulk-add-cameras.html`) for the Lumana mobile add-cameras flow. It is intended as a visual + interaction reference; production code should reuse the published Lumana mobile component library, not lift this CSS.

## How to read this prototype

Foundations and the highest-frequency components have been renamed to mirror Figma's published names. The rest is documented below as a mapping table — the prototype class stays for stability, but the Figma component / variant is named explicitly so you can swap component-by-component.

- **Tokens (color, shadow, typography):** verbatim from Figma. The `:root` block in the file is the spec.
- **Component classes:** mirror Figma component names where the rename was unambiguous. Otherwise see §3.
- **Icons:** all `<svg>` elements are placeholder renders — replace with the icon component from Figma `04-Iconography` (file `lXI4UA3B9OqKzU8nrlWdX8`, frame `3:23`).
- **Out of scope (bespoke prototype chrome):** the device frame, flow-map left rail, status-bar mock, mock keyboard, home-indicator, and demo state attributes (`data-typing`, `data-addr-mode`, …) — do not port these.

---

## 1. Foundations

### 1.1 Color tokens — Figma `01-Foundations` 939:15

CSS variable names mirror Figma verbatim, including the typo `color-text-defsult`. Light values only — dark mode is a future pass.

The full Figma color set has 262 tokens; only the ones the prototype actually consumes are defined in `:root`. Add more as needed.

| Prototype CSS var | Figma name | Light value |
|---|---|---|
| `--color-text-bold` | `color-text-bold` | `#101828` |
| `--color-text-defsult` | `color-text-defsult` *(typo verbatim)* | `#344054` |
| `--color-text-subtle` | `color-text-subtle` | `#667085` |
| `--color-text-subtlest` | `color-text-subtlest` | `#98A2B3` |
| `--color-text-inverse` | `color-text-inverse` | `#FFFFFF` |
| `--color-text-success-default` | `color-text-success-default` | `#22C55E` |
| `--color-elevation-surface-default` | `color-elevation-surface-default` | `#FFFFFF` |
| `--color-elevation-surface-subtle` | `color-elevation-surface-subtle` | `#F9FAFB` |
| `--color-background-accent-gray200-default` | `color-background-accent-gray200-default` | `#F2F4F7` |
| `--color-background-button-primary-default` | `color-background-button-primary-default` | `#4367F6` |
| `--color-background-button-primary-hover` | `color-background-button-primary-hover` | `#748CED` |
| `--color-background-accent-primary-subtlest` | `color-background-accent-primary-subtlest` | `#F4F6FE` |
| `--color-background-accent-emerald-subtle` | `color-background-accent-emerald-subtle` | `#D1FAE5` |
| `--color-background-accent-amber-default` | `color-background-accent-amber-default` | `#F59E0B` |
| `--color-background-accent-amber-subtle` | `color-background-accent-amber-subtle` | `#FEF3C7` |
| `--color-background-accent-red-default` | `color-background-accent-red-default` | `#EF4444` |
| `--color-background-accent-red-subtle` | `color-background-accent-red-subtle` | `#FEE2E2` |
| `--color-background-information-default` | `color-background-information-default` | `#EFF6FF` |
| `--color-border-default` | `color-border-default` | `#EAECF0` |
| `--color-border-input-default` | `color-border-input-default` | `#D0D5DD` |
| `--color-border-button-primary-default` | `color-border-button-primary-default` | `#3456E0` |
| `--color-border-info` | `color-border-info` | `#3B82F6` |

**Quirks worth knowing for the full Figma set (apply as more tokens come into use):**
- `color-text-defsult` is misspelled in Figma (preserve verbatim).
- `indygo` is the Figma spelling for indigo across all `*-accent-indygo*` tokens.
- `color-border-button-tertiary-grey-{focused,pressed}` use `grey` while every other "gray" token uses `gray`.
- Two duplicate-named tokens in the source: `color-background-accent-purple-default` and `color-background-accent-cyan-default` each appear twice — pick the row whose hex matches the family.
- `*-opacity50` tokens are not literal 50% opacity — they're solid hexes, with opacity applied at consumption.

### 1.2 Typography — Figma `01-Foundations` 3:28

Single family: **Satoshi**, weights 400 / 500. Six sizes used in this prototype (xs–2xl), each in Regular and Medium.

| Figma name | Size / Line | CSS var (size / line) | Utility classes |
|---|---|---|---|
| `Text-xs` | 10 / 16 | `--type-xs-size` / `--type-xs-line` | `.text-xs-r`, `.text-xs-m` |
| `Text-sm` | 12 / 20 | `--type-sm-size` / `--type-sm-line` | `.text-sm-r`, `.text-sm-m` |
| `Text-base` | 14 / 24 | `--type-base-size` / `--type-base-line` | `.text-base-r`, `.text-base-m` |
| `Text-lg` | 16 / 24 | `--type-lg-size` / `--type-lg-line` | `.text-lg-r`, `.text-lg-m` |
| `Text-xl` | 20 / 28 | `--type-xl-size` / `--type-xl-line` | `.text-xl-r`, `.text-xl-m` |
| `Text-2xl` | 24 / 32 | `--type-2xl-size` / `--type-2xl-line` | `.text-2xl-r`, `.text-2xl-m` |

Existing inline `font-size`/`line-height` declarations in the prototype already match this scale — no class was applied to HTML elements (per the "class-renames only" rule). When porting, replace the inline declarations with the matching utility class.

Larger sizes (Text-3xl through Text-7xl, 32/40 → 64/64) are also published in Figma but not used here.

### 1.3 Shadows — Figma `01-Foundations` 3:30

| Figma name | Effect | CSS var |
|---|---|---|
| `Shadow/xs` | `0 1px 2px 0 #1018280D` | `--shadow-xs` |
| `Shadow/sm` | `0 1px 2px 0 #1018280F, 0 1px 3px 0 #1018281A` | `--shadow-sm` |
| `Shadow/md` | `0 2px 4px -2px #0F172A0F, 0 4px 8px -2px #0F172A1A` | `--shadow-md` |
| `Shadow/lg` | `0 4px 6px -2px #10182808, 0 12px 16px -4px #10182814` | `--shadow-lg` |
| `Shadow/xl` | `0 8px 8px -4px #10182808, 0 20px 24px -4px #10182814` | `--shadow-xl` |
| `Shadow/2xl` | `0 24px 48px -12px #1018282E` | `--shadow-2xl` |

Inset stripes (e.g. info banners, success banners), focus rings, status-dot rings, and the bespoke phone-frame chrome stay hardcoded — they're not part of the shadow scale.

### 1.4 Icons — Figma `04-Iconography` 3:23

Every `<svg>` in the prototype is a render placeholder. Recurring icons used:

| Visual | Figma icon name |
|---|---|
| Back chevron in top bar | `chevron-left` |
| Right chevron in list rows | `chevron-right` |
| Up / down chevron | `chevron-up` / `chevron-down` |
| Magnifying glass (search input prefix, top bar) | `search` |
| × in input clear / close buttons | `close` / `x` |
| Plus | `plus` |
| Three dots (top bar overflow) | `ellipsis-vertical` (or `more-horizontal`) |
| Trash | `trash` |
| Pencil / edit | `edit` |
| Check (status, success bar) | `check` |
| Pin icon (map) | `map-pin` |
| Speaker / mic | `mic` |
| Camera | `camera` |
| Wi-Fi / signal bars | `signal-strength` |
| Tag | `tag` |
| Filter | `filter` |
| Info circle | `info` |
| Warning triangle | `alert-triangle` |
| Network / globe | `globe` |
| Question mark | `help-circle` |
| Folder / location pin | `map-pin-area` (folder for sublocations) |

Confirm exact names against the published icon set when you swap them.

---

## 2. Components renamed in-place (you can grep for these)

These prototype classes were renamed to mirror Figma component names. When porting, replace each class with the corresponding Figma component instance.

### Buttons — Figma `03-Mobile-Components` 3:32

Figma variant axes: `Hierarchy ∈ {Primary, Secondary, Default, Tertiary Color, Tertiary Gray, Tertiary Caution, Video}` × `Size ∈ {Medium - 40px, Large - 48px, Large - 56px}` × `State ∈ {Default, Focused, Pressed, Disabled}`.

| Prototype class | Figma component variant |
|---|---|
| `.button` (base) | Button (base) |
| `.button-primary` | Button / `Hierarchy=Primary` |
| `.button-default` | Button / `Hierarchy=Default` (white bg, gray border, gray text) |
| `.button-caution` | **Bespoke** — Figma's `Tertiary Caution` is text-only red; prototype uses solid-red destructive. **Gap to flag with design system team.** |
| `.button-tertiary-color` | Button / `Hierarchy=Tertiary Color` (text-only blue) |
| `.button-icon` | `Button (Icon)` family |
| `.button-md` (modifier) | adds `Size=Medium - 40px` constraint |
| `.button-full` (modifier) | layout helper — flex: 1, prototype-only |
| `*-button` specialized (`link-button`, `retry-button`, `edit-cam-button`, `cd-test-button`, `cn-test-button`, `cs-copy-button`, `cd-delete-button`, `core-restart-button`, `cc-primary-button`, `card-delete`, `chev-button`, `expand-all-button`, `filter-button`, `ut-button`) | Each is a context-specific composition. Map to the closest Hierarchy. Most are `Tertiary Color`, `Tertiary Gray`, or `Default`. |

### Inputs — Figma `03-Mobile-Components` 3:42

Figma component family: `Input` (composed of `_Input Top`, `_Input Field`, `_Input Bottom`, `_Prefix`, `_Suffix`).

`_Input Field` axes: `Type ∈ {Default, IP, Phone number}` × `State ∈ {Default, Typing, Value, Disabled, Read only, Error}`.

| Prototype class | Figma component |
|---|---|
| `.input-wrap` | Input (composed: top + field + optional bottom) |
| `.input-label` | `_Input Top` (label row) |
| `.input-field` | `_Input Field` (the bordered text row itself) |
| `.input-clear` | Clear `Button (Icon)` placed inside `_Suffix` |
| `.input-wrap-narrow` | Input with `width: 100px` constraint (layout) |
| `.input-label-row` | `_Input Top` with adjacent helper link (e.g. "Search address instead") |
| `.textarea` | `Textarea input` (separate component) |

### Top bar — Figma `03-Mobile-Components` 1394:35217

Figma variants: `Type ∈ {Template, Cameras, With tabs, With Segmented controls, Search bar}`.

| Prototype class | Figma variant |
|---|---|
| `.top-bar` | Top bar / `Type=Template` |
| `.cam-top-bar` | Top bar / `Type=Cameras` |
| `.s5-top-bar` | Top bar / `Type=Template` (header for the AC1 list views) |

`.tb-left`, `.tb-title`, `.tb-right` are slot wrappers inside the top bar — not separately published as components.

### Bottom action bar — Figma `03-Mobile-Components` 2930:32429

Figma variants: One action / Two actions.

| Prototype class | Figma component |
|---|---|
| `.bottom-action-bar` | Bottom action bar |

### Stepper — Figma `03-Mobile-Components` 1305:35277

Figma variants: `Variant ∈ {33%, 50%, 66%, 100%}` (progress fill).

| Prototype class | Figma component |
|---|---|
| `.stepper` | Stepper |
| `.stepper .sp-label` | label slot ("N of M – StepName") |
| `.stepper .sp-bar` | track |
| `.stepper .sp-fill` | fill |

---

## 3. Components NOT renamed — mapping table

These are still using prototype-local class names. The mapping below tells you what Figma component each one ports to. Substring-overlap risk made bulk rename unsafe; when you port, look up the prototype class and replace with the Figma component on the right.

### Chips — Figma `03-Mobile-Components` 2805:9087
Figma component: `Chips`. Variants: `State ∈ {Default, Selected}`.

| Prototype class | Maps to |
|---|---|
| `.s5-chip` | Chips / `State=Default` (or `Selected` when `.on`) — used as filter chips in Devices / AC1 |
| `.netchip` | Chips with status-dot prefix (composition; not a published variant) |
| `.tag-chip` | Chips with color dot (composition) |
| `.mt-chip` | Chips (manage tags sheet) |
| `.sub-tag-pill` | Chips |
| `.sb-filter` | Chips (search bar filter) |
| `.fg-chips`, `.kc-chips`, `.chips-row`, `.chips-scroll` | Container rows holding multiple Chips |

### Dropdowns — Figma `03-Mobile-Components` 3:40

| Prototype class | Maps to |
|---|---|
| `.cd-loc-dropdown`, `.cl-addr-dropdown` | Dropdown panel (autocomplete suggestions) |
| `.cd-loc-item`, `.cl-addr-item` | Dropdown row item |
| `.cd-select` | Dropdown trigger (read-only input with chevron) |
| `.opt-list`, `.opt-row` | Dropdown row list (filter sheet picker) |

### Bottom sheet — Figma `03-Mobile-Components` 3:44

All bottom sheets in the prototype are local compositions of the same Figma component:

| Prototype class | Notes |
|---|---|
| `.sheet`, `.sheet-backdrop`, `.sheet-handle` | Base bottom sheet structure |
| `.sheet-title`, `.sheet-title-row`, `.sheet-row`, `.sheet-list`, `.sheet-foot`, `.sheet-clear` | Slots inside the sheet |
| `.addsub-sheet` | Bottom sheet — Add sublocation form |
| `.tags-sheet`, `.manage-tags-sheet` | Bottom sheet — tag picker / management |
| `.location-menu-sheet` | Bottom sheet — location overflow menu |
| `.info-sheet`, `.info-sheet-body`, `.info-sheet-foot`, `.info-sheet-title` | Bottom sheet — informational content |
| `.preview-sheet` | Bottom sheet — camera preview |
| `.status-sheet` | Bottom sheet — status filter |
| `.type-sheet` | Bottom sheet — device-type filter |
| `.edit-sheet`, `.edit-sheet-body` | Bottom sheet — edit controls |

### Checkboxes — Figma `03-Mobile-Components` 3:34

| Prototype class | Maps to |
|---|---|
| `.cb-row` | Checkbox row (label + input) |
| `.cb-box`, `.cb-square` | Checkbox box |
| `.cb-label` | Checkbox label |
| `.cn-checkbox-row`, `.cam-cb-wrap`, `.conf-cb-wrap`, `.th-cb-wrap` | Context-specific compositions of the same checkbox component |

### Radio button — Figma `03-Mobile-Components` 3:51

| Prototype class | Maps to |
|---|---|
| `.radio` | Radio dot |
| `.radio-row` | Radio row (full-width tap target) |
| `.radio-body` | Label + supporting text inside radio row |
| `.radio-wrap` | Padding wrapper |
| `.type-radio-list`, `.type-radio-opt` | Radio list (device-type picker) |

### Toggles — Figma `03-Mobile-Components` 3:56

| Prototype class | Maps to |
|---|---|
| `.toggle-sw` | Toggle (iOS-style switch) |
| `.cd-toggle-row`, `.cd-toggle-desc-row` | Toggle row with label + description |

### Tabs — Figma `03-Mobile-Components` 3:54

Figma includes top-bar tab variants and stand-alone tabs.

| Prototype class | Maps to |
|---|---|
| `.cd-tabs`, `.cd-tab` | Tabs (camera details: Edit / Info) |
| `.cam-tabs`, `.cam-tabs-group` | Top bar segmented (cam streams: Mainstream / Substream) — see Top bar `Type=With Segmented controls` |
| `.segmented`, `.segmented-opt` | Segmented control |
| `.bottom-tabs`, `.bottom-tab` | Bottom tab bar (note: not in your Figma list, may be bespoke / future) |

### Search input — Figma `03-Mobile-Components` 3656:1886

| Prototype class | Maps to |
|---|---|
| `.search-row` | Search input (top bar `Type=Search bar`) |
| `.s5-search-row`, `.s5-search-pill`, `.s5-search-collapse` | Search-active mode pill (Pattern from Figma node `5:6765`) |
| `.cl-addr-search` | Address search input (magnifier prefix + autofill dropdown). Composition of Search input + Dropdown. |

### Badges — Figma `03-Mobile-Components` 3:30

| Prototype class | Maps to |
|---|---|
| `.status-dot` (`.ok`, `.warn`, `.err`, `.info`) | Badge / status dot |
| `.cam-conn-dot` | Badge / connection status dot |
| `.cfg`, `.batch` | Badge / inline pill |
| `.netchip .dot` | Badge variant inside chip |
| `.conf-status-ico` | Badge / status icon |

### Messages — Figma `03-Mobile-Components` 3:45

| Prototype class | Maps to |
|---|---|
| `.cd-info-bar`, `.info-msg`, `.info-strip` | Message / Info |
| `.cd-success-bar`, `.success-card`, `.cc-success-msg` | Message / Success |
| `.banner`, `.scan-banner` | Banner (top-of-screen info banner) |
| `.cd-test-msg` | Message / Inline test result |
| `.conf-fail-card` | Message / Error card |
| `.cn-test-output` | Message / Test output |

### Notification (toast) — Figma `03-Mobile-Components` 1432:7385

| Prototype class | Maps to |
|---|---|
| `.copy-toast`, `.undo-toast` | Toast / Notification |

(JS hook: `showCopyToast(...)` in the script — port to the design-system Toast trigger.)

### Modal — Figma `03-Mobile-Components` 3364:2512

No modal currently in the prototype. Reserve the component for confirm-delete and leave-flow guards.

---

## 4. Bespoke (do not port)

These are prototype-only chrome and never ship in the real app. They exist to make the HTML preview feel like a phone for screenshot review.

| Class / element | Why |
|---|---|
| `.workspace`, `.flowmap`, `.fm-step`, `.fm-section`, `.fm-group*`, `.fm-steps` | Designer-only flow navigator (left rail) |
| `.device`, `.screen`, `.screens`, `.stage` | Phone-frame chrome around the screen |
| `.status-bar`, `.indicators`, `.sig-bar`, `.battery` | iOS status-bar mock |
| `.home-indicator` | iOS home-indicator chrome |
| `data-typing`, `data-addr-mode`, `data-tab`, `data-addr-mode`, `data-tz-derived` | Demo state attributes that gate dropdown / map / parsed-fields visibility — replace with real component state |
| Mock data arrays in `<script>` (e.g. `LOC_SUGGESTIONS`, `CL_ADDR_SUGGESTIONS`, `coreDrafts`, `tagLibrary`) | Mock fixtures — replace with real APIs |

Mock keyboard has been removed.

---

## 5. Known design-system gaps to discuss with the design-system team

1. **Solid-red destructive button.** The prototype uses one (`.button-caution`). Figma's `Tertiary Caution` Hierarchy is text-only red; there is no `Hierarchy=Caution` solid-red. Either add a destructive Hierarchy or accept that red-CTA ships outside the system.
2. **Top bar variants.** No `Type=Stepper` variant — the "step screen" top bar (back button + title + stepper progress bar below) is composed locally. Worth a published variant.
3. **Bottom tab bar.** Used by the prototype's Devices / AC1 lists (`.bottom-tabs`) but not in the component list you sent. Confirm whether one exists.
4. **Stepper with N-of-M label.** Figma's Stepper variants are `33%/50%/66%/100%` (fill width only). The label "1 of 3 – Core" is composed locally. Worth a published variant or composed pattern doc.
5. **Address autofill dropdown.** Currently a local composition of Search input + Dropdown items. Worth a published "Search with autofill" pattern.
6. **Toast.** The prototype shows the toast at `bottom: 24px` with `box-shadow: var(--shadow-2xl)`. Confirm with notification component spec.
7. **Radius tokens.** Figma's color and shadow tokens are published, but no radius tokens were referenced in the foundation file URLs you sent. The prototype uses a single `--r: 4px`. Confirm whether radius is in another node.

---

## 6. File layout

- `mobile-bulk-add-cameras.html` — the prototype, single file
- `assets/` — small static images
- `HANDOFF.md` — this file
- `CLAUDE.md` — design-task notes / decisions log (keep for context, not for porting)

To preview: `open mobile-bulk-add-cameras.html` (macOS) or open in any modern browser.
