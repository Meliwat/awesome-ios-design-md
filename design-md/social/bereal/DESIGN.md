# Design System Inspiration of BeReal (iOS)

## 1. Visual Theme & Atmosphere

BeReal's iOS app is a deliberate rejection of the polished social-media aesthetic. The interface is a true-black canvas (`#000000`) with almost no chrome, no gradients, no decorative color — a stark, photographic darkroom where the only thing that matters is the unfiltered dual-camera moment. There is no brand color in the UI. The accent is white. This is a design philosophy, not an oversight: by stripping every gradient, glow, and tint, BeReal forces the eye onto the photo itself — the back camera's view of where you are, and the small front-camera selfie of your face reacting to it. The app feels less like Instagram and more like a disposable camera with a 2-minute timer taped to it.

The signature element is the dual-lens composite card: a full-bleed photo from the rear camera with a small, rounded, draggable selfie thumbnail pinned to the top-left corner. This composite is the atomic unit of the entire product — it appears in capture, in the friends feed, in your profile grid, and in the memories calendar. Everything else (reactions, comments, the timer banner) is layered on top of or around this card in plain white text on black. Authenticity signals are first-class UI: a "⚠ late" badge with a relative timestamp ("2h late"), a "RealMojis" reaction strip of tiny circular selfie-photos instead of abstract emoji, and small dots indicating how many retakes someone needed before posting.

Typography is San Francisco (SF Pro) used plainly — no custom typeface, no display weights, no expressive type moments. Headlines are compact, functional, and white. The most expressive "type" moment in the app isn't type at all: it's the 2-minute countdown banner that appears at the top of the feed when the daily notification fires ("⏰ 2 min left to capture a BeReal"). The restraint in type mirrors the restraint in color — BeReal is anti-design as design, and the consistency of that stance is what makes it feel honest.

**Key Characteristics:**
- True-black canvas (`#000000`) — maximum starkness, no warm-black softening
- No brand accent color — the accent is white; deliberate chromatic minimalism
- Dual-lens composite card: full back photo + small draggable rounded front selfie (top-left)
- 2-minute countdown banner — the daily ritual, top of the friends feed
- Authenticity tags as UI: "⚠ late", relative timestamps, retake-count dots
- RealMojis reactions — circular selfie-photos, never abstract emoji, no like counts
- SF Pro at 400 / 600 / 700, no custom font, no display sizes
- Bottom tab nav: Friends / Official / Search / Profile
- Haptics on dual-shutter capture and camera flip

## 2. Color Palette & Roles

### Canvas & Surfaces (Dark — the only mode)
- **Canvas Black** (`#000000`): The primary and only canvas — true black, full-bleed behind every screen.
- **Surface 1** (`#1C1C1E`): Sheets, bottom modals, comment composer, settings rows.
- **Surface 2** (`#2C2C2E`): Elevated rows, pressed states, chip backgrounds, RealMoji picker tray.
- **Divider** (`#2C2C2E`): Hairline separators between settings rows and comment threads.

### Text
- **Text Primary** (`#FFFFFF`): Usernames, captions, headlines, all primary text.
- **Text Secondary** (`#8E8E93`): Timestamps, "your friends", metadata, location strings, placeholder text.
- **Text Tertiary** (`#636366`): Disabled labels, very low-emphasis hints.

### Accent (Intentionally Monochrome)
- **Accent White** (`#FFFFFF`): The primary action color — capture ring, active tab, primary button fill. BeReal has *no* brand hue; white is the verb.
- **On-Accent Black** (`#000000`): Text/icon on white-filled buttons (intentional inversion, like the capture button).

### Semantic
- **Late Amber** (`#FFD60A`): The "⚠ late" authenticity badge and timer-warning accent — the single warm color, used only for lateness/urgency.
- **Error Red** (`#FF453A`): Failed posts, connection-lost, destructive confirm ("Delete BeReal").
- **Online Green** (`#30D158`): Rare — friend-active dot in messaging.

