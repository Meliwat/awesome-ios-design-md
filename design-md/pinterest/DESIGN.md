# Design System Inspiration of Pinterest (iOS)

## 1. Visual Theme & Atmosphere

Pinterest's iOS app is a content-first, photo-driven canvas where the interface all but disappears so user-generated imagery can do the talking. The signature feel is a staggered mosaic of images — the "masonry grid" — where every pin is a different height, every row breaks at a different vertical point, and scrolling feels more like flipping through a scrapbook than browsing a traditional feed. On light mode (the default), the canvas is pure white (`#FFFFFF`) so that pins — typically rich, saturated, oversaturated-on-purpose lifestyle photography — pop without any frame or chrome competing with them. The app removes every visual barrier between the user and the content: no borders on pins, no drop shadows by default, minimal text around the image, tiny icons.

The accent system is loud in one place and one place only: the Pinterest Red (`#E60023`). This is the color of the "P" in the logo, the "Save" button on every pin, and nothing else of consequence. It is the app's verb — Save. Pin. Commit. The rest of the UI is rendered in black (`#111111`), grays (`#767676`, `#E9E9E9`), and white. There are no secondary accent colors, no pastels, no gradients — because pins themselves contribute all the color the app needs. When you're in Dark mode, the canvas becomes `#121212` (a Google-Material-inspired warm dark rather than true black), the red stays identical, and text flips to white — but the philosophy is the same: the pin is the art, the chrome is the frame.

Typography is Pinterest Sans, a custom humanist geometric sans released in 2022 alongside the broader brand refresh. Before the custom typeface, Pinterest used Helvetica Neue and later a brand-adjacent Proxima / Source combination; Pinterest Sans now handles everything from micro-labels to splash-screen hero type. The hierarchy is compact and content-sensitive: sizes run 11–28pt, weights concentrate at 400 / 500 / 700, and no screen ever shows more than three type sizes at once. The largest expressive moment is the pin detail page title, where a 28pt weight 700 sits directly under the hero pin image — otherwise, the app keeps type modest because images are loud.

**Key Characteristics:**
- Bright white canvas (`#FFFFFF`) in light mode, warm dark canvas (`#121212`) in dark mode
- **Masonry grid** — staggered 2-column layout with variable-height pins, the app's single biggest signature move
- Pinterest Red (`#E60023`) reserved for the Save button, "P" logo, and nothing else
- Pins render edge-to-edge with minimal chrome — no borders, no drop shadows on the tile itself
- Pinterest Sans (2022 proprietary) — 400 / 500 / 700 weights, plus a legacy SF Pro fallback
- Floating search bar at the top of Home — a rounded-pill input that sits over the feed
- Pin detail expands full-screen with an "More like this" infinite-scroll feed below
- Board covers use a 2×2 collage of recent pins
- Idea Pins (story-style) ship with a dedicated vertical-video player experience
- Bottom tab bar: Home, Search, Create, Notifications, Profile — always 5 tabs, centered Create `+`
- Haptics on save, long-press context menu, board switch, follow

## 2. Color Palette & Roles

### Primary
- **Pinterest Red** (`#E60023`): Primary CTA, Save button on every pin, the "P" of the logomark, unread notification dot, active state indicators on specific toggles. This is the only brand accent — it appears in very few places by design, which is what gives it weight.
- **Pinterest Red Pressed** (`#AD081B`): Pressed / active-state variant for the Save button and other red CTAs.
- **Red Hover / Focus** (`#CC0020`): Slight deepening for mouse-over or keyboard-focus states when rendered on web; matched on iOS for pressed state alternative.

### Canvas & Surfaces (Light Mode — default)
- **Canvas White** (`#FFFFFF`): Primary canvas. Also the compose sheet and default nav background.
- **Surface 1** (`#F8F8F8`): Very subtle section backing, board cover interior on lighter themes.
- **Divider / Hairline** (`#E9E9E9`): 1pt list separators, top-border of the tab bar, dividers on settings list.
- **Input Background** (`#EFEFEF`): Search bar fill, comment compose fill.
- **Skeleton / Shimmer** (`#F2F2F2` → `#E5E5E5`): Placeholder blocks while images load.

