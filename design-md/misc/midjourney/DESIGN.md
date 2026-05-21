# Design System Inspiration of Midjourney (iOS)

## 1. Visual Theme & Atmosphere

Midjourney's iOS app is a gallery first and a tool second. The canvas is **true black** (`#000000`) — not a warm charcoal, not an off-black — because the entire purpose of the screen is to make generated imagery glow on OLED. Every other surface is a barely-lifted near-black (`#0E0E0E`, `#1A1A1A`), so the only saturated color anywhere in the interface is the user's own renders. Open the app and you land on the **Create feed**: a prompt bar pinned at the bottom (or top, per layout), and below it a vertical stream of "jobs" — each job rendered as a tight **2×2 image grid** with a one-line prompt above it and a compact action row below. The grid is the hero; the chrome recedes.

This is a deliberate inversion of normal product design. Midjourney has **no brand accent color**. There is no "Spotify Green", no "Coinbase Blue" driving CTAs. White (`#FFFFFF`) is the only "primary" — it's the fill of the Imagine button, the active tab glyph, the U1–U4 upscale chips. Everything else is a grey ramp from `#FFFFFF` text down through `#A1A1A1` secondary to `#6E6E6E` tertiary. The result is a near-monochrome shell that disappears around full-color photography, exactly like a darkroom or a photographer's contact sheet. The product's roots in a Discord bot still show: the Discord blurple (`#4D5BCE`) survives as a connect/account accent, and the U1/U2/V1/V2 button grammar is lifted verbatim from the bot's reaction buttons.

The signature interaction is the **prompt → job → grid → upscale** loop. You type a natural-language prompt (optionally with `--ar`, `--v`, `--stylize`, `--style raw` parameters appended) into the prompt bar, tap **Imagine**, and a job appears at the top of the feed — first as a progressing blur, then resolving into four images. Below each grid is a row of monospace action chips: **U1 U2 U3 U4** (upscale that quadrant) and **V1 V2 V3 V4** (create variations), plus a re-roll (🔄). This grammar — parameters as flags, results as a 4-up grid, refinement as indexed chips — is Midjourney's entire identity and must be reproduced faithfully.

Typography is split-personality. The **UI** (titles, tabs, metadata) uses a clean grotesque sans — Inter on the web/app fallback — kept deliberately quiet. But **prompts and parameters** render in a **monospace** face, because a prompt is treated as code: `a glass owl, studio light --ar 3:2 --v 6 --stylize 250`. This monospace-for-prompts convention is a load-bearing brand signal — it tells the user "this string is a precise instruction, not prose."

**Key Characteristics:**
- True black canvas (`#000000`) — OLED black so imagery glows; dark-only, no light mode
- No brand accent — white (`#FFFFFF`) is the only "primary"; chrome is a grey ramp `#FFFFFF → #A1A1A1 → #6E6E6E`
- 2×2 image grid is the hero unit — every job renders as a 4-up quad
- Prompt bar — natural language + `--flag` parameters; tap Imagine to submit
- U1–U4 / V1–V4 chip grammar — monospace indexed chips for upscale and variation (Discord-bot heritage)
- Monospace for prompts & parameters — the prompt is treated as code, not prose
- Job feed — vertical stream of prompt + grid + action row, newest on top
- Discord blurple (`#4D5BCE`) survives as a connect/account accent only
- Progress blur — jobs resolve from a blurred placeholder to four sharp images
- Minimal chrome: bottom tab bar (Create / Explore / Search / Library / Profile), thin segmented control (Imagine / Edit / Personalize)

## 2. Color Palette & Roles

### Primary (Interactive)
- **White** (`#FFFFFF`): The only primary — Imagine button fill, active tab glyph, upscale chips (U1–U4), primary text.
- **White Pressed** (`#DADADA`): Pressed state of white-filled buttons.
- **Rate / Link Blue** (`#2D7FF9`): Hyperlinks, "Rate images" reward prompts, in-app links.
- **Discord Blurple** (`#4D5BCE`): "Connect Discord" / account-linking accent only — a heritage color, never a general CTA.