### Light Mode (Not Used)
BeReal's iOS app is dark-only by design — the photographic darkroom metaphor depends on true black. No light variant ships.

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro Text` / `SF Pro Display` (system) — BeReal ships no custom typeface
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for the countdown timer so digits don't jitter as they tick

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Timer Banner | SF Pro Display | 24pt | 700 | 1.1 | -0.3pt | "2 min left to capture" countdown headline |
| Screen Title | SF Pro Display | 22pt | 700 | 1.2 | -0.3pt | "Friends", "Discovery" nav title |
| Username (Card) | SF Pro Text | 17pt | 600 | 1.2 | -0.2pt | Poster's name above the dual-lens card |
| Caption | SF Pro Text | 16pt | 400 | 1.35 | 0pt | The optional one-line caption under a BeReal |
| Body | SF Pro Text | 15pt | 400 | 1.4 | 0pt | Comment text, settings descriptions |
| Comment Author | SF Pro Text | 14pt | 600 | 1.3 | 0pt | Bold name preceding a comment body |
| Meta / Timestamp | SF Pro Text | 13pt | 400 | 1.3 | 0pt | "2h late", "Paris, France", "12:43" |
| Late Badge | SF Pro Text | 12pt | 700 | 1.1 | 0.2pt | "⚠ LATE" authenticity tag |
| Button (Primary) | SF Pro Text | 17pt | 600 | 1.0 | -0.2pt | "Post", "Add a caption" |
| Tab Label | SF Pro Text | 10pt | 600 | 1.0 | 0.1pt | Bottom tab bar labels (when shown) |
| Retake Count | SF Pro Text | 11pt | 600 | 1.0 | 0pt | "3 retakes" pill under a posted card |

### Principles
- **System font only**: No custom face — BeReal's identity is the dual-lens layout, not type
- **Weight concentrated at 400 / 600 / 700**: Regular for body, semibold for names/buttons, bold for the timer
- **No display-size headlines except the timer** — the photo is the headline; the timer is the only "hero" type
- **Tabular numerals on the countdown** — `2:00 → 1:59` must not shift horizontally
- **White on black, always** — there is no colored text anywhere except the amber late badge

## 4. Component Stylings

### Buttons

**Capture Button (The Dual Shutter)**
- Shape: Circle, 76pt outer ring, 4pt white stroke, transparent center
- Inner: 64pt solid white disc on press
- Pressed: scale 0.92, brief white flash overlay on the viewfinder, paired with `.impactOccurred(.heavy)` haptic
- Triggers both cameras in sequence (~0.5s apart) — a second lighter haptic on the front-camera capture

**Primary Button ("Post" / "Send")**
- Background: `#FFFFFF`
- Text: `#000000` (intentional inversion for contrast on black)
- Padding: 14pt vertical, 32pt horizontal
- Corner radius: 500pt (full pill)
- Font: SF Pro Text 17pt weight 600
- Pressed: opacity 0.85, scale 0.97
- Used for: "Post", "Send", "Continue"

**Secondary Button ("Add a caption" / "Retake")**
- Background: transparent
- Text: `#FFFFFF`
- Border: 1pt solid `rgba(255,255,255,0.4)`
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: 500pt
- Font: SF Pro Text 16pt weight 600
- Pressed: border opacity → 1.0

**Camera Flip Icon Button**
- Size: 28pt glyph, 44pt hit area
- Default: `#FFFFFF` on transparent
- Tap: 180° flip animation on the viewfinder + light haptic
- Position: bottom-right of the capture screen

**RealMoji Add Button**
- Circular 44pt dashed-stroke white outline with a `+` glyph at center
- Tap: opens the RealMoji selfie picker (front camera live)

### Cards & Containers

**Dual-Lens Composite Card (THE signature)**
- Aspect: 3:4 portrait, full content width minus 0pt margins (edge-to-edge in feed)
- Back photo: full-bleed, corner radius 18pt
- Front selfie: 100pt × 134pt (3:4), corner radius 14pt, 2pt black inner stroke, default position top-left with 12pt inset
- Front selfie is **draggable** — long-press lifts it (scale 1.05 + shadow), drag snaps to any corner
- Card shadow: `rgba(0,0,0,0.6) 0 8px 24px` (registers on black via opacity)
- Tap front selfie: swaps which camera is primary (front becomes full-bleed) with a cross-fade