### Canvas & Surfaces (Dark Mode)
- **Canvas Dark** (`#121212`): Primary dark canvas — same Material-influenced warm dark that Spotify uses.
- **Surface 1 Dark** (`#1E1E1E`): Sheet backgrounds, raised cards, board covers.
- **Surface 2 Dark** (`#2A2A2A`): Input fields, pressed row backgrounds.
- **Divider Dark** (`#2E2E2E`): Hairlines and dividers on dark surfaces.

### Text (Light Mode)
- **Text Primary** (`#111111`): Pin titles, creator names, primary screen headlines — intentionally a hair off pure black for softness.
- **Text Secondary** (`#767676`): Metadata, reactions count, description secondary line.
- **Text Tertiary** (`#B5B5B5`): Timestamps, placeholder text, "1 of 4 ideas" counters.
- **Text Disabled** (`#D9D9D9`): Disabled button text, grayed-out labels.

### Text (Dark Mode)
- **Text Primary Dark** (`#FFFFFF`): Main text on dark canvas.
- **Text Secondary Dark** (`#AAAAAA`): Metadata on dark.
- **Text Tertiary Dark** (`#6E6E6E`): Lowest-emphasis labels on dark.

### Semantic
- **Success Green** (`#008A3C`): Save confirmation toast, "Pinned to board" success state. Rarely used — almost everything uses red.
- **Error Red** (`#E60023`): Same token as the brand red for error states.
- **Info Blue** (`#0074E8`): Used very sparingly for links and "Learn more" inline text.
- **Board Accent (dynamic)**: Each board can display a user-chosen accent color for its cover; the app derives a tint from the top pin on the board if one isn't set.

### Content Accent: Per-Pin Dominant Color
- Pins sometimes render with a dynamically extracted dominant color as the background behind the pin image during load (skeleton state), transitioning to `#FFFFFF` once the image appears.
- This is not used as a persistent theme like Spotify's Now Playing gradient — it's a load-state courtesy.

## 3. Typography Rules

### Font Family
- **Primary (2022+)**: `Pinterest Sans` (proprietary, released alongside 2022 brand refresh — five weights)
- **Pre-2022 legacy**: Helvetica Neue and later Proxima-adjacent custom stacks — still visible on older marketing surfaces
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Helvetica Neue', Helvetica, Arial, sans-serif`
- **CJK / Non-Latin**: System fonts — PingFang SC (Chinese), Hiragino Sans (Japanese), Apple SD Gothic Neo (Korean), Noto Sans for extended Arabic, Hebrew, Devanagari, Thai, Cyrillic
- Pinterest Sans is optimized for image-adjacent type — slightly tighter tracking than SF Pro, softer terminals, warmer feel

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Pin Detail Title | Pinterest Sans | 28pt | 700 | 1.2 | -0.3pt | Headline on pin detail screen |
| Screen Title (Large) | Pinterest Sans | 24pt | 700 | 1.2 | -0.2pt | Profile display name, board title hero |
| Section Header | Pinterest Sans | 20pt | 700 | 1.25 | -0.1pt | "Your boards", "Created by you" |
| Board Name | Pinterest Sans | 17pt | 700 | 1.25 | -0.1pt | Board tile caption |
| Pin Title | Pinterest Sans | 14pt | 500 | 1.3 | 0pt | Pin caption below masonry tile |
| Username | Pinterest Sans | 14pt | 600 | 1.3 | 0pt | Creator name on pins, boards, comments |
| Body | Pinterest Sans | 15pt | 400 | 1.45 | 0pt | Pin description, board description |
| Comment | Pinterest Sans | 14pt | 400 | 1.4 | 0pt | Comment text on pin detail |
| Meta / Timestamp | Pinterest Sans | 12pt | 400 | 1.3 | 0pt | "3 days ago", "22 pins" |
| Button (Primary) | Pinterest Sans | 16pt | 700 | 1.0 | 0pt | Red Save button, Follow pill |
| Button (Secondary) | Pinterest Sans | 14pt | 600 | 1.0 | 0pt | Outline / transparent buttons |
| Tab Label | Pinterest Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels (when shown) |
| Micro Label | Pinterest Sans | 11pt | 700 | 1.2 | 0.4pt | "IDEA PIN" / "VIDEO" badges, all caps |

