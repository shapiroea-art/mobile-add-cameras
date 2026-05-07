# Configure step — default selection of authenticated cameras

**Date:** 2026-05-07
**Scope:** `mobile-bulk-add-cameras.html`, Configure step (5 of 5, panel `s4`).

## Problem

On the Configure step today, **all** detected cameras — including ones that fail authentication — sit in the "to add" set. The CTA reads `Add 12 cameras` and commits everything. The only way to exclude a row is the trash icon (hard-remove). There is no per-row include/exclude toggle, and no signal that failed-auth cameras shouldn't be committed by default.

Feedback: cameras that didn't pass authentication should not be added by default; the user must be able to deselect cameras that are currently included and select cameras that aren't.

## Decision summary

1. Default: successfully authenticated cameras are **selected**; failed-auth cameras (`authfail`, `timeout`) are **unselected**.
2. Each row gets a leading-edge **checkbox**. The trash icon is **removed** — checkbox replaces it.
3. Failed-auth cameras can still be checked without first fixing credentials. Tapping a failed row's checkbox simply opts it into the commit; the existing post-commit progressive status list handles the resulting failure with the existing Retry / Add credentials affordances.
4. A **master checkbox** sits in the table header, tri-state.
5. A persistent banner at the top of the list explains the new default while any failed row is unchecked.
6. Bottom sel-block and primary CTA reflect *selected* count, not row count.

## Detailed design

### Default state on entering Configure (s4)

| Row state | Default checkbox |
|-----------|------------------|
| `connecting` → `connected` → `online` | **Checked** |
| `authfail`, `timeout` | **Unchecked** |

Default is computed per row as the row's state resolves during the connect animation (`connecting` → terminal). While a row is still `connecting`, the checkbox is rendered **unchecked and disabled** (greyed). On terminal state, it flips: `online` → checked + enabled; `authfail` / `timeout` → unchecked + enabled. The CTA counter rises as rows resolve.

### Row anatomy

Left to right:

1. **Checkbox** (replaces trash icon). Same `.cb` square style used elsewhere in the prototype.
2. Thumbnail (`conf-thumb` / `conf-thumb-off` for failed states) — unchanged.
3. Body: status icon + name + meta line — unchanged.
4. Chevron — unchanged.

Tap targets:

- **Checkbox** → toggle `c.checked`.
- **Thumbnail** → open inline preview (existing).
- **Chevron** → expand details (existing).
- **Row body** (anywhere not the above and not the fail card) → expand details (existing).
- **Fail card** Retry / Add credentials → unchanged. On success → row auto-checks (see "State transitions").

Visual state of an **unchecked** row:

- Thumbnail: 60% opacity.
- Name: `text-subtle` (was `text-bold`).
- Meta line: unchanged.
- The blue `selected` outline used elsewhere in the prototype does **not** apply here.
- The red fail card stays at full strength when present — it is the actionable bit.

### Header

The table header gets a master checkbox at the leading edge.

Layout (left to right):
- Master checkbox (tri-state)
- "Cameras" label
- Right-aligned: "Expand all" button (unchanged)

Master checkbox state:
- All visible rows checked → **checked**.
- All visible rows unchecked → **unchecked**.
- Mixed → **indeterminate** (dash / minus).

Master tap behavior:
- When checked or indeterminate → deselect all visible rows.
- When unchecked → select all visible rows (including failed rows; explicit, no special-casing).

"Visible" rows = rows currently rendered after any active filter (e.g., the `M errors` chip filter is applied).

### Banner stack (top of list, above the table header)

Banners stack in priority order:

1. **Capacity exceeded** banner (existing `#s4CapBanner`) — unchanged. If the total `load` of *checked* rows exceeds core capacity, this banner shows and the CTA is disabled per existing logic.
2. **Auth-failed banner** (new) — visible whenever at least one failed row is unchecked. Copy:

   > *"N cameras couldn't authenticate and weren't selected. Tap the checkbox to include anyway."*

   - `N` = count of `authfail` + `timeout` rows that are currently unchecked.
   - Banner hides when `N = 0` (all failed rows are either checked, successfully retried, or no failures remain).
   - Re-shows if a checked-failed row becomes unchecked again or a fresh failure surfaces.

Style: reuses the existing `info-msg` / `cap-banner` pattern.

### Bottom sel-block

Left to right:

- Camera icon (existing).
- Text: **`N cameras to add`**, where `N` = checked-row count. Singular: `1 camera to add`.
- Trailing chip: **`M errors`** where `M` = count of *checked* rows whose state is `authfail` or `timeout`.
  - Hidden when `M = 0`.
  - Tap toggles a filter that limits the visible rows to checked-failed rows.
  - When the filter is active, the master checkbox and bulk actions operate on those visible rows only.

Repurposing rationale: with the new default, unchecked failures are the system's default; the chip is most useful for surfacing failures the user has *opted into*, since those are the ones they need to fix or de-select.

### Primary CTA

- Text: **`Add N cameras`**, where `N` = checked-row count. Singular: `Add 1 camera`.
- `N = 0` → CTA reads **`Add cameras`** and is disabled.
- Capacity-exceeded banner takes precedence: if checked-row load exceeds core capacity, the existing capacity logic disables the CTA.

### State transitions

| Trigger | Result |
|---------|--------|
| Retry on failed row succeeds (`authfail/timeout → online`) | Row auto-checks. Fail card removed. Banner count decrements. |
| Retry on failed row fails again | Row stays unchecked. Banner unchanged. Existing "Retry failed — credentials still incorrect" reason shown. |
| Add credentials succeeds | Same as retry succeeds. |
| Add credentials fails | Same as retry fails. |
| User manually checks a failed row, then a subsequent re-attempt fails | Row stays **checked** (user intent wins). Counts toward the `M errors` chip. |
| All rows unchecked | Master → unchecked. Sel-block: `0 cameras to add`. CTA disabled. |
| Filter active + master tapped | Operates on visible rows only. |

### Out of scope

- **Post-commit status screen (s5).** No changes. Whatever ends up in the "to add" set at commit time is sent through the existing stagger-cycle + retry UX.
- **Hard-remove from the Configure list.** Replaced by deselect-only. If a "hide unselected" header action is ever needed, it can be added later as a separate change.
- **Trash icon's existing undo toast.** Removed alongside the trash icon. Deselect doesn't need undo (one tap restores).
- **Row outline on selection.** Not used on this screen. Selection is the checkbox.

## Open questions

None blocking implementation. Final wording of the auth-failed banner is settled in copy review during build.

## Acceptance checklist

- [ ] Entering Configure: successful rows checked, failed rows unchecked.
- [ ] Each row has a leading checkbox; trash icon removed; tap targets behave as specified.
- [ ] Unchecked rows are visually dimmed; failed-unchecked rows still show the red fail card.
- [ ] Master checkbox in the header is tri-state and operates on visible rows.
- [ ] Auth-failed banner shows with correct count; hides when no unchecked failures remain.
- [ ] Sel-block text and `M errors` chip reflect *selected* counts; chip filters list to checked-failed rows.
- [ ] CTA reads `Add N cameras` (singular handled), disables at 0, respects capacity logic.
- [ ] Retry / Add credentials success → auto-check + banner decrement.
- [ ] Capacity banner + auth-failed banner stack correctly.
- [ ] No regressions to thumbnail preview, expand-all, edit-camera sheet, or post-commit (s5) flow.