### Canvas & Surfaces (Dark — the only mode)
- **True Black Canvas** (`#000000`): Primary canvas. OLED black; behind every screen.
- **Surface 1** (`#0E0E0E`): Cards, sheets, the prompt bar's resting backdrop, token sections.
- **Surface 2** (`#1A1A1A`): Prompt input fill, ghost button background, raised rows.
- **Surface 3** (`#242424`): Pressed surface, hovered chips, scrubber tracks.
- **Divider** (`#2A2A2A`): Hairline dividers, chip borders, input borders.

### Text
- **Text Primary** (`#FFFFFF`): Titles, prompt text, active labels.
- **Text Secondary** (`#A1A1A1`): Metadata (image count, mode, elapsed time), inactive nav, prompt sub-lines.
- **Text Tertiary** (`#6E6E6E`): Placeholder text, disabled state, parameter hints, inactive tab labels.

### Semantic
- **Success Green** (`#2ECC71`): Job-complete tick, "Saved to library" toast.
- **Warning Amber** (`#F5A623`): "Slow / Relaxed queue" indicator, quota warnings.
- **Error Red** (`#E5484D`): Failed job, banned-prompt warning, network error.
- **Progress Shimmer** (`#1A1A1A → #2A2A2A`): Animated blur/shimmer on a rendering job before images resolve.

### Image Grid (content, not chrome)
The 2×2 grid carries 100% of the screen's saturation. The shell defines no decorative color — whatever the model generates is the palette. Treat generated imagery as full-bleed, full-color, and never tint or overlay it except for a 1px `#2A2A2A` seam between the four cells.

### Color Usage Rules
| Token | Hex | Where |
|-------|-----|-------|
| Canvas | `#000000` | Behind everything (OLED black) |
| Surface 1 | `#0E0E0E` | Cards, sheets, sections |
| Surface 2 | `#1A1A1A` | Prompt input, ghost buttons |
| Divider | `#2A2A2A` | Hairlines, chip & input borders |
| Primary (white) | `#FFFFFF` | Imagine button, active tab, U-chips, titles |
| Text secondary | `#A1A1A1` | Metadata, inactive nav |
| Text tertiary | `#6E6E6E` | Placeholders, disabled |
| Rate blue | `#2D7FF9` | Links, rate-images reward |
| Blurple | `#4D5BCE` | Discord connect only |
| Success | `#2ECC71` | Job complete |
| Error | `#E5484D` | Failed job |

## 3. Typography Rules

### Font Family
- **UI Sans**: `Inter` (by Rasmus Andersson, SIL OFL) — the closest open grotesque to Midjourney's clean app UI face. Used for titles, tabs, metadata, body. On iOS, falls back to SF Pro Text / SF Pro Display.
- **Prompt / Parameter Mono**: `SF Mono` on iOS (system monospace); `IBM Plex Mono` or `JetBrains Mono` as a bundled alternative. **Prompts and `--flags` always render monospace** — the prompt is treated as code.
- **Fallback Stack (sans)**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Fallback Stack (mono)**: `'SF Mono', ui-monospace, Menlo, 'JetBrains Mono', monospace`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Inter | 32pt | 800 | 1.15 | -0.4pt | "Create", large-title nav |
| Nav Title (compact) | Inter | 22pt | 700 | 1.2 | -0.3pt | Collapsed top bar / "Explore" |
| Section Heading | Inter | 19pt | 700 | 1.25 | -0.2pt | "Recent jobs", sheet titles |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Descriptions, settings rows |
| Action / Tab Active | Inter | 15pt | 600 | 1.0 | 0pt | Segmented control, primary action |
| Card Title | Inter | 15pt | 600 | 1.3 | 0pt | Library / album titles |
| Prompt Text | SF Mono | 13pt | 400 | 1.4 | 0pt | The prompt above each grid |
| Parameter Chip | SF Mono | 13pt | 600 | 1.0 | 0pt | `--ar 1:1`, `--stylize 250` |
| Job Chip (U1/V1) | SF Mono | 13pt | 600 | 1.0 | 0.2pt | Upscale / variation indexed chips |
| Metadata | Inter | 14pt | 400 | 1.4 | 0pt | "Relaxed · 4 images · 38s" |
| Caption | Inter | 12pt | 500 | 1.3 | 0pt | Timestamps, small labels |
| Tab Label | Inter | 10pt | 500 | 1.0 | 0.1pt | Bottom tab text |
| Placeholder | Inter | 14pt | 400 | 1.4 | 0pt | "What will you imagine?" |

