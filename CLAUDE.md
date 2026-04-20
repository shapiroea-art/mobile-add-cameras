# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Design project for **Lumana VMS** — adding a **mobile add-cameras flow** (with bulk add and multi-brand / ONVIF support) to close a desktop/mobile parity gap. Desktop already has add-camera; mobile currently has none.

Lumana is an AI-based physical security VMS for enterprise. Desktop product surfaces: Control Center, Traffic & Parking, Devices, AI Search, Alert/Zone monitoring, Dashboards, Archives, Camera Walls, Reports, Cases, Workflows, Site Planner, Org Database. A mobile app exists (viewer-focused today).

## Repository state

Currently contains an interactive HTML prototype at `mobile-bulk-add-cameras.html`. Open with `open mobile-bulk-add-cameras.html` (macOS) to review. No build/test stack yet — when a codebase lands here, update this file with the commands.

## Design system (sourced from Figma node `5:100129`)

- **Font:** Satoshi (400/500/700) via Fontshare CDN
- **Primary:** `#4367F6` (blue); primary border `#3456E0`; primary soft `#EEF2FE`
- **Grays:** `#101828` (text-bold) · `#344054` (text-default) · `#667085` (text-subtle) · `#98A2B3` (text-subtlest) · `#D0D5DD` (input border) · `#EAECF0` (default border) · `#F2F4F7` (gray-200 bg) · `#F9FAFB` (surface-subtle)
- **Status:** success `#12B76A`, warning `#F79009`, danger `#F04438` — placeholders until Figma token confirmed
- **Radius:** 4px throughout (matches Figma)
- **Phone frame:** 360px width
- **Type scale:** 24/500 (page title), 16/500 (subtitle, button), 16/400 (body), 14/500 (stepper label, meta), 12 (small labels), 10–11/600 uppercase (badges, field labels)
- **Stepper pattern:** `N of M – StepName` label + 6px progress bar with primary fill

## Competitive context (2026-04-19)

Researched 20 competitors (Verkada, Ambient AI, Avigilon, Spot AI, Milestone, Uniview, Ava Security, BriefCam, Brivo, Coram, Dahua, Flock, Genetec, Hikvision, Immix, Lorex, Network Optix, Rhombus, Ubiquiti, Wyze). Key findings that should shape design decisions:

- **No competitor ships true mobile bulk-add across multiple camera brands.** White-space for Lumana.
- Closest benchmarks: **Rhombus** (Bluetooth bulk + one-QR-for-many, own-brand only), **Avigilon Alta** (QR + Alta Key, own-brand only, sequential), **UniFi Protect** (LAN discovery + Bluetooth proximity).
- Enterprise VMS (Milestone, Genetec, Avigilon Unity, Nx, BriefCam) keep provisioning **desktop-only** — mobile is viewer/operator only.
- OEM apps (Hik-Connect, DMSS, EZView) are mobile-first but single-device, QR/serial-dependent, and tied to their own ecosystems; third-party ONVIF is pushed to the NVR UI or desktop.
- Most third-party cameras don't carry a proprietary QR code — so the **primary discovery primitive on mobile should be network / ONVIF WS-Discovery**, with QR as a secondary path.

Pain points from competitor reviews to design against: case-sensitive verification codes (Hik), "already registered to another account" lockouts (Uniview), 2.4 GHz Wi-Fi quirks surfaced only on failure (Wyze), post-update device disappearance (Alta), sequential flows that don't recover state on interrupt (DMSS, EZView).

## UX decisions (2026-04-20)

Flow is **4 steps** (Core → Discover → Authenticate → Review) + a post-commit status screen outside the stepper.

**1. Scan-first, no method picker.** Flow starts scanning automatically when the user enters step 2. Alternative add methods (*Add by IP · Scan IP range · Scan QR · Add by RTSP*) live in an overflow (kebab) menu in the top bar and a "Didn't find it? Add manually" row below the scan results. Why: a method-picker screen was a tax on every installer for a decision that's the same ~95% of the time. Why not filter chips: filters narrow the current result set; add methods *acquire* new cameras — conflating them confuses users.

**2. Scan runs ~20s with background-safe copy.** Progress bar driven by elapsed-time ETA (not actual addresses scanned, which come in bursts); the "Found" counter is the real liveness signal. Copy: primary "Scanning network · about 20 seconds" + secondary "You can leave this screen — scanning continues in the background." No push-notification promise until engineering confirms it's wired up. Open engineering question: does the scan actually survive iOS/Android backgrounding at 30s+?

**3. Audio/mic is auto-detected — no toggle in bulk add.** Per-camera mic control lives in the device detail screen *after* add, where the admin who cares about privacy/compliance looks anyway. Why: fewer decisions for installers, cleaner rows, same capability for downstream config.

**4. Post-commit is a progressive status list (no ring, no overlay).** Tapping "Add N cameras" navigates straight to the status list with all rows at **Connecting…**. Over ~3–4 seconds, rows stagger-cycle through **Connected** (intermediate: stream up, verification pending) → **Online** (verified and streaming). 50–300ms jitter per row so it doesn't look mocked. Two problematic cameras remain for the retry UX: one flips to **Auth failed** at ~1.5s, another to **Connection timeout** at ~2.5s. A live counter (Online · Connecting · Failed) sits at the top. Retry-failed button appears only once failures surface. Why: honest live feedback matching the real technical pipeline, replaces the fake ring progress with the real progression.

## Working conventions

- Start any critique or "how should we design X" question with the `brainstorming` skill before jumping to answers.
- Any UI creation (HTML prototypes, components, screens) goes through the `frontend-design` skill.
- Figma MCP is primary for design context: run `get_design_context` + `get_screenshot` before implementing from a Figma node. Target 1:1 visual parity, validate against screenshot.
- No design tokens, colors, or type sizes invented — reuse what's in the Lumana design system or the file being edited.