### Principles
- **Weights concentrated at 400 / 500 / 600 / 700**: Regular, medium (for pin titles), semibold (for usernames), bold (for headlines and CTAs)
- **No thin, no light, no black**: The font ships with those weights but the product never uses them
- **Negative letter-spacing reserved for 20pt+**: Anything under 20pt sits at 0pt tracking; titles tighten to -0.1 through -0.3pt
- **Line heights lean readable**: Pinterest is heavy on descriptions and text below pins; body sits at 1.4–1.45 where most apps use 1.3
- **Dynamic Type supported on text-heavy views**: Descriptions, comments, and settings respect user preference; pin title captions are fixed size because masonry layout relies on predictable heights
- **No all-caps except micro-labels**: The 11pt `MICRO LABEL` treatment is the only place all-caps appears

## 4. Component Stylings

### Buttons

**Save Button (The Iconic Red Pill)**
- Shape: Pill, 40pt height, width fits content (typically 64–72pt wide for "Save")
- Background: `#E60023`
- Text: `#FFFFFF`, Pinterest Sans 16pt weight 700
- Padding: 10pt vertical, 20pt horizontal
- Corner radius: 500pt (full pill)
- Pressed: background `#AD081B`, scale 0.97, medium haptic
- State variant: "Saved" — background becomes `#111111` (black), text stays white, retains pill shape. This color flip is the moment the user knows the pin is committed.
- Position on every pin: upper-right corner of the image on hover / long-press, and as a persistent overlay button in the corner of the pin when in "Save mode"

**Primary Pill (Follow / Sign up)**
- Background: `#E60023`
- Text: `#FFFFFF`, Pinterest Sans 16pt weight 700
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: 500pt
- Pressed: `#AD081B`, scale 0.97
- Used for: "Follow", "Continue", "Sign up"

**Secondary Pill (Share / Follow on)**
- Background: `#EFEFEF` (light mode) / `#2A2A2A` (dark)
- Text: `#111111` light / `#FFFFFF` dark, 16pt weight 600
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: 500pt
- Pressed: background `#E0E0E0` / `#3A3A3A`

**Icon Button (Overflow / Share / Message)**
- Glyph size: 24pt
- Hit area: 44pt × 44pt
- Default: `#111111` (light) / `#FFFFFF` (dark)
- Pressed: background `rgba(0,0,0,0.05)` light / `rgba(255,255,255,0.1)` dark (slight wash, no full button look)

**Text Button ("See all", "More")**
- Font: Pinterest Sans 14pt weight 700
- Color: `#111111` (light) / `#FFFFFF` (dark)
- No underline, no background; 44pt hit area

### Cards & Containers

**Pin Tile (Masonry Grid — THE signature component)**
- Width: (screen width − 16pt horizontal margin − 8pt inter-column gap) ÷ 2 — so ≈ 183pt on a 390pt iPhone
- Height: **variable** — matches the aspect ratio of the original pin image. A single column can have a 200pt-tall pin followed by a 380pt-tall pin, because each pin renders at its native aspect.
- Structure (top to bottom):
  1. Pin image — edge-to-edge within the tile, corner radius 16pt, no shadow, no border
  2. Optional badge top-left (11pt uppercase micro-label on a semi-transparent black chip for "VIDEO", "IDEA PIN")
  3. Save button top-right overlay (40pt pill, appears on long-press or always for pinboard browsing)
  4. Below image: 4pt gap, then pin title (14pt weight 500, 2-line clamp), 2pt gap, then creator row (20pt circle avatar + username 12pt weight 400 secondary)
- Tile gap: 8pt vertical between pins in the same column; 8pt horizontal between columns
- Tile background: transparent (canvas shows through)
- Corner radius on image: 16pt — larger than most apps; softens the grid
- Tap: full-bleed fade-through transition to pin detail (described below)

**Pin Detail Hero**
- Image renders at its native aspect, full-width with 16pt horizontal margin, corner radius 16pt
- Overlaid at the top-right is the Save button (pill, always visible)
- Below the image:
  - Creator row: 44pt circle avatar + display name (17pt weight 700) + "Follow" pill trailing
  - Pin title (28pt weight 700, multi-line OK)
  - Pin description (15pt weight 400, 1.45 line height)
  - Tag chips — horizontal scroll, pill chips at 30pt height
  - "Comments" header + comments list
- Below: **"More like this"** infinite-scroll masonry of related pins — this is Pinterest's trademark infinite-loop experience