### Principles
- **Two voices, one rule**: UI is quiet sans; prompts and parameters are monospace. Never render a prompt in the sans face — monospace *is* the signal that a string is an instruction.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: Regular body, medium captions, semibold actions, bold headings, extrabold large title. No thin, no light.
- **Restraint over expression**: The type system is intentionally generic so it never competes with imagery. Brand personality lives in the grid and the `--flag` grammar, not the typeface.
- **Monospace is for instructions only**: prompts, parameters, U/V chips, seeds, job IDs. Nothing decorative is monospace.
- **Dynamic Type**: titles, body, metadata scale; tab labels, job chips, parameter chips stay fixed (layout-sensitive grid).

## 4. Component Stylings

### Buttons

**Primary Button (Imagine / Generate)**
- Shape: Full pill, 999pt corner radius (or 50% circle when icon-only in the prompt bar)
- Background: `#FFFFFF` (white — the only primary)
- Text / icon: `#000000`
- Padding: 14pt vertical, 28pt horizontal (pill); 36pt circle for the prompt-bar send
- Font: Inter 16pt weight 600
- Pressed: background `#DADADA` + scale 0.98
- Disabled (empty prompt): background `#242424`, text `#6E6E6E`

**Secondary / Outline Button (Vary, Remix, Edit)**
- Background: transparent
- Text: `#FFFFFF`
- Border: 1pt `#2A2A2A`
- Padding: 12pt vertical, 20pt horizontal
- Corner radius: 999pt (pill) or 8pt (rectangular contexts)
- Font: Inter 14pt weight 500
- Pressed: background `#1A1A1A`

**Ghost Button (Remix / inline tool)**
- Background: `#1A1A1A`
- Text: `#FFFFFF`
- Border: 1pt `#2A2A2A`
- Corner radius: 8pt
- Padding: 10pt vertical, 18pt horizontal
- Font: Inter 14pt weight 500

**Text Button (Cancel job / Delete)**
- Font: Inter 14pt weight 500
- Color: `#A1A1A1` (or `#E5484D` for destructive)
- No background, no border
- Pressed: color shifts to `#FFFFFF`

### Job Chips (the core atom)

The U1–U4 / V1–V4 chip grammar is Midjourney's signature control, inherited from the Discord bot's reaction buttons.

**Upscale Chip (U1 / U2 / U3 / U4)**
- Shape: rounded rectangle, 8pt corner radius
- Default: background `#FFFFFF`, text `#000000` (white = the actionable primary)
- Font: SF Mono 13pt weight 600
- Padding: 8pt vertical, 14pt horizontal
- Pressed: background `#DADADA`
- After upscale: chip dims to `#1A1A1A` bg / `#6E6E6E` text (consumed)

**Variation Chip (V1 / V2 / V3 / V4)**
- Background: `#1A1A1A`
- Text: `#FFFFFF`
- Border: 1pt `#2A2A2A`
- Same shape/size/font as upscale
- Pressed: background `#242424`

**Re-roll Chip (🔄)**
- Same as variation chip, leading 🔄 glyph, "Re-roll" label
- Re-runs the original prompt as a fresh job at the top of the feed

