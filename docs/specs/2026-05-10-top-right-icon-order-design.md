# Top-right icon order on Devices & Location screens

**Date:** 2026-05-10
**Scope:** `mobile-bulk-add-cameras.html`, panels `sDevices`, `s0`, `sSublocation`.

## Problem

The two contexts disagree on where their top-right action icons sit:

- Devices: `[Add location] [Filter] [Notifications]`
- Locations (s0 / sSublocation): `[Edit] [Add] [Filter]`

Add and Filter exist on both, but in different positions. A user who learns one screen does not get a free read on the other. The screen-specific icon (Notifications on Devices, Edit on Locations) lands in different slots too.

## Decision

Adopt a single positional convention across all three panels:

| Slot | Role | Devices | Locations |
|--|--|--|--|
| 1 (leftmost of right group) | Create new content | Add location | Add (sublocation/core/camera) |
| 2 | Filter the current view | Filter | Filter |
| 3 (rightmost) | Screen-meta utility | Notifications | Edit |

## Rationale

- **Slots 1 and 2 are stable across screens.** The body-related action pair (Add, Filter) sits in the same place every time. Muscle memory carries.
- **Slot 3 is the screen-meta utility** — globally that's Notifications (alerts at the entry level); inside a location it's Edit (this-location's metadata). Both behave like utilities that aren't part of the create-or-filter loop.
- **Notifications stays rightmost on Devices** — preserves the convention adopted in the previous icon-order commit (`2281b4d`).
- **Add is rightmost on neither screen.** That's a deliberate trade — placing the screen-specific icon at slot 3 is what gives the cross-screen consistency. The cost is that the primary verb (Add) doesn't sit at the most thumb-accessible corner.

## What changes

- **Devices (`sDevices`)**: No change.
- **s0 (Administrative Center 1)**: Edit moves from slot 1 (leftmost) to slot 3 (rightmost). Add takes slot 1, Filter takes slot 2.
- **sSublocation**: Same change as s0.

## Acceptance checklist

- [ ] s0 top-right reads, left to right: Add — Filter — Edit.
- [ ] sSublocation top-right reads, left to right: Add — Filter — Edit.
- [ ] sDevices top-right unchanged: Add location — Filter — Notifications.
- [ ] All icons retain their plain styling (no fills), 40×40 hit target, `text-subtle` color.
- [ ] Filter funnel still wired to `enterFilterMode(<panelId>)`.
- [ ] Filter badge dot still anchors to the funnel (slot 2) on Locations and (slot 2) on Devices — unchanged because the `.fl-icon-wrap` class follows the Filter button regardless of slot.
- [ ] Tap targets and onclick handlers unchanged from before.

## Out of scope

- s5 (Added landing) — already merged into s0.
- Camera/Core panels.
- Any icon style changes (already plain across the board).
- Touch-target size bump (separate critique recommendation).