**Board Cover Tile**
- Square 1:1 tile (typical size 180pt)
- Structure: 2×2 collage of the 4 most-recent pins on the board — top-left is the largest (takes up the left half), top-right + bottom-right are smaller stacked, bottom-left fills remaining (sometimes 3-pin composition instead)
- Board name below: 17pt weight 700, 1-line with ellipsis
- Pin count: 12pt weight 400 secondary
- Corner radius: 16pt (matches pin tiles)
- Tap: scale 0.96 with soft haptic, opens board screen

**Idea Pin Card (Story-style)**
- Full-viewport 9:16 vertical player, similar to TikTok / Reels but Pinterest-branded
- Overlays:
  - Top: progress bar segments (one thin bar per slide, `#FFFFFF` fills, `rgba(255,255,255,0.3)` track)
  - Bottom-left: creator avatar + name + "Follow" pill
  - Bottom-right: React (heart SF Symbol), comment, share, save (red pill) — stacked vertically
  - Caption / tag row: sits above the bottom UI, 15pt weight 400 white with text shadow
- Transitions between slides: spring-tap (tap right to advance, tap left to go back, swipe up to see details)

**Comment Row**
- 36pt circular avatar leading
- Username (14pt weight 600) + comment body (14pt weight 400) stacked
- Reaction row (heart icon + count) at 24pt trailing, or below text on longer comments
- Vertical padding 12pt; divider hairline below

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` (light) / `#121212` (dark) with 1pt top hairline `#E9E9E9` / `#2E2E2E`
- 5 tabs in order: **Home** (house icon), **Search** (magnifying glass), **Create** (centered `+` button, slightly larger, red background `#E60023`), **Notifications** (bell icon), **Profile** (user circle or user avatar)
- Icon size: 26pt glyph, 44pt hit area
- Active: `#111111` light / `#FFFFFF` dark (filled SF Symbol variant)
- Inactive: `#767676` light / `#AAAAAA` dark (outlined variant)
- Labels: hidden by default — Pinterest uses icon-only tab bar on iOS for most of its recent history
- Create button (center): 48pt × 48pt rounded-rect with 14pt corner radius, background `#E60023`, white `+` glyph — stands out visually from the other four icons

**Top Floating Search Bar (Home)**
- Position: floats above the masonry feed, 12pt from top safe area, 16pt from sides
- Height: 48pt
- Background: `#FFFFFF` with soft shadow `rgba(0,0,0,0.08) 0 2px 8px` (light) / `#1E1E1E` with no shadow (dark)
- Corner radius: 500pt (full pill)
- Leading: 20pt `magnifyingglass` SF Symbol in `#111111` / `#FFFFFF`
- Placeholder: "Search for ideas" 16pt weight 400 `#767676`
- On scroll down: the bar slides up out of view; on scroll up: slides back in (typical Material-style scroll-hide behavior)

**Top Nav (Board / Pin Detail / Settings)**
- Height: 44pt + safe area
- Back chevron 24pt leading, title 17pt weight 700 center (multi-word boards allow truncation), overflow `ellipsis` 24pt trailing
- Background: matches canvas; no divider unless content scrolls under

**Tab Row (Profile Screen)**
- Sticky below profile header when scrolling
- Two or three tabs: "Created", "Saved", sometimes "More ideas"
- Active: `#111111` text 17pt weight 700, 2pt underline bar `#111111`
- Inactive: `#767676` 17pt weight 500

### Input Fields

**Search Bar (Search Tab)**
- Background: `#EFEFEF` light / `#2A2A2A` dark
- Height: 44pt
- Corner radius: 500pt (full pill)
- Leading `magnifyingglass` 18pt `#767676`
- Placeholder: "Search for ideas" 16pt weight 400 `#767676`
- Focus: no border change; keyboard comes up, "Cancel" text button appears trailing 14pt weight 600 black

**Comment Compose**
- Background: `#FFFFFF` with 1pt top border `#E9E9E9`
- Height: 48pt when collapsed, expands with content
- Placeholder: "Add a comment" 14pt weight 400 `#767676`
- Trailing: "Post" text button, grayed until input is non-empty, turns `#111111` weight 700 when ready

