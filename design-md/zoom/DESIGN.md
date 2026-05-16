# Design System Inspiration of Zoom (iOS)

## 1. Visual Theme & Atmosphere

Zoom's iOS app is built around one moment: the call. Everything else — the Meetings list, Team Chat, Mail, Phone — is a calm, neutral utility surface that exists to get you *into* the call as fast as possible. Outside a meeting the app supports both light (`#FFFFFF`) and dark (`#1A1A1A`) appearances; inside a meeting the experience snaps to a committed dark theater (`#1A1A1A`) so that participant video — the actual content — is the brightest thing on screen. The design philosophy is "stage and crew": the UI is the crew, dressed in black, and the video tiles are the stage.

The accent is Zoom Blue `#2D8CFF` — a confident, friendly blue used for the one action that matters on each screen: the big "Join" button, the "Start" pill, the active control state, links. It is the single brand color, used sparingly so that it always reads as *the* primary action. The one chromatic exception is the in-call red `#E02828`, reserved exclusively for "Leave"/"End" and the muted-microphone indicator — the two moments where the app needs to communicate urgency or a live-risk state. There is no third accent; status and structure are carried by gray, white, and the blue.

Typography is a clean grotesque — Zoom ships with a custom-tuned face that is closely approximated by Lato / Inter — used at 400 / 600 / 700 across a compact 12–28pt range. There are no decorative display headlines; the most expressive typographic surface is the meeting-join screen, where the meeting topic sits at 22–24pt bold over the dark theater while the participant grid waits behind it. In the gallery grid, name labels are deliberately small (13pt) and tucked into the corner of each tile so they never compete with faces.

**Key Characteristics:**
- Dual appearance outside calls (`#FFFFFF` / `#1A1A1A`); committed dark theater (`#1A1A1A`) inside a call
- Zoom Blue (`#2D8CFF`) as the single accent — the big Join button, Start pill, active controls, links
- In-call red (`#E02828`) reserved for Leave/End and the muted-mic indicator only
- Gallery video grid — rounded tiles with corner name labels and a mic-mute badge per participant
- Active-speaker highlight — a 3pt Zoom-blue border animates onto whoever is talking
- In-call control bar — a floating dark bar: mic / video / share / participants / react / leave
- Bottom tab bar (Meetings / Team Chat / Mail / Phone / More) — neutral, utilitarian
- Big blue "Join" as the signature CTA — oversized, unmissable, the app's center of gravity

## 2. Color Palette & Roles

### Primary
- **Zoom Blue** (`#2D8CFF`): Primary CTA (Join, Start), active control state, links, selected tab, toggles.
- **Blue Pressed** (`#1F6FCC`): Active/pressed state for blue buttons.
- **Blue Tint** (`rgba(45,140,255,0.12)`): Selected-row wash, subtle highlight behind active items.

### Canvas & Surface (Dark — in-call + dark mode)
- **Canvas** (`#1A1A1A`): In-call theater background; dark-mode app canvas.
- **Surface 1** (`#2D2D2D`): Cards, list rows, the in-call control bar, sheets.
- **Surface 2** (`#3A3A3A`): Higher elevation — popovers, pressed rows, video-tile placeholder.
- **Divider** (`#3A3A3A`): Hairline dividers between list rows.
- **Text Primary** (`#FFFFFF`): Names, titles, primary UI text on dark.
- **Text Secondary** (`#B0B0B0`): Metadata, timestamps, secondary labels on dark.
- **Text Tertiary** (`#7A7A7A`): Disabled, placeholder, very-low-emphasis labels.

### Canvas & Surface (Light — outside-call default)
- **Light Canvas** (`#FFFFFF`): App background in light appearance.
- **Light Surface** (`#F5F5F5`): Cards, grouped-list backgrounds, search fields.
- **Light Divider** (`#E5E5E5`): Hairline dividers in light appearance.
- **Light Text Primary** (`#1A1A1A`): Titles, names in light appearance.
- **Light Text Secondary** (`#6B6B6B`): Metadata in light appearance.

### Semantic
- **In-Call Red** (`#E02828`): Leave/End meeting button, muted-microphone glyph and badge.
- **Red Pressed** (`#B91F1F`): Pressed state for the End button.
- **Success Green** (`#0E8A45`): "Connected", recording-saved, success toast.
- **Recording Red** (`#E02828`): The recording dot indicator (shared with in-call red).
- **Raise-Hand Yellow** (`#F5C518`): Raised-hand glyph and the "reactions" hand badge.
- **Warning Amber** (`#F5A623`): Weak-connection / poor-network banner.

## 3. Typography Rules