**Parameter Chip (`--ar 1:1`, `--stylize 250`)**
- Background: `#1A1A1A`, border 1pt `#2A2A2A`
- Text: `#FFFFFF` (flag) — SF Mono 13pt weight 600
- Corner radius: 8pt
- Read-only in the feed; tappable in the prompt composer to edit

### Image Grid (the hero)

**2×2 Job Grid**
- Container: 12pt corner radius, `overflow: hidden`
- Four equal cells, each `aspect-ratio: 1` (default `--ar 1:1`; container reshapes for `--ar 3:2`, `16:9`, `9:16`, etc.)
- Seam: 6pt gap rendered as `#000000` (canvas shows through) — or 1pt `#2A2A2A` hairline in compact density
- Tap a cell → full-screen lightbox with U/V actions for that quadrant
- While rendering: shimmer placeholder (`#1A1A1A → #2A2A2A` sweep) + percentage label

**Job Action Row** (below the grid)
- Height: 40pt
- Leading icons (24pt hit, 18pt glyph): favorite (heart), download, more (⋯)
- Trailing: U1–U4 chip cluster (or "Upscaled" state)
- Color: icons `#A1A1A1`, chips per chip spec above

### Prompt Bar (signature input)

- Height: 50pt, pinned (bottom of Create, or above the tab bar)
- Background: `#1A1A1A`, border 1pt `#2A2A2A`
- Corner radius: 25pt (full-height pill)
- Leading: 18pt insets, placeholder "What will you imagine?" in `#6E6E6E`
- Trailing: 36pt circular **Imagine** button — `#FFFFFF` fill, `#000000` arrow glyph
- On focus: border brightens to `#3A3A3A`, parameter helper chips (`--ar`, `--v`, `--stylize`) slide up above
- Parameters typed inline as `--flag value` render in monospace as the user types

### Navigation

**Bottom Tab Bar**
- Height: 70pt + safe area
- Background: `rgba(0,0,0,0.92)` with `backdrop-filter: blur(20px)`, 0.5pt top border `#2A2A2A`
- Tabs (5): Create, Explore, Search, Library, Profile
- Icon size: 23pt
- Active: glyph + label `#FFFFFF` (no tint pill — Midjourney has no accent), stroke-width thickens slightly
- Inactive: `#6E6E6E`
- Labels: Inter 10pt w500, always shown

**Segmented Control (Imagine / Edit / Personalize)**
- Sits below the top bar, 0.5pt bottom divider `#2A2A2A`
- Active item: `#FFFFFF` text + 2pt underline `#FFFFFF`
- Inactive: `#6E6E6E`, no underline
- Font: Inter 14pt w600

**Top Bar**
- Height: 44pt + safe area
- Leading: sailboat logomark (22pt, white) + screen title (Inter 19pt w700)
- Trailing: search + filter icons (21pt, `#A1A1A1`)

### Input Fields

**Search Bar (Explore / Library)**
- Height: 44pt
- Background: `#1A1A1A`, no border
- Corner radius: 12pt
- Leading `magnifyingglass` 18pt `#6E6E6E`
- Placeholder "Search the community" 14pt `#6E6E6E`
- Focus: 1pt `#2D7FF9` border

**Prompt Composer (full editor)**
- Multi-line text area, monospace, `#FFFFFF` text on `#0E0E0E`
- Parameter chips render inline; image references appear as 44pt thumbnails pinned at the start of the field
- "Imagine" pill docked bottom-right of the composer

### Distinctive Components

**Job Card (prompt + grid + actions)**
- The atomic unit of the feed: monospace prompt line (`#A1A1A1`, key phrase in `#FFFFFF`), 2×2 grid, action row
- 18pt vertical gap between job cards
- Newest job animates in at the top, pushing older jobs down

**U/V Chip Cluster**
- The Discord-bot reaction grammar surfaced natively
- U1–U4 are white (do-it-now); V1–V4 are dark (explore-from); re-roll re-runs the prompt

**Progress Blur → Resolve**
- A rendering job shows a blurred, shimmering placeholder with a percentage; on completion it crossfades (250ms) into the four sharp images with a subtle scale-from-0.98