**Board Picker Sheet**
- Bottom sheet, 12pt top-corner radius
- List of boards (row = 56pt tall, 40pt square board cover + board name + pin count)
- "Create board" pinned at top with `+` icon
- Search within boards at the top

### Distinctive Components

**The Masonry Grid**
- 2-column variable-height layout is the single most-signature Pinterest UI feature
- Each column is independent: pins "fall" into the shorter column like Tetris, creating the staggered look
- Pins render at their native aspect ratio — no cropping, no forced uniform height
- 8pt gap between columns and between pins in a column
- **Critical implementation note**: heights must be computed from image aspect ratio before rendering — skeleton placeholders during load respect the eventual aspect so the grid doesn't jump

**Save Button Overlay**
- Always-accessible red pill in the top-right corner of every pin
- On tap: red pill morphs to black "Saved" pill (200ms spring), success haptic fires, brief toast "Saved to {board name}" fades in from the bottom
- On long-press: opens board picker sheet for choosing destination

**Pin Detail Pinch-to-Zoom**
- Pin image supports pinch-to-zoom gesture, image scales up in place with inertia
- Release snaps back to full-width with spring

**Shop the Look Tags**
- Certain pins have small white circular tags (20pt diameter) placed over product locations in the image
- Tap the tag: opens a product card with price, brand, "Buy" button
- Tags pulse subtly every 3 seconds to hint interactivity

**"More like this" Infinite Feed**
- Below the comments on every pin detail, a masonry feed of visually-similar pins streams in infinitely
- Scrolling deep never ends — Pinterest's visual-search-driven recommendation is fundamental to the app
- Each pin here is tappable and starts the experience over, creating a rabbit-hole navigation pattern

**Idea Pin Player**
- Vertical story-style player for multi-slide pins (similar to Instagram Stories but curated and Pinterest-themed)
- Progress bar segments at top, content (image/video/text slide) fills the rest
- Tap right = next slide, tap left = previous, swipe up = see details, swipe down = dismiss
- Overlay controls: creator, save, react — bottom of screen

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56, 72
- Masonry grid gap: 8pt both directions (tight, image-focused)
- Pin detail content margin: 16pt horizontal
- Section vertical rhythm: 32pt between major sections (pin detail → comments → more like this)

### Grid & Container
- Masonry: 2 columns on all iPhone widths; 3 columns on iPad portrait, 4 on iPad landscape
- Pin detail: single-column, 16pt margin
- Board cover grid (profile): 2 columns with 8pt gap
- Settings / list content: full-width rows with 16pt horizontal inset

### Whitespace Philosophy
- **Tight between pins**: 8pt gaps keep the mosaic feel — the feed is image-dense on purpose
- **Generous in reading views**: Pin detail, comments, and settings use 16pt+ insets and 1.45 line height for body copy
- **Large image corner radii**: 16pt is a lot — this is the app's softening move, applied only to images (not buttons, not chips)

### Border Radius Scale
- Square (0pt): Nothing — Pinterest never uses a hard corner
- Soft (4pt): Rarely used
- Standard (8pt): Some inline tags and status badges
- Pronounced (12pt): Bottom sheets top corners
- Signature (16pt): Pin images, board covers — the app's most-recognized corner
- Full Pill (500pt): Save button, Follow, Search bar, filter chips
- Circle (50%): Avatars, create button icon, shop-the-look tags

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Pin tiles in the feed, body text, most screens |
| Floating Search (Level 1) | `rgba(0,0,0,0.08) 0 2px 8px` | Floating search pill over the feed in light mode |
| Board Cover Lift (Level 2) | `rgba(0,0,0,0.1) 0 4px 16px` | Board cover on hover-state equivalent, create flow previews |
| Sheet (Level 3) | `rgba(0,0,0,0.25) 0 -12px 40px` | Bottom sheets — board picker, share |
| Modal Dim | `rgba(0,0,0,0.6)` + backdrop-blur 20 | Context menu dimming |
| Toast (Level 4) | `rgba(0,0,0,0.2) 0 6px 24px` | "Saved to {board}" toast slides up |

**Shadow Philosophy**: Pinterest is mostly a flat app. Shadows exist in two places: (1) the floating search bar in light mode, which needs a subtle lift so it reads as a floating element over the feed, and (2) bottom sheets, where shadow communicates layering. Pin tiles themselves never carry shadow — the masonry grid looks cleanest when every pin sits flush on the canvas. In dark mode, shadows are even more restrained because the darker canvas already provides depth through surface-color steps (`#121212` → `#1E1E1E` → `#2A2A2A`).