**Feed Post (Friends)**
- Header: 32pt circular avatar + username (17pt w600) + "⚠ 2h late" badge (amber) + ⋯
- The dual-lens composite card directly below the header, edge-to-edge
- Caption (16pt w400) one line below the card if present
- Location + relative time (13pt w400 `#8E8E93`) under the caption
- RealMojis strip: row of overlapping 30pt circular selfie-reactions, then the `+` add button
- "View N comments" link (14pt w400 `#8E8E93`)

**RealMoji Reaction Bubble**
- 30pt circular photo (a real selfie, not an emoji), 2pt black ring
- Overlap: each bubble overlaps the previous by 8pt (stacked cluster)
- Tap the cluster: opens the full reactions sheet (who reacted, with their selfie)
- No counts, no like numbers anywhere — reactions are faces, not metrics

**Memories Calendar Cell**
- Square grid cell, 4 columns, 2pt gap
- Each cell: that day's BeReal as a tiny dual-lens thumbnail (corner radius 6pt)
- Date label (11pt w600 white) bottom-left over a subtle scrim
- Tap: expands that day's BeReal full-screen

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `#000000` (opaque true black, no blur — the canvas continues)
- Tabs: Friends, Official, Search, Profile
- Icon size: 26pt
- Active: `#FFFFFF` filled glyph; Inactive: `rgba(255,255,255,0.45)` outline
- Labels: SF Pro Text 10pt w600 (or icon-only on some builds)
- Top hairline: `rgba(255,255,255,0.08)` 0.5pt

**Top Bar (Friends)**
- Height: 48pt + safe area
- Left: "Friends" / "My Friends" segmented toggle (text, 17pt w600, inactive at 45% opacity)
- Center: BeReal wordmark (lowercase, 18pt w700) on Capture entry
- Right: profile avatar (28pt circular)

**Capture Screen (Full Screen)**
- Edge-to-edge live viewfinder (back camera)
- Small live front-camera selfie inset top-left (same 100×134pt as the card)
- 2-minute countdown pill top-center: amber text on `rgba(0,0,0,0.5)` blur, tabular digits
- Capture button bottom-center (76pt dual shutter)
- Camera flip bottom-right, flash toggle bottom-left

### Input Fields

**Caption Field**
- Background: transparent (overlaid on the card or on black)
- Placeholder: "Add a caption…", 16pt w400 `#8E8E93`
- Text: `#FFFFFF` 16pt
- No border, no box — a bare cursor and text, single line, centered

**Comment Composer**
- Background: `#1C1C1E`
- Height: 44pt, corner radius 500pt (pill)
- Placeholder: "Add a comment…", 15pt w400 `#8E8E93`, 16pt horizontal inset
- Trailing: circular white "Send" button (32pt) appears once text is entered

### Distinctive Components

**2-Minute Countdown Banner**
- Full-width pill at the top of the friends feed when the daily notification has fired
- Background: `rgba(0,0,0,0.6)` with light blur, 1pt `rgba(255,215,10,0.4)` amber border
- Text: "⏰ 2 min left to capture a BeReal" — amber `#FFD60A`, tabular digits
- Counts down live; on expiry the banner says "Late — post anyway?" and the late badge logic engages

**Authenticity / Late Tag**
- Inline pill next to the username: "⚠ 2h late"
- Background: transparent; text + glyph in amber `#FFD60A`, 12pt w700
- Relative time recalculated from post timestamp vs the day's notification time

**Retake-Count Dots**
- A small "N retakes" pill under the card showing how many attempts the poster made
- Honesty signal — surfaced, not hidden
- 11pt w600 white on `rgba(255,255,255,0.12)` pill

**Behind-the-Scenes / BTS History**
- A small stacked-photos icon on a card if the poster recorded a BTS video
- Tap: plays the short behind-the-scenes clip in a full-screen modal

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 52, 76
- Standard margin: 16pt horizontal for text; the dual-lens card goes **edge-to-edge** (0pt)
- Feed post vertical rhythm: 24pt between posts