**Parameter Helper Strip**
- On prompt focus, a horizontally-scrolling strip of `--ar 1:1`, `--ar 16:9`, `--v 6`, `--style raw`, `--stylize 250` chips slides up; tapping one appends the flag to the prompt in monospace

**Lightbox / Single-image View**
- Full-bleed image on `#000000`, swipe between the four quadrants, bottom action bar: Upscale, Vary (Subtle/Strong), Remix, Zoom Out, Download, Favorite

**Explore Masonry**
- The community feed renders as a dense masonry of generated images on `#000000` — no card chrome, just imagery with a tap-to-open behavior

## 5. Layout Principles

### Spacing System
- Base unit: 2pt (tight) — scale: 2, 4, 6, 8, 10, 14, 18, 24, 32, 48
- Grid cell seam: 6pt (`#000000` shows through)
- Job-card vertical gap: 18pt
- Screen horizontal inset: 14pt (imagery wants to be near-edge-to-edge)

### Grid & Container
- iPhone: content at 14pt horizontal insets; 2×2 grid fills the content width
- The job grid reshapes to the prompt's `--ar`: square (1:1), landscape (3:2, 16:9), portrait (9:16) — the container aspect changes, the 2×2 structure does not
- iPad: feed becomes 2 columns of job cards; Explore masonry scales to 3–4 columns
- Lightbox: full-bleed, zero insets, image centered on black

### Whitespace Philosophy
- **Black is the whitespace**: negative space is literally `#000000` — it frames imagery like matting around a print
- **Chrome is thin and dim**: dividers are 0.5–1pt `#2A2A2A`; metadata is `#A1A1A1`; nothing chrome competes with the grid
- **Imagery near-bleeds**: 14pt insets, 6pt cell seams — maximize pixels devoted to the render
- **No decorative surfaces**: cards are `#0E0E0E` (barely visible); the design refuses ornament so the model output is the only spectacle

### Border Radius Scale
- Square (0pt): full-bleed lightbox imagery, top/bottom edges
- Subtle (6pt): inner image-cell rounding in compact density
- Small (8pt): job chips, parameter chips, ghost buttons
- Standard (12pt): job-grid container, search bar, cards
- Comfortable (16pt): bottom sheets, modals
- Prompt Pill (25pt): the prompt bar (full-height pill)
- Pill (999pt): primary buttons, segmented backgrounds
- Circle (50%): the Imagine send button, avatars

## 6. Depth & Elevation

Midjourney is radically flat — depth would distract from imagery. Elevation exists only as separation, never as decoration.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Job cards, feed, all content |
| Hairline (Level 1) | 1pt `#2A2A2A` border | Prompt bar, chips, inputs, segmented divider |
| Floating (Level 2) | `rgba(0,0,0,0.6) 0 8px 24px` + 1pt `#2A2A2A` | Bottom sheets, parameter helper strip, menus |
| Scrim (Level 3) | `rgba(0,0,0,0.7)` overlay | Behind the lightbox and modal sheets |

**Shadow Philosophy**: On true black, drop-shadows are nearly invisible — so Midjourney uses a 1pt `#2A2A2A` border as the primary "this floats" cue, with shadow only as a faint reinforcement on sheets. The lightbox doesn't elevate; it *replaces* — full-screen black scrim, image centered, no card.

### Motion
- **Job submit**: prompt bar's Imagine button pulses (scale 1 → 0.96 → 1, 120ms); a new shimmering job card slides in at the top over 250ms ease-out, pushing the feed down
- **Render progress**: shimmer sweep across the 2×2 placeholder (1.4s loop, `#1A1A1A → #2A2A2A`), percentage counts up
- **Resolve**: placeholder crossfades to four sharp images over 250ms + scale 0.98 → 1
- **Upscale tap**: U-chip flashes to `#DADADA` (90ms), upscaled result opens in lightbox with a 220ms shared-element zoom from the tapped cell
- **Lightbox swipe**: 1:1 finger-tracked paging between the four quadrants, 280ms spring (damping 0.85) on release
- **Parameter strip**: slides up 8pt + fades in over 180ms on prompt focus; slides down on blur
- **Tab change**: instant glyph color swap (no slide) — Midjourney keeps navigation motion silent
- **Haptic feedback**: soft impact on Imagine submit, light tick on chip tap, success haptic on job complete