### Font Family
- **Primary**: Zoom's custom grotesque — closely approximated by `Lato` or `Inter` at 400 / 600 / 700
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures on the call timer and meeting IDs (`font-variant-numeric: tabular-nums`)

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Lato | 28pt | 700 | 1.2 | -0.3pt | "Meetings", "Team Chat" nav large title |
| Meeting Topic | Lato | 22pt | 700 | 1.25 | -0.2pt | Topic on the join / pre-meeting screen |
| Section Header | Lato | 17pt | 700 | 1.3 | -0.1pt | "Today", grouped-list headers |
| List Title | Lato | 16pt | 600 | 1.3 | 0pt | Meeting name, chat name, contact name |
| Body | Lato | 15pt | 400 | 1.45 | 0pt | Chat message text, descriptions |
| Button (Primary) | Lato | 17pt | 700 | 1.0 | 0pt | The big "Join" / "Start" button |
| Button (Control) | Lato | 11pt | 600 | 1.1 | 0pt | Label under each in-call control glyph |
| Metadata | Lato | 13pt | 400 | 1.35 | 0pt | "10:00 AM · 45 min", message timestamp |
| Tile Name Label | Lato | 13pt | 600 | 1.0 | 0pt | Participant name in the gallery tile corner |
| Call Timer | Lato | 14pt | 600 | 1.0 | 0pt | "12:34" elapsed, tabular |
| Meeting ID | Lato | 14pt | 400 | 1.0 | 0.4pt | "842 1234 5678", tabular |
| Tab Label | Lato | 10pt | 600 | 1.0 | 0.1pt | Bottom tab bar labels |
| Tiny Label (UPPER) | Lato | 11pt | 700 | 1.2 | 0.4pt | "RECORDING", small caps labels |

### Principles
- **Weights concentrated at 400 / 600 / 700**: Regular for body, semibold for list/tile labels, bold for headers and the Join button — no light, no black
- **The Join button is the loudest type on screen**: 17pt bold in white on Zoom Blue; nothing else competes
- **Tile name labels stay small**: 13pt semibold so faces, not text, own the gallery grid
- **Tabular numerics on the timer and meeting IDs**: The call timer and 11-digit meeting IDs never reflow
- **Minimal tracking**: Negative tracking only on titles; IDs get +0.4pt for digit legibility

## 4. Component Stylings

### Buttons