### Grid & Container
- Content width: full device width; card is full-bleed, text content inset 16pt
- Memories grid: 4-column square grid, 2pt gap (dense photographic mosaic)
- Profile grid: 3-column dual-lens thumbnails, 2pt gap

### Whitespace Philosophy
- **The photo owns the screen**: One BeReal per viewport in the feed — it is not a dense list
- **Minimal chrome**: Almost no padding or boxing; text floats directly on black
- **Dense only in the calendar**: Memories is intentionally tight to read as a film contact sheet

### Border Radius Scale
- Square (0pt): Almost nothing — BeReal avoids hard squares
- Tiny (6pt): Memories/profile grid thumbnails
- Soft (14pt): The front-camera selfie inset
- Card (18pt): The dual-lens composite card
- Comfortable (24pt): Bottom sheets, modals
- Full Pill (500pt): Primary/secondary buttons, the countdown banner, comment composer
- Circle (50%): Avatars, capture ring, RealMoji bubbles

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Text on canvas, tab bar, feed background |
| Card (Level 1) | `rgba(0,0,0,0.6) 0 8px 24px` | The dual-lens composite card lifts off black |
| Lifted Selfie (Level 2) | `rgba(0,0,0,0.7) 0 6px 18px` | Front-camera inset while being dragged (scale 1.05) |
| Sheet (Level 3) | `rgba(0,0,0,0.8) 0 -16px 48px` | RealMoji picker, comments sheet, share sheet |
| Blur Overlay | `rgba(0,0,0,0.5)` + backdrop-blur 30 | Countdown pill, full-screen viewers |

**Shadow Philosophy**: Because the canvas is pure black, shadows can't darken — they register through high opacity (0.6–0.8) and generous blur so the white card edges separate from the void. There are no colored glows, no soft ambient lighting. The only "elevation" that matters is the dual-lens card floating slightly above the black, and the selfie inset lifting when grabbed. Everything else is resolutely flat.

### Motion
- **Dual-shutter capture**: viewfinder white-flash 120ms + back-camera shutter, then ~0.5s later the front camera fires with a second lighter flash; two haptics (heavy then soft)
- **Camera flip**: 180° 3D rotation of the viewfinder over 0.35s, ease-in-out, light haptic
- **Selfie drag-snap**: long-press lifts (scale 1.0 → 1.05, 150ms spring), release snaps to nearest corner with a 0.3s spring (damping 0.75)
- **RealMoji pop**: a newly-added reaction scales 0 → 1.2 → 1.0 over 280ms
- **Late banner countdown**: digits roll with a subtle vertical slide each second (tabular, no layout shift)
- **Feed reveal**: friends' BeReals are blurred until you post yours — un-blur is a 0.4s fade once you capture

## 7. Do's and Don'ts

### Do
- Use pure black `#000000` as the canvas — the darkroom metaphor requires true black
- Keep the UI monochrome — white is the only accent; amber only for lateness
- Make the dual-lens composite the hero of every surface (feed, profile, memories)
- Keep the front-camera selfie draggable and corner-snapping
- Surface authenticity signals openly: "⚠ late", relative timestamps, retake counts
- Use RealMojis (real selfie photos) for reactions — never abstract emoji, never counts
- Render the 2-minute countdown with tabular numerals and amber text
- Pair the capture button with a heavy haptic and a second soft one for the front lens

### Don't
- Don't introduce a brand accent color — the lack of one *is* the brand
- Don't use warm-black (`#121212`) — BeReal is true `#000000`
- Don't add like counts, follower counts, or vanity metrics anywhere
- Don't use abstract emoji for reactions — only RealMoji selfie-photos
- Don't beautify with gradients, glows, or decorative shadows
- Don't hide the late badge or retake count — honesty is the product
- Don't add a light mode — it breaks the photographic darkroom feel
- Don't use a custom display font — system SF Pro only, no expressive type

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Selfie inset 88×118pt; card aspect held at 3:4 |
| iPhone 13/14/15 | 390pt | Standard 100×134pt selfie inset |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the top toggle row |
| iPhone 15/16 Pro Max | 430pt | Selfie inset 112×150pt; memories grid stays 4-col |
| iPad | 768pt+ | Feed centered at 480pt max-width; memories grid 6-col |