## 7. Do's and Don'ts

### Do
- Use true black (`#000000`) as the canvas — OLED black makes imagery glow
- Treat white (`#FFFFFF`) as the only primary — Imagine button, active tab, U-chips
- Render the 2×2 grid as the hero — it carries 100% of the screen's color
- Render prompts and `--flags` in monospace — the prompt is code, not prose
- Use the U1–U4 / V1–V4 chip grammar verbatim — white U-chips, dark V-chips
- Keep chrome a grey ramp (`#FFFFFF → #A1A1A1 → #6E6E6E`) — never tint it
- Reshape the grid container to the prompt's `--ar` while keeping the 2×2 structure
- Use a 1pt `#2A2A2A` border (not shadow) as the "this floats" cue on black
- Reserve Discord blurple (`#4D5BCE`) for account/Discord connect only
- Show a shimmer placeholder + percentage while a job renders

### Don't
- Don't introduce a brand accent color — Midjourney deliberately has none
- Don't use a warm charcoal — the canvas is pure `#000000`, not `#191919`
- Don't tint, overlay, or filter generated imagery — content is full-color, chrome is grey
- Don't render prompts in the sans UI face — monospace is the instruction signal
- Don't add card borders/shadows around job cards — the feed is flat on black
- Don't use a tinted pill on the active tab — active is just `#FFFFFF`, no accent
- Don't reorder the U/V grammar — U is upscale (white), V is variation (dark)
- Don't pad imagery with large insets — 14pt insets, 6pt seams; pixels go to the render
- Don't animate navigation — tab changes are an instant color swap
- Don't use the blurple as a general CTA — it's a Discord-heritage account accent only
- Don't decorate empty space — black *is* the matting around the print

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 2×2 grid at 14pt insets; prompt bar full-width |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top bar |
| iPhone 15/16 Pro Max | 430pt | Larger grid; same structure |
| iPad (portrait) | 768pt | Feed → 2 columns of job cards; Explore masonry 3 cols |
| iPad (landscape) | 1024pt+ | Feed 2–3 columns; Explore masonry 4 cols; lightbox with side rail |

### Dynamic Type
- Screen title, section headings, body, metadata: full scale
- Tab labels, job chips (U1/V1), parameter chips, prompt monospace: FIXED (grid-layout-sensitive)
- Prompt text in the composer: scales to XL but stays monospace

### Orientation
- All screens support rotation
- Landscape: lightbox becomes the dominant mode (image fills, actions to the side)
- iPad split view: Library/Explore as primary + a job-detail secondary pane

### Touch Targets
- Job chip (U1/V1): 44pt min hit, 13pt mono glyph
- Tab bar icons: 23pt glyph, 44pt hit
- Imagine send button: 36pt circle, 44pt hit
- Grid cell: full-cell tap → lightbox
- Action-row icons: 18pt glyph, 24pt hit (favorite/download/more)

### Safe Area Handling
- Top: top bar respects safe area and Dynamic Island
- Bottom: prompt bar + tab bar respect home indicator
- Keyboard: prompt composer scrolls above keyboard; parameter helper strip docks above the keyboard
- Sides: 14pt content inset; lightbox is full-bleed (ignores insets)

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#000000` (true OLED black, dark-only)
- Surface 1: `#0E0E0E`
- Surface 2: `#1A1A1A`
- Surface 3: `#242424`
- Divider: `#2A2A2A`
- Primary (white): `#FFFFFF`
- White pressed: `#DADADA`
- Text secondary: `#A1A1A1`
- Text tertiary: `#6E6E6E`
- Rate / link blue: `#2D7FF9`
- Discord blurple: `#4D5BCE`
- Success: `#2ECC71`
- Error: `#E5484D`