### Motion
- **Save button morph**: Red pill → black "Saved" pill in 200ms spring (damping 0.8), success haptic fires on state change
- **Pin tap → detail**: Shared-element transition — the tapped pin image scales up from its grid position to the pin detail hero, 0.35s spring, damping 0.85
- **Masonry entrance**: New pins fade-up 8pt with 50ms stagger per column as they load into the feed
- **Idea Pin advance**: Tap-right advances a slide with 0.25s ease-out
- **Pull-to-refresh**: 28pt red spinner (the "P" logomark) rotates while loading
- **Long-press pin**: Scale 0.96 + haptic, context menu springs in 0.3s
- **Follow button**: Red pill → gray "Following" with 0.25s spring cross-fade, success haptic

## 7. Do's and Don'ts

### Do
- Use `#FFFFFF` as light-mode canvas — stark white, not warm-tinted
- Use `#121212` as dark-mode canvas — warm dark, not pure black
- Reserve Pinterest Red (`#E60023`) for the Save button, primary CTAs, and the logomark — that's it
- Render pins at their native aspect ratio in the masonry grid — never force a uniform height
- Use 16pt corner radius on pin images and board covers — softer than most apps, and intentional
- Always show the Save button on a pin — it's the app's core verb
- Use Pinterest Sans at 400 / 500 / 700 weights only; 500 is the weight for pin titles
- Keep the masonry gap tight at 8pt both directions — pins should feel like a mosaic
- Use the floating search pill at the top of Home in light mode with a soft shadow
- Morph the Save button to black "Saved" when committed — this is the success signal
- Show "More like this" below every pin detail with infinite scroll
- Use icon-only tab bar (no labels) on iOS — Pinterest's house style

### Don't
- Don't put shadows on pin tiles — the feed looks cleanest flat
- Don't force pins to uniform height — masonry is broken the moment you do
- Don't use red for anything other than the Save button / CTAs / brand — no red text, no red borders, no red accents on metadata
- Don't use tiny corner radii (4pt or less) on images — 16pt is the minimum
- Don't use pure black `#000000` for dark-mode canvas — `#121212` is warmer and reduces eye strain
- Don't use display-size type for pin captions — 14pt weight 500 is the limit
- Don't add gradients, glossy effects, or skeuomorphism — Pinterest is flat and photographic
- Don't animate aggressively — springs are subtle (0.2–0.35s range)
- Don't cover pin images with persistent UI chrome — the Save button is the only permanent overlay
- Don't break the 2-column masonry on iPhone — even on Pro Max, it stays 2 columns (wider tiles, not 3)

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Masonry tile ~176pt wide, pin title caption slightly tighter |
| iPhone 13/14/15 | 390pt | Standard masonry tile ~183pt wide |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated at top of floating search |
| iPhone 15/16 Pro Max | 430pt | Masonry tile ~203pt wide, headlines bump +1pt |
| iPad | 768pt+ | 3-column masonry portrait, 4-column landscape; pin detail max 640pt wide centered |

### Dynamic Type
- Pin detail title and body: full scale
- Pin caption title in masonry feed: **fixed size** (layout-sensitive — affects tile height)
- Comments and descriptions: full scale
- Tab labels: hidden, so N/A
- Save button text: fixed size (pill geometry)

### Orientation
- Feed / Search / Profile / Create: **portrait-locked**
- Pin detail with a video or Idea Pin: rotates to landscape for video playback
- Idea Pin player: portrait-locked

### Touch Targets
- Save button: 40pt pill — exceeds 44pt minimum via hit area expansion
- Pin tile: full tile tappable (variable height, 44pt minimum enforced by tile overall size)
- Icon buttons: 44pt hit area
- Tab bar icons: 26pt glyph with 44pt hit area
- Create button (center tab): 48pt × 48pt

### Safe Area Handling
- Top: floating search respects top safe area (12pt inset below Dynamic Island)
- Bottom: tab bar respects home indicator
- Idea Pin player: ignores safe area for video content, respects it for overlays
- Sides: 16pt content insets on non-masonry screens; masonry uses full-width − 16pt outer margin − 8pt column gap