**Primary "Join" / "Start" Button (the signature CTA)**
- Background: `#2D8CFF`
- Text: `#FFFFFF`, Lato 17pt weight 700
- Padding: 14pt vertical, 28pt horizontal
- Corner radius: 8pt
- Height: 48pt (oversized — the app's center of gravity)
- Pressed: background `#1F6FCC`, scale 0.98, medium haptic
- Disabled: background `#3A3A3A`, text `#7A7A7A`

**Secondary Button ("Start a meeting" outline, "Schedule")**
- Background: transparent
- Text: `#2D8CFF`, Lato 16pt weight 600
- Border: 1pt solid `#2D8CFF`
- Padding: 12pt vertical, 20pt horizontal
- Corner radius: 8pt
- Pressed: background `rgba(45,140,255,0.12)`

**In-Call Control Button (mic / video / share / participants / react)**
- Layout: 28pt glyph stacked above an 11pt w600 label
- Hit target: 56pt wide, full bar height
- Default: glyph + label `#FFFFFF`
- Active/On (e.g., video on): glyph tinted, subtle `#3A3A3A` rounded background
- Muted state (mic off): glyph and label `#E02828`
- Spacing: distributed evenly across the control bar

**Leave / End Button**
- Background: `#E02828`
- Text: `#FFFFFF`, Lato 15pt weight 700
- Padding: 8pt vertical, 18pt horizontal
- Corner radius: 8pt
- Pressed: background `#B91F1F`, scale 0.98
- Tapping opens a confirm sheet: "Leave Meeting" / "End Meeting for All"

### Cards & Containers

**Meeting List Row**
- Height: 72pt
- Layout: time block (left, 13pt) → meeting topic (16pt w600) + subtitle (13pt `#B0B0B0`) → trailing blue "Join" pill
- Background: `#2D2D2D` (dark) / `#FFFFFF` (light), grouped under "Today" / "Tomorrow" headers
- Pressed: background `#3A3A3A` (dark) / `#F5F5F5` (light)
- Trailing Join pill: 32pt tall, `#2D8CFF`, white 14pt w700 text, 500pt radius

**Gallery Video Tile (the signature element)**
- Aspect: 16:9 in the grid; corner radius 8pt
- Background placeholder: `#3A3A3A` with a centered circular avatar (initials) when video is off
- Name label: bottom-left, 13pt w600 white over a `rgba(0,0,0,0.45)` rounded chip with 6pt padding
- Mic-mute badge: bottom-left corner, a 16pt `#E02828` mic-slash glyph on a dark circle
- Active speaker: a 3pt `#2D8CFF` border inset that animates on (opacity + slight scale) when audio is detected
- Grid gaps: 4pt between tiles; 1 / 2 / 4 / 6 / 9-up layouts depending on participant count

**In-Call Control Bar**
- Position: floating at the bottom, full width minus 12pt inset, 72pt tall
- Background: `#2D2D2D` at 96% opacity with a subtle top hairline `#3A3A3A`
- Corner radius: 16pt (floating card feel)
- Contents: Mute · Stop Video · Share · Participants · React · Leave (red)
- Auto-hide: fades out after ~4s of no interaction; tap anywhere on video to bring it back

**Pre-Meeting / Join Card**
- Full-screen dark theater (`#1A1A1A`)
- Self-preview video at the top (rounded 12pt)
- Meeting topic 22pt w700 white centered
- Meeting ID 14pt `#B0B0B0` tabular
- Toggles: "Join with video" / "Join with audio" — switches tinted `#2D8CFF` when on
- Big blue Join button pinned near the bottom

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#2D2D2D` (dark) / `#FFFFFF` (light) with a top hairline divider
- Tabs: Meetings · Team Chat · Mail · Phone · More
- Icon size: 24pt; label 10pt w600
- Active: icon + label `#2D8CFF`
- Inactive: `#B0B0B0` (dark) / `#6B6B6B` (light)
- Active tab icon: filled variant; inactive: outlined

**Top Bar**
- Height: 44pt + safe area
- Leading: contextual ("Edit" / back) ; Center: screen title (17pt w700) ; Trailing: "+" or search (24pt)
- Background matches appearance; subtle blur on scroll in light mode, opaque in dark

**In-Call Top Strip**
- Minimal: leave-context chip, meeting name, call timer (14pt w600 tabular), camera-flip, and a "Speaker"/"Gallery" view toggle
- Background: transparent over the theater; auto-hides with the control bar

### Input Fields

**Search Bar (Chat / Contacts)**
- Background: `#2D2D2D` (dark) / `#F5F5F5` (light)
- Height: 40pt
- Corner radius: 10pt
- Leading SF Symbol `magnifyingglass`, 16pt `#B0B0B0`
- Placeholder: "Search", 15pt w400 `#7A7A7A`
- Focus: no border change; caret `#2D8CFF`

**Join-by-ID Field**
- Background: `#2D2D2D` (dark) / `#FFFFFF` (light), 1pt border `#3A3A3A`
- Height: 48pt, corner radius 8pt
- Text: 18pt w400 tabular, centered (digit-friendly)
- Placeholder: "Meeting ID", `#7A7A7A`
- Focus: border `#2D8CFF`

**Chat Composer**
- Pill input, `#2D2D2D` (dark) / `#F5F5F5` (light), 40pt min height, 500pt radius
- Trailing send button: circular 32pt `#2D8CFF` with a white arrow when text present, otherwise dim

### Distinctive Components

**Gallery Video Grid**
- The defining surface — a responsive grid of 16:9 rounded tiles
- Layouts: 1-up (full), 2-up (stacked), 4-up (2×2), 6-up (2×3), 9-up (3×3), with paging dots if more
- Each tile carries: video or avatar placeholder, corner name chip, mic-mute badge
- Pinch / swipe to page between groups; double-tap a tile to pin (switches to speaker view)

**Active-Speaker Highlight**
- A 3pt `#2D8CFF` inset border that fades + scales onto the tile of whoever is speaking
- Debounced ~300ms so it doesn't flicker between people; only one tile highlighted at a time
- In speaker view, the active speaker fills the stage and others become a filmstrip

**In-Call Control Bar (auto-hide)**
- Six controls, evenly spaced; the Leave control is always red and rightmost
- The whole bar slides down + fades after inactivity; any tap on the video stage restores it with a 200ms spring

**Raise Hand / Reactions**
- A reactions tray slides up: 👍 ❤️ 😂 😮 👏 🎉 plus a "Raise Hand" toggle
- Raised hand renders a `#F5C518` hand glyph badge on the participant's tile and in the participant list
- Emoji reactions float up from the bottom of the speaker's tile and fade after ~3s

**Recording Indicator**
- Top-left: a pulsing `#E02828` dot + "Recording" 11pt w700 UPPER label
- The dot scales 1.0 → 0.7 → 1.0 on a 1.2s loop to signal "live"

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 48, 56
- Standard margin: 16pt horizontal; grouped lists inset 16pt
- Gallery tile gap: 4pt (tight — maximize face size)

### Grid & Container
- Content width: full device width, 16pt horizontal margins
- Meeting / chat lists: single column, grouped by day
- Gallery grid: edge-to-edge with 4pt gaps; control bar floats above with a 12pt side inset
- Speaker view: one full-bleed stage + a scrollable filmstrip strip

### Whitespace Philosophy
- **The stage is sacred**: In a call, the video grid takes nearly the entire viewport — chrome is minimal and auto-hiding
- **Lists are calm and roomy**: 72pt meeting rows give each meeting clear separation — this is a scan-and-tap surface
- **The Join button gets air**: It is oversized and surrounded by generous padding so it is impossible to miss

### Border Radius Scale
- Sharp (0pt): Dividers, full-bleed video stage
- Standard (8pt): Buttons, video tiles, input fields, Join pill base
- Comfortable (10–12pt): Search fields, self-preview, sheets
- Floating (16pt): The in-call control bar, modals
- Full Pill (500pt): Join row pill, chat composer, filter chips
- Circle (50%): Avatars, the chat send button, mic-mute badge background

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, video stage, canvas |
| Card (Level 1) | `rgba(0,0,0,0.25) 0 1px 4px` | Meeting rows lifted in light mode |
| Control Bar (Level 2) | `rgba(0,0,0,0.4) 0 8px 24px` | The floating in-call control bar |
| Sheet (Level 3) | `rgba(0,0,0,0.5) 0 -12px 40px` | Reactions tray, leave-confirm sheet |
| Active Speaker | 3pt `#2D8CFF` inset border + slight scale | The talking participant's tile |
| Scrim | `rgba(0,0,0,0.5)` | Behind modals and the More menu |

**Shadow Philosophy**: In the dark theater shadows barely register, so Zoom communicates the floating control bar with a soft, dark 24pt-blur shadow and a 16pt radius rather than a hard edge. The most important "elevation" cue is not a shadow at all — it is the animated 3pt blue active-speaker border, which lifts the talking tile forward visually. In light mode, meeting rows get a barely-there 1px card shadow; everything else is flat.

### Motion
- **Join button tap**: scale 0.98 → 1.0 spring (response 0.25, damping 0.8) with `.impactOccurred(.medium)`
- **Active-speaker border**: opacity 0 → 1 + scale 1.0 → 1.0 (border inset grows) over 220ms, debounced 300ms
- **Control bar auto-hide**: slide down 16pt + fade over 250ms after ~4s idle; restore in 200ms spring on tap
- **Reactions float**: emoji rises 120pt while fading from 1 → 0 over ~3s with slight horizontal drift
- **Tile re-layout**: grid reflow on participant join/leave with a 300ms ease-in-out
- **Mute toggle**: glyph cross-fades to red + a quick 1.0 → 0.9 → 1.0 bounce; `.impactOccurred(.light)`
- **Recording dot**: continuous 1.2s pulse (scale 1.0 ↔ 0.7)

## 7. Do's and Don'ts

### Do
- Use the committed dark theater (`#1A1A1A`) inside a call so video is the brightest element
- Reserve Zoom Blue (`#2D8CFF`) for the primary action (Join/Start), active controls, and links
- Reserve in-call red (`#E02828`) for Leave/End and the muted-mic state only
- Make the Join button oversized (48pt tall, 17pt bold) — it is the app's center of gravity
- Use rounded 8pt video tiles with small corner name labels so faces dominate
- Animate the 3pt blue active-speaker border with a ~300ms debounce so it never flickers
- Auto-hide the control bar after ~4s and restore it on any tap of the video stage
- Support both light and dark outside calls; snap to dark theater inside calls
- Use tabular numerics on the call timer and meeting IDs

### Don't
- Don't keep a light background inside a call — it competes with participant video
- Don't introduce a third accent color — blue for action, red for leave/mute, gray for everything else
- Don't make name labels large — 13pt max; the gallery is about faces, not text
- Don't let the active-speaker border flicker — debounce it
- Don't keep the control bar permanently visible over video — auto-hide is the behavior
- Don't use red for anything except Leave/End and mute — it must always mean the same thing
- Don't shrink the Join button to a normal-sized button — its size is the affordance
- Don't animate the grid reflow abruptly — participants joining/leaving should ease, not snap
- Don't use heavy display type — the meeting topic at 22pt bold is the ceiling

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 4-up max in portrait gallery; control labels may hide under 360pt |
| iPhone 13/14/15 | 390pt | Standard 6-up gallery, full control bar with labels |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the in-call top strip |
| iPhone 15/16 Pro Max | 430pt | 9-up gallery in portrait; larger Join button |
| iPad | 768pt+ | Up to 9–16 tiles, persistent control bar, sidebar for chat/meetings |

### Dynamic Type
- Meeting titles, body/chat text, metadata: scale with Dynamic Type
- Tile name labels: scale modestly, clamp at 15pt (must not overwhelm the tile)
- Call timer & meeting ID: fixed tabular (layout-critical)
- Control labels: fixed 11pt; hide entirely on very narrow widths rather than scale

### Orientation
- Meetings / Chat / Mail / Phone: **portrait-primary**
- In-call gallery & speaker view: **rotates to landscape** — grid recomputes to wide tiles, control bar stays bottom
- Screen share viewing: **landscape preferred**

### Touch Targets
- Join button: 48pt tall — unmissable
- In-call controls: 56pt wide hit area each, full bar height
- Meeting rows: full 72pt height tappable
- Tab bar icons: 24pt glyph, 44pt effective hit
- Tile pin (double-tap): the whole tile is the target

### Safe Area Handling
- Top: in-call strip respects Dynamic Island; lists respect the notch
- Bottom: the floating control bar sits above the home indicator with a 12pt inset; tab bar respects it
- Sides: 16pt content insets; gallery grid is edge-to-edge with 4pt internal gaps

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (in-call / dark): `#1A1A1A`
- Light canvas: `#FFFFFF`
- Surface 1: `#2D2D2D`
- Surface 2 / divider: `#3A3A3A`
- Primary text (dark): `#FFFFFF`
- Secondary text (dark): `#B0B0B0`
- Tertiary text: `#7A7A7A`
- Zoom Blue (action): `#2D8CFF`
- Blue pressed: `#1F6FCC`
- In-call red (leave/mute): `#E02828`
- Raise-hand yellow: `#F5C518`

### Example Component Prompts
- "Create a SwiftUI Zoom gallery tile: a 16:9 rounded-8pt container with background #3A3A3A. When video is off, center a 56pt circular avatar with initials. Bottom-left: a name chip 'Alex Rivera' in Lato 13pt weight 600 white over a rgba(0,0,0,0.45) rounded background with 6pt padding, and a 16pt #E02828 mic-slash badge. If the participant is the active speaker, add a 3pt #2D8CFF inset border that fades in over 220ms."
- "Build the Zoom Join button: background #2D8CFF, text 'Join' in Lato 17pt weight 700 #FFFFFF, 14pt vertical / 28pt horizontal padding, 8pt corner radius, 48pt tall (oversized). Pressed: background #1F6FCC, scale 0.98, medium haptic."
- "Design the Zoom in-call control bar: a floating bar at the bottom, full width minus a 12pt side inset, 72pt tall, background #2D2D2D at 96% opacity, 16pt corner radius, shadow rgba(0,0,0,0.4) 0 8px 24px. Six controls evenly spaced — Mute, Stop Video, Share, Participants, React, Leave — each a 28pt glyph above an 11pt weight 600 label in white; the muted Mute control is #E02828 and the Leave control is a red #E02828 pill. The bar slides down + fades after 4s of inactivity."
- "Create a Zoom meeting list row: 72pt tall on #2D2D2D. Left a time block '10:00 AM' in Lato 13pt, center the topic 'Weekly Sync' in 16pt weight 600 white plus '45 min · Zoom Meeting' in 13pt #B0B0B0, trailing a 32pt-tall #2D8CFF 'Join' pill (500pt radius, white 14pt weight 700)."
- "Build the Zoom recording indicator: top-left, a pulsing #E02828 dot (scale 1.0 ↔ 0.7 over 1.2s) next to 'RECORDING' in Lato 11pt weight 700 uppercase white."

### Iteration Guide
1. Inside a call, the canvas is the dark theater `#1A1A1A` — never light; video is the brightest element
2. Zoom Blue (`#2D8CFF`) is the only accent for primary action and active state; in-call red (`#E02828`) is only Leave/End and mute
3. The Join button is oversized (48pt / 17pt bold) — its size is the affordance
4. Video tiles are rounded 8pt with small (13pt) corner name labels — faces dominate, not text
5. The active-speaker highlight is a 3pt blue inset border, debounced ~300ms so it never flickers
6. The control bar auto-hides after ~4s and restores on any tap of the video stage
7. Support light + dark outside calls; the in-call theater is always dark
8. Typography is Lato/Inter 400/600/700 with tabular numerics on the timer and meeting IDs