### Example Component Prompts
- "Build the Midjourney job card in SwiftUI: a monospace prompt line at top (SF Mono 13pt `#A1A1A1`, the key phrase in `#FFFFFF`), then a 2×2 image grid (12pt corner radius, four equal `aspect-ratio: 1` cells, 6pt `#000000` seam), then a 40pt action row (favorite/download/more icons 18pt `#A1A1A1` on the left, U1–U4 chip cluster on the right). 18pt gap between job cards. Canvas `#000000`."

- "Create the Midjourney prompt bar: 50pt tall pinned pill, `#1A1A1A` background, 1pt `#2A2A2A` border, 25pt corner radius. Leading 18pt inset placeholder 'What will you imagine?' in `#6E6E6E`. Trailing 36pt circular Imagine button — `#FFFFFF` fill, `#000000` arrow glyph. On focus, brighten border to `#3A3A3A` and slide up a horizontal parameter helper strip (`--ar 1:1`, `--ar 16:9`, `--v 6`, `--style raw`, `--stylize 250`) as monospace chips."

- "Render the Midjourney U/V chip cluster: U1 U2 U3 U4 as white chips (`#FFFFFF` bg, `#000000` text, SF Mono 13pt w600, 8pt corner radius, 8pt×14pt padding) — these are the do-it-now upscale actions. V1 V2 V3 V4 as dark chips (`#1A1A1A` bg, `#FFFFFF` text, 1pt `#2A2A2A` border, same shape). A trailing 🔄 'Re-roll' dark chip re-runs the original prompt. After an upscale, dim its U-chip to `#1A1A1A`/`#6E6E6E`."

- "Build the Midjourney bottom tab bar: 70pt + safe area, `rgba(0,0,0,0.92)` with 20px backdrop blur, 0.5pt `#2A2A2A` top border. Five tabs: Create, Explore, Search, Library, Profile — 23pt glyphs, Inter 10pt w500 labels. Active = `#FFFFFF` glyph+label with slightly thicker stroke and NO tint pill (Midjourney has no accent). Inactive = `#6E6E6E`."

- "Create the Midjourney render-progress state: a 2×2 placeholder grid with an animated shimmer sweep (`#1A1A1A → #2A2A2A`, 1.4s loop) and a centered percentage label in `#A1A1A1`. On completion, crossfade (250ms) to the four sharp images with a scale 0.98 → 1."

- "Build the Midjourney lightbox: full-bleed image on `#000000`, no card, 1:1 finger-tracked horizontal paging between the four quadrants (280ms spring on release). Bottom action bar: Upscale, Vary (Subtle / Strong), Remix, Zoom Out, Download, Favorite — all white-text ghost buttons on a `rgba(0,0,0,0.7)` scrim."

### Iteration Guide
1. Canvas is true black `#000000` — NOT a warm charcoal, NOT off-black; dark-only, no light mode
2. There is NO brand accent — white `#FFFFFF` is the only primary (Imagine, active tab, U-chips)
3. The 2×2 grid is the hero and the only source of color — chrome is a grey ramp, never tinted
4. Prompts and `--flags` are ALWAYS monospace — that's the "this is an instruction" signal
5. U1–U4 chips are white (do-it-now upscale); V1–V4 chips are dark (variations) — never swap the grammar
6. The grid container reshapes to the prompt's `--ar`; the 2×2 structure itself never changes
7. Floating elements use a 1pt `#2A2A2A` border (not shadow) — shadows vanish on black
8. Discord blurple `#4D5BCE` is account/Discord-connect only — never a general CTA
9. Imagery near-bleeds (14pt insets, 6pt seams) — pixels belong to the render, not the chrome
10. Motion is minimal: job slides in, shimmer resolves, chip flashes — navigation is a silent color swap