## 9. Agent Prompt Guide

### Quick Color Reference
- Light canvas: `#FFFFFF`
- Dark canvas: `#121212`
- Surface 1 (light): `#F8F8F8`
- Surface 1 (dark): `#1E1E1E`
- Input fill (light): `#EFEFEF`
- Input fill (dark): `#2A2A2A`
- Text primary (light): `#111111`
- Text primary (dark): `#FFFFFF`
- Text secondary (light): `#767676`
- Text secondary (dark): `#AAAAAA`
- Pinterest Red: `#E60023`
- Red pressed: `#AD081B`
- Divider: `#E9E9E9` (light) / `#2E2E2E` (dark)

### Example Component Prompts
- "Create a SwiftUI Pinterest Save button: 40pt tall pill, background `#E60023`, text 'Save' in Pinterest Sans 16pt weight 700 `#FFFFFF`. 10pt vertical / 20pt horizontal padding, 500pt corner radius (full pill). Pressed: background `#AD081B`, scale 0.97 with medium haptic. On save success: morph to black `#111111` background with text 'Saved', 200ms spring transition, success haptic."
- "Build the Pinterest masonry grid: 2-column layout, each column independent (pins fall into shorter column like Tetris). Pin tile: full column width, height = image aspect ratio × width. Image renders with 16pt corner radius, no shadow, no border. Below image: 4pt gap, pin title Pinterest Sans 14pt weight 500 (2-line clamp), 2pt gap, creator row with 20pt avatar + username 12pt weight 400 `#767676`. 8pt gap between tiles vertically and between columns horizontally."
- "Design the Pinterest floating search bar: pill at top of Home feed, 48pt tall, 16pt from sides, 12pt below top safe area. Background `#FFFFFF` (light) with shadow `rgba(0,0,0,0.08) 0 2px 8px`, or `#1E1E1E` (dark) no shadow. 500pt corner radius. Leading magnifyingglass SF Symbol 20pt `#111111`, placeholder 'Search for ideas' 16pt weight 400 `#767676`. Slides up out of view on scroll down, back on scroll up."
- "Create a pin detail screen: image at top full-width with 16pt horizontal margin and 16pt corner radius, Save button pill overlay top-right. Below: 44pt circle avatar + creator display name 17pt weight 700 + Follow red pill trailing. Then pin title Pinterest Sans 28pt weight 700, then description 15pt weight 400 `#111111` line-height 1.45. Comments section below. At bottom: 'More like this' masonry grid infinite feed of related pins."
- "Build the Pinterest bottom tab bar: 56pt + safe area, 5 tabs (Home, Search, Create, Notifications, Profile). Create tab in the center is 48pt × 48pt rounded-rect with 14pt corner radius, background `#E60023`, white `+` glyph — stands out from the other four outlined icons. All other tabs: 26pt SF Symbol, active `#111111` filled variant, inactive `#767676` outlined. No labels shown. 1pt top hairline `#E9E9E9`."
- "Design an Idea Pin player: full-viewport 9:16 vertical slide stack. Top overlay: progress bar segments (one thin white bar per slide, `rgba(255,255,255,0.3)` track, `#FFFFFF` fill). Bottom-left: 32pt avatar + creator name 14pt weight 600 white + Follow red pill. Bottom-right: stacked react (heart), comment, share, Save (red pill) icons. Tap right advances slide, tap left goes back, swipe up for details."

### Iteration Guide
1. Canvas in light mode is pure white `#FFFFFF` — the app is photo-centric, so the canvas should disappear behind imagery
2. Pinterest Red `#E60023` is reserved for the Save button, primary CTAs, and the logomark — never for text, borders, or generic accents
3. Masonry grid is 2-column with variable height — this is the single most important layout signature; do not normalize heights
4. Pin images use 16pt corner radius — larger than most apps; this is the softening touch
5. Typography is Pinterest Sans at 400 / 500 / 700 (500 is the weight for pin title captions)
6. Save button morphs to black "Saved" on success — this is the feedback moment
7. No shadows on pin tiles — the feed stays flat; shadows only on floating search and sheets
8. Dark mode canvas is `#121212`, not true black — Material-style warm dark
9. Create button (center tab) uses the brand red background to stand out from the other tabs
10. "More like this" infinite feed below every pin detail is load-bearing UX — always show it