### Dynamic Type
- Usernames, captions, comments: full scale
- Countdown timer: fixed (tabular, layout-critical — never scales)
- Late badge / retake count: fixed 12pt / 11pt
- Tab labels: fixed 10pt

### Orientation
- All screens: **portrait-locked** — BeReal is a portrait-camera ritual
- Full-screen photo viewer: portrait only (the 3:4 composite assumes it)

### Touch Targets
- Capture button: 76pt — the largest, deliberately unmissable
- Selfie inset: full 100×134pt is draggable (long-press to lift)
- Tab icons: 26pt glyph, 44pt effective hit
- RealMoji `+`: 44pt

### Safe Area Handling
- Top: countdown pill and toggle row sit below the safe area / Dynamic Island
- Bottom: tab bar and capture button respect the home indicator
- Card: intentionally goes edge-to-edge horizontally (0pt side insets)

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#000000`
- Surface 1 (sheets): `#1C1C1E`
- Surface 2 (rows/chips): `#2C2C2E`
- Divider: `#2C2C2E`
- Primary text: `#FFFFFF`
- Secondary text: `#8E8E93`
- Tertiary text: `#636366`
- Accent (action): `#FFFFFF` (no brand hue)
- On-accent: `#000000`
- Late amber: `#FFD60A`
- Error red: `#FF453A`

### Example Component Prompts
- "Create a SwiftUI BeReal dual-lens composite card: 3:4 full-bleed back photo with 18pt corner radius, and a 100×134pt front-camera selfie inset (14pt radius, 2pt black stroke) pinned top-left with a 12pt inset. The selfie is long-press draggable and snaps to the nearest corner with a 0.3s spring. Card shadow rgba(0,0,0,0.6) 0 8px 24px."
- "Build the BeReal capture button: a 76pt circle with a 4pt white stroke and transparent center; on press it fills with a 64pt white disc, scales to 0.92, flashes the viewfinder white for 120ms, and fires a heavy haptic followed ~0.5s later by a soft haptic for the front camera."
- "Design the BeReal 2-minute countdown banner: a full-width pill at the top of the feed, background rgba(0,0,0,0.6) with light blur and a 1pt rgba(255,215,10,0.4) border, text '⏰ 2 min left to capture a BeReal' in amber #FFD60A using tabular numerals so digits don't shift while counting down."
- "Create a BeReal friends-feed post: 32pt circular avatar + username 'jordan' in SF Pro 17pt weight 600 + an amber '⚠ 2h late' badge + trailing ⋯. Below, the edge-to-edge dual-lens card. Then a one-line caption 16pt #FFFFFF, location + relative time 13pt #8E8E93, and a strip of overlapping 30pt circular RealMoji selfie-reactions ending with a dashed-outline + button. No like counts anywhere."
- "Build the BeReal RealMoji reaction cluster: a horizontal row of 30pt circular selfie photos each with a 2pt black ring, overlapping the previous by 8pt; tapping the cluster opens a sheet listing who reacted with their selfie. Never use abstract emoji and never show a numeric count."

### Iteration Guide
1. Canvas is pure black `#000000` — never warm-black
2. The UI is monochrome — white is the only accent; amber is reserved for lateness/urgency
3. The dual-lens composite card is the hero of every screen — keep it edge-to-edge with a draggable corner-snapping selfie
4. Reactions are RealMoji selfie-photos, not emoji, and there are no counts or vanity metrics
5. Authenticity is visible: show "⚠ late", relative timestamps, and retake-count dots — never hide them
6. Typography is system SF Pro at 400/600/700 — no custom font, no display sizes except the countdown
7. Shadows register via opacity (0.6–0.8) and blur, not darkening — no colored glows
8. Portrait-locked, dark-only — there is no light mode and no landscape
