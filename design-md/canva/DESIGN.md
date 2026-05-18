# Design System Inspiration of Canva (iOS)

## 1. Visual Theme & Atmosphere

Canva's iOS app is a friendly, confidence-building creation tool. Its whole personality says *you can design this* — to a non-designer, on a phone, in two minutes. The atmosphere is bright, rounded and gradient-forward. The default workspace is near-white (`#FFFFFF` / `#F7F8FA`), every container has a soft 12–16pt corner radius, and the unmistakable Canva signal is the **cyan→purple brand gradient** (`#00C4CC → #7D2AE8`). That gradient is the logo, the floating **create** button, the primary CTA, and the hero rails of the template galleries. Color is used generously but with discipline: the gradient for *brand and action*, full-color thumbnails for *content*, and a calm neutral grey for *chrome*.

The home screen is the heart of the app and the screen this guide treats as the signature: a personal greeting ("Hi, Mel 👋"), a prominent rounded search field ("Search templates, photos…"), a horizontally scrolling row of **design-type tiles** (Instagram Post, Presentation, Doc, Whiteboard, Video — each a gradient-filled rounded icon), then vertically stacked **template galleries** ("Recommended", "Your designs", "Presentations", "Social media") rendered as a responsive 3-up thumbnail grid. Premium templates wear a small gold **PRO** badge. The bottom is a 5-slot tab bar — Home, Templates, a center gradient **create FAB**, Projects, You — with the FAB lifted slightly and casting the only meaningful shadow.

The other defining surface is the **design editor**: a full-screen canvas (the artboard) centered on a neutral backdrop, a top bar (back, undo/redo, Pro, share, the gradient **Done**), and a bottom **tool rail** of element types (Templates, Elements, Text, Uploads, Photos, Draw, Projects, Apps). Tapping a tool slides up a bottom-sheet picker. Selecting an element raises a contextual toolbar (color, font, size, position, transparency, animate). The editor is where Canva's "anyone can design" promise is delivered — heavy color in the content, restrained neutral chrome around it. The brand typeface is **Canva Sans**, a rounded geometric humanist; this guide uses **Plus Jakarta Sans** (free, SIL OFL) as a faithful free stand-in — same friendly roundness, same tight display tracking.

**Key Characteristics:**
- Bright near-white workspace (`#FFFFFF`) light / warm-neutral (`#18191B`) dark — friendly, never harsh
- Cyan→purple brand gradient (`#00C4CC → #7D2AE8`) is the logo, the create FAB, the primary CTA, template hero rails
- Home = personal greeting + big rounded search + horizontal design-type tile row + stacked template galleries
- Design-type tiles — gradient-filled rounded-square icons (Instagram, Presentation, Doc, Whiteboard, Video…)
- Template galleries — responsive 3-up thumbnail grid; full-color content, gold **PRO** badges on premium
- 5-slot bottom tab bar with a centered, lifted gradient **create FAB** (the one shadowed element)
- Design editor — neutral artboard backdrop, bottom tool rail, contextual element toolbar, slide-up pickers
- Pill-shaped buttons everywhere — primary uses the gradient; secondary is solid purple or neutral outline
- Generous 12–16pt rounded corners on every card, tile, sheet and field — softness is the brand
- Pro is gold (`#FFC24B`) — the single premium accent, distinct from the brand gradient
- Plus Jakarta Sans (Canva Sans stand-in) — rounded geometric humanist, tight display tracking, heavy weights for headings
- Bottom-sheet pickers — every tool, share flow and design-type chooser slides up as a rounded sheet

## 2. Color Palette & Roles

### Primary (Interactive)
- **Canva Cyan** (`#00C4CC`): Brand gradient start; "See all" links, selected states, inline accents.
- **Canva Purple** (`#7D2AE8`): Brand gradient end; solid secondary buttons, selected tool highlight.
- **Canva Gradient** (`#00C4CC → #7D2AE8`, 135°): Logo, create FAB, primary CTA, design-type tiles, template hero rails.
- **Canva Blue** (`#3B5CFF`): Secondary action / link blue, info accents (used inside gradients and as a flat link).
- **Canva Purple Pressed** (`#6A1FD0`): Pressed state for gradient/purple buttons (apply via 0.93 brightness).

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light app background and editor sheet.
- **Workspace Grey** (`#F7F8FA`): Home background behind cards, editor artboard backdrop.
- **Surface Raised** (`#FFFFFF`): Cards, tiles, sheets (with shadow, not border).
- **Surface Pressed** (`#EEF0F4`): Pressed rows / chips.
- **Divider** (`#E6E8EC`): Hairline separators, search-field outline.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#18191B`): Primary dark background — warm-neutral, NOT pure black.
- **Dark Surface 1** (`#1F2023`): Cards, tiles, panels.
- **Dark Surface 2** (`#27282C`): Search field, raised chips, hovered rows.
- **Dark Divider** (`#303135`): Hairline separators and the tab-bar top border.

### Text
- **Text Primary Light** (`#0D0E10`): Headlines and body on white.
- **Text Secondary Light** (`#6F7178`): Metadata, captions, placeholder.
- **Text Tertiary Light** (`#A4A6AD`): Disabled, inactive tab labels.
- **Text Primary Dark** (`#ECECEE`): Headlines and body on dark.
- **Text Secondary Dark** (`#A4A6AD`): Metadata, captions on dark.
- **Text Tertiary Dark** (`#6F7178`): Disabled, inactive tab labels on dark.

### Template / Accent Palette
Canva's template thumbnails and design-type tiles use a wide gradient set. These are the recurring brand-adjacent gradients used on tiles and rails.

| Role | Gradient (Light + Dark identical) |
|------|-----------------------------------|
| Brand / Action | `#00C4CC → #7D2AE8` |
| Cyan / Blue tile | `#00C4CC → #3B5CFF` |
| Purple / Magenta tile | `#7D2AE8 → #C13AE0` |
| Warm / Sunset tile | `#FF7A59 → #FFC24B` |
| Fresh / Mint tile | `#1FC77C → #00C4CC` |
| Indigo tile | `#3B5CFF → #7D2AE8` |
| Coral tile | `#FF5163 → #FF7A59` |

### Semantic
- **Pro Gold** (`#FFC24B`): Canva Pro badge, upgrade CTAs, premium-element lock — the single premium accent.
- **Success Green** (`#1FC77C`): Saved / synced / published confirmation.
- **Error Red** (`#FF5163`): Delete confirmation, upload-failure banner.
- **Warning Amber** (`#FFC24B`): Reuses Pro Gold for quota / limit warnings.
- **Selection Tint** (`#7D2AE81F`): ~12% purple overlay on a selected element on the artboard.

## 3. Typography Rules

### Font Family
- **Primary**: `Canva Sans` (Canva's proprietary rounded geometric humanist). Free stand-in: **`Plus Jakarta Sans`** (by Tokotype, SIL OFL) — same friendly roundness, excellent display weights.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **In-design fonts**: the editor exposes hundreds of user-selectable fonts for *content text* — that is design data, not app chrome. App UI always uses Canva Sans / Plus Jakarta Sans.
- **Weights used**: 400 (body), 500 (labels), 600 (emphasis / chips), 700 (section headings), 800 (display / greeting).

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Plus Jakarta Sans | 32pt | 800 | 1.15 | -0.6pt | Onboarding / large titles |
| Greeting | Plus Jakarta Sans | 26pt | 800 | 1.2 | -0.3pt | "Hi, {name} 👋" home header |
| Section Title | Plus Jakarta Sans | 22pt | 700 | 1.25 | -0.2pt | "Recommended", "Your designs" |
| Subsection | Plus Jakarta Sans | 18pt | 700 | 1.3 | -0.1pt | Sheet titles, sub-headers |
| Body | Plus Jakarta Sans | 16pt | 400 | 1.5 | 0pt | Paragraph / descriptions |
| Label | Plus Jakarta Sans | 15pt | 600 | 1.35 | 0pt | Tile labels, template names |
| Button | Plus Jakarta Sans | 16pt | 700 | 1.0 | 0pt | Primary / secondary buttons |
| Meta | Plus Jakarta Sans | 14pt | 400 | 1.4 | 0pt | "Edited 2 hours ago · Shared" |
| Chip / Type Label | Plus Jakarta Sans | 12pt | 600 | 1.3 | 0.1pt | Design-type tile labels |
| Tab Label | Plus Jakarta Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Pro Badge | Plus Jakarta Sans | 9pt | 800 | 1.0 | 0.3pt | Gold PRO badge on thumbnails |
| Tool Label | Plus Jakarta Sans | 11pt | 600 | 1.2 | 0.1pt | Editor bottom tool-rail labels |

### Principles
- **Friendly, rounded, confident**: heavy display weights (800) with tight negative tracking for greetings/titles; warm and approachable, never corporate-thin.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: regular body, semibold labels/chips, bold headings, extrabold display. No thin, no hairline.
- **App chrome ≠ design content**: app UI is always Canva Sans; the hundreds of in-editor fonts apply only to the user's content text.
- **Labels are semibold**: tile labels, template names and chips sit at 600 — readable at small sizes over busy thumbnails.
- **Dynamic Type first-class**: greeting, section titles, body, meta scale; tab labels, chip labels, the Pro badge and tool labels stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (Create a design / Done)**
- Shape: pill, 999pt corner radius
- Background: linear gradient `#00C4CC → #7D2AE8` at 135°
- Text: `#FFFFFF`, Plus Jakarta Sans 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal
- Pressed: brightness 0.93 + scale 0.98
- The home create FAB is a 46–56pt rounded square (16pt radius) with this gradient + the only app shadow `rgba(125,42,232,0.45) 0 6px 16px`

**Secondary Solid Button (Continue)**
- Background: solid `#7D2AE8` (Canva Purple)
- Text: `#FFFFFF`, 15pt weight 700
- Padding: 12pt vertical, 22pt horizontal
- Corner radius: 999pt (pill)
- Pressed: background `#6A1FD0`

**Outline / Neutral Button (Use template)**
- Background: transparent
- Text: `#0D0E10` light / `#ECECEE` dark, 15pt weight 600
- Border: 1.5pt `#E6E8EC` / `#303135`
- Corner radius: 999pt (pill)
- Padding: 11pt vertical, 20pt horizontal
- Pressed: background `#EEF0F4` / `#27282C`

**Pro Button (Try Pro / Upgrade)**
- Background: `#FFC24B` (Pro Gold)
- Text: `#1A1205`, 14pt weight 800
- Corner radius: 999pt (pill)
- Padding: 10pt vertical, 18pt horizontal
- Often paired with a small crown glyph

**Text Button (See all / Skip)**
- Font: Plus Jakarta Sans 15pt weight 600
- Color: `#00C4CC` (Canva Cyan)
- No background, no underline
- Pressed: opacity 0.6

**Icon Button (top bar undo / share / settings)**
- 22pt glyph in a 44pt hit area
- Color: `#0D0E10` / `#ECECEE`
- Pressed: background circle `#EEF0F4` / `#27282C` at 36pt

### Core Atoms

**Design-Type Tile**
- A vertical unit: a 56pt rounded-square (16pt radius) gradient icon + a 12pt w600 label below
- Each type has its own gradient (Instagram cyan-blue, Presentation purple-magenta, Doc sunset, Whiteboard mint, Video indigo)
- Tap scales 0.96 then opens that type's template gallery or a new blank doc

**Template Thumbnail**
- Aspect ratio matches the design type (3:4 portrait social, 16:9 presentation, 1:1 square)
- 12pt corner radius, full-color, no border; soft shadow on light, flat on dark
- Optional gold **PRO** badge top-right: `#FFC24B` bg, `#1A1205` text, 9pt w800, 6pt radius
- Tap opens the template detail / "Customize this template" sheet

**Search Field**
- Height: 46pt, 14pt corner radius
- Background: `#F7F8FA` / `#27282C`
- Leading magnifier 17pt `#6F7178`
- Placeholder "Search templates, photos…" 15pt `#6F7178`
- Focus: 1.5pt `#00C4CC` border

**Pro Badge / Lock**
- Gold pill or a small crown glyph in `#FFC24B`; on premium elements a lock overlay with the same gold

**Avatar**
- 34pt circle, gradient fill if no photo, white initial in Plus Jakarta Sans 14pt w700

### Navigation

**Bottom Tab Bar**
- Height: 70pt + safe area
- Background: `#FFFFFF` / `#18191B` at 94% + blur, 0.5pt top border `#E6E8EC` / `#303135`
- 5 slots: Home, Templates, **create FAB (center)**, Projects, You
- The center slot is a 46pt rounded-square (16pt radius) gradient FAB, lifted -4pt, shadow `rgba(125,42,232,0.45) 0 6px 16px` — the one elevated element
- Icon size: 22pt; active label/icon `#0D0E10` / `#ECECEE`; inactive `#A4A6AD`
- Labels: Plus Jakarta Sans 10pt w600, always shown (except under the FAB)

**Home Top Bar**
- Greeting "Hi, {name} 👋" Plus Jakarta Sans 26pt w800 leading; trailing 34pt avatar
- No title bar — the greeting *is* the header; transparent over the workspace

**Editor Top Bar**
- Height: 52pt + safe area
- Leading: back chevron; center-left: undo / redo
- Trailing: Pro (gold), share glyph, gradient **Done** pill
- Background: `#FFFFFF` / `#18191B`, 0.5pt bottom divider

**Editor Bottom Tool Rail**
- Height: ~64pt + safe area
- Horizontal scroll of tools: Templates, Elements, Text, Uploads, Photos, Draw, Projects, Apps
- Each: 24pt glyph + 11pt w600 label, inactive `#6F7178`, active `#7D2AE8`
- Tapping a tool slides up its bottom-sheet picker

### Input Fields

**Text Field (project name, search)**
- Height: 46pt, 14pt corner radius
- Background: `#F7F8FA` / `#27282C`, no border at rest
- Focus: 1.5pt `#00C4CC` border
- Placeholder `#6F7178` 15pt; text `#0D0E10` / `#ECECEE` 16pt

**Contextual Element Toolbar (editor)**
- Appears above the keyboard / above the artboard when an element is selected
- A horizontal scroll of round/pill controls: color swatch, font, size stepper, alignment, transparency, layer, animate, delete
- Background: `#FFFFFF` / `#1F2023`, 16pt top corners, soft shadow

### Distinctive Components

**Create FAB (center tab)**
- The signature affordance: a centered, lifted, gradient rounded-square `+` button in the tab bar. Tap opens the "Create a design" bottom sheet — recent sizes, design types, custom-size, import.

**Design-Type Tile Row**
- A horizontally scrolling rail of gradient tiles on home. The fastest path to a new design; each tile's gradient is part of Canva's color identity.

**Template Gallery Grid**
- Stacked, titled sections ("Recommended", "Your designs", "Presentations") each a horizontally scrollable or 3-up grid of full-color thumbnails with optional gold PRO badges. Content is loud; chrome is quiet.

**Bottom-Sheet Picker**
- Every tool, design-type chooser, share flow and font picker is a rounded bottom sheet (16–24pt top corners) that slides up over a dimmed backdrop with a grab handle.

**Design Editor (Artboard)**
- A neutral-backdrop full-screen canvas with the artboard centered; top bar + bottom tool rail + contextual element toolbar. The "anyone can design" core — heavy color in content, restrained chrome.

**Pro Upsell Sheet**
- Gold-accented sheet listing Pro benefits, a feature carousel, and a gold "Start free trial" CTA — the single place gold dominates.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 10, 12, 16, 18, 22, 24, 32, 40, 48
- Home horizontal inset: 18pt
- Template grid gutter: 10pt; 3 columns on iPhone
- Section vertical rhythm: 22pt before a section header, 12pt header→content

### Grid & Container
- iPhone: single column; 18pt insets; template grid 3-up; horizontal rails scroll
- iPad portrait: 18–24pt insets; template grid 4–5-up; design-type tiles in a wrap grid
- iPad landscape: editor shows a persistent left tool panel + canvas (rail becomes a sidebar)
- Editor artboard: centered, fit-to-screen with pinch-zoom; backdrop is workspace grey

### Whitespace Philosophy
- **Friendly density**: cards and tiles are generously rounded with 10–12pt gutters — busy but breathable
- **Content is loud, chrome is quiet**: thumbnails are full-color; surrounding UI is neutral grey + one gradient accent
- **Rounded everything**: 12–16pt radii on cards/tiles/sheets, 999pt pills on buttons — softness is the brand
- **The gradient is rationed**: it marks brand + primary action only; secondary actions are solid purple or neutral outline

### Border Radius Scale
- Square (0pt): full-bleed thumbnails edges within a clipped tile (rare)
- Subtle (6pt): PRO badge, small chips
- Standard (8pt): nested controls, stepper buttons
- Comfortable (12pt): template thumbnails, cards
- Large (16pt): design-type tiles, search field, create FAB, sheet top corners
- Extra (24pt): large bottom sheets, modals
- Pill (500pt): all buttons, filter chips
- Circle (50%): avatars, round tool controls

## 6. Depth & Elevation

Canva is mostly flat with soft, diffuse shadows reserved for raised content and the create FAB.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Backgrounds, dividers, inline rows |
| Card (Level 1) | `rgba(13,14,16,0.06) 0 2px 8px` (light) | Template thumbnails, cards on white |
| Create FAB | `rgba(125,42,232,0.45) 0 6px 16px` | The centered gradient create button only |
| Sheet (Level 2) | `rgba(13,14,16,0.12) 0 -6px 28px` | Bottom-sheet pickers, contextual toolbar |
| Modal Overlay | `rgba(13,14,16,0.45)` | Dim behind sheets and Pro upsell |

**Shadow Philosophy**: shadows are soft and diffuse (the friendly, "Canva" feel), used to lift content thumbnails off the workspace and to signal a sheet is floating. The create FAB gets a tinted purple glow so it reads as *the* primary action. On dark mode shadows nearly vanish, so cards rely on the `#1F2023` surface step and sheets add a faint 1pt `#303135` top border.

### Motion
- **Create FAB tap**: scales 0.92 then the "Create a design" sheet slides up 320ms ease-out; dim fades in parallel
- **Design-type tile tap**: scales 0.96, then push/slide to the gallery in 300ms
- **Bottom sheet**: slides up 320ms cubic-ease-out; drag-to-dismiss tracks the finger, releases with spring damping 0.85
- **Template open**: thumbnail does a shared-element-style zoom into the detail sheet over 320ms
- **Editor element select**: contextual toolbar slides up from the bottom in 220ms ease-out; selection box fades in with 8 round handles
- **Tab switch**: instant content swap; active icon does a subtle 1.05 scale pop (120ms)
- **Gradient buttons**: press = 0.93 brightness + 0.98 scale, 100ms
- **Skeleton loading**: template grid shows shimmering rounded placeholders (1.2s loop) while thumbnails fetch
- **Haptic feedback**: light impact on FAB tap, tile tap, element select, template apply; success haptic on export/publish complete

## 7. Do's and Don'ts

### Do
- Use a bright near-white workspace (`#FFFFFF` / `#F7F8FA`) light, warm-neutral (`#18191B`) dark — friendly, never harsh
- Reserve the cyan→purple gradient (`#00C4CC → #7D2AE8`) for brand + primary action (logo, create FAB, primary CTA, hero rails)
- Round everything generously — 12–16pt radii on cards/tiles/sheets, 999pt pills on buttons
- Build home as greeting + big rounded search + horizontal design-type tile rail + stacked template galleries
- Give each design-type tile its own gradient (Instagram, Presentation, Doc, Whiteboard, Video)
- Keep template thumbnails full-color with optional gold PRO badges — content is loud, chrome is quiet
- Center the create FAB in the tab bar, lifted, with a tinted purple glow — the one elevated element
- Use gold (`#FFC24B`) exclusively for Pro / premium — never mix it with the brand gradient's meaning
- Slide tools, share flows and design-type choosers up as rounded bottom sheets with a grab handle
- Use heavy display weights (800) with tight negative tracking for greetings and section titles

### Don't
- Don't use pure black (`#000000`) for canvas or text — warm-neutral `#18191B` / near-black `#0D0E10`
- Don't overuse the gradient — secondary buttons are solid purple or neutral outline, not gradient
- Don't use sharp corners — Canva's softness (rounded, pill) is the entire brand feel
- Don't dim or desaturate content thumbnails in dark mode — only the chrome dims
- Don't put shadows on flat rows or chrome — shadows are for raised content cards and the FAB only
- Don't use the brand gradient for Pro — Pro is gold; the gradient is brand/action
- Don't replace the centered create FAB with a plain tab — the lifted gradient `+` is the signature
- Don't use thin/hairline weights for headings — Canva is confident and rounded (700/800)
- Don't apply in-editor content fonts to app chrome — app UI is always Canva Sans / Plus Jakarta Sans
- Don't open tools as full-screen pages — Canva uses slide-up bottom sheets
- Don't crowd buttons with square corners — every button is a pill

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Template grid 2–3-up; insets 16pt; FAB 46pt |
| iPhone 13/14/15 | 390pt | Standard: grid 3-up, insets 18pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated by transparent home top bar |
| iPhone 15/16 Pro Max | 430pt | Grid 3-up larger thumbnails; insets 20pt |
| iPad (portrait) | 768pt | Grid 4-up; design-type tiles wrap; editor rail at bottom |
| iPad (landscape) | 1024pt+ | Grid 5–6-up; editor shows a persistent left tool sidebar + canvas |

### Dynamic Type
- Greeting, section titles, body, meta: full scale
- Tab labels (10pt), chip/type labels (12pt), Pro badge (9pt), tool labels (11pt): FIXED (layout-sensitive)
- Template names truncate to 1 line with ellipsis at large type sizes

### Orientation
- Home and galleries reflow column count on rotation (3→4→5-up)
- The editor is the key orientation case: portrait = bottom tool rail; landscape (iPad) = left sidebar + canvas
- Artboard re-fits to screen on rotation; zoom resets to fit

### Touch Targets
- Tab icons / create FAB: 46pt FAB, 44pt hit for tabs
- Design-type tile: 56pt icon, 76pt full-tile tap
- Template thumbnail: full thumbnail tappable (min 100pt)
- Top-bar icon buttons: 22pt glyph, 44pt hit
- Editor tool-rail item: 24pt glyph, 56pt hit

### Safe Area Handling
- Top: transparent home bar / editor bar respects safe area + Dynamic Island
- Bottom: tab bar + create FAB + home indicator respected; sheets dock above the home indicator
- Keyboard: project-name and search fields scroll above keyboard; the contextual element toolbar sits directly above the keyboard in the editor
- Sides: 18pt home inset (16pt SE)

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF` / workspace `#F7F8FA`
- Canvas (dark): `#18191B`
- Surface 1 (dark): `#1F2023` · Surface 2 (dark): `#27282C`
- Divider: `#E6E8EC` light / `#303135` dark
- Text primary: `#0D0E10` light / `#ECECEE` dark
- Text secondary: `#6F7178` / `#A4A6AD`
- Canva Cyan: `#00C4CC` · Canva Purple: `#7D2AE8`
- Brand gradient: `#00C4CC → #7D2AE8` (135°)
- Canva Blue: `#3B5CFF`
- Pro Gold: `#FFC24B`
- Success: `#1FC77C` · Error: `#FF5163`
- Tile gradients: cyan-blue `#00C4CC→#3B5CFF`, purple-magenta `#7D2AE8→#C13AE0`, sunset `#FF7A59→#FFC24B`, mint `#1FC77C→#00C4CC`
- Selection tint: `#7D2AE81F`

### Example Component Prompts
- "Build the Canva home in SwiftUI: transparent top bar with 'Hi, {name} 👋' in Plus Jakarta Sans 26pt w800 leading + a 34pt gradient avatar trailing. Below: a 46pt search field, 14pt corner radius, `#27282C` fill, leading 17pt magnifier, placeholder 'Search templates, photos…' in `#A4A6AD`. Below: a horizontal scroll of design-type tiles — each a 56pt rounded-square (16pt radius) with its own gradient (Instagram `#00C4CC→#3B5CFF`, Presentation `#7D2AE8→#C13AE0`, Doc `#FF7A59→#FFC24B`) and a 12pt w600 label. Then a 'Recommended' section header (Plus Jakarta Sans 22pt w700) with a 'See all' link in `#00C4CC`, and a 3-up grid of 3:4 full-color template thumbnails, 12pt radius, optional gold PRO badge."

- "Create the Canva bottom tab bar: 5 slots — Home, Templates, center create FAB, Projects, You. Background `#18191B` @94% + blur, 0.5pt top border `#303135`. The center slot is a 46pt rounded-square (16pt radius) with gradient `#00C4CC → #7D2AE8`, lifted -4pt, shadow `rgba(125,42,232,0.45) 0 6px 16px`, white `+` glyph. Other tabs: 22pt glyph + 10pt w600 label, active `#ECECEE`, inactive `#A4A6AD`."

- "Render a Canva primary button: pill (999pt radius), linear-gradient `#00C4CC → #7D2AE8` at 135°, white text Plus Jakarta Sans 16pt w700, 14pt/28pt padding. Pressed = brightness 0.93 + scale 0.98. Add a secondary solid-purple `#7D2AE8` pill and a gold Pro pill (`#FFC24B` bg, `#1A1205` text, 14pt w800)."

- "Build a Canva template thumbnail: 3:4 aspect, 12pt corner radius, full-color gradient fill, soft shadow on light / flat on dark. Top-right gold PRO badge — `#FFC24B` background, `#1A1205` text, Plus Jakarta Sans 9pt w800, 6pt radius, 2pt/6pt padding. Tap = shared-element zoom into the customize sheet over 320ms."

- "Create the Canva 'Create a design' bottom sheet: rounded 24pt top corners, grab handle, dimmed `rgba(13,14,16,0.45)` backdrop. Title 'Create a design' Plus Jakarta Sans 18pt w700. A grid of design-type tiles (gradient rounded-square icons + 12pt w600 labels), a 'Custom size' row, and an 'Import' row. Slides up 320ms ease-out; drag-to-dismiss with spring damping 0.85."

- "Build the Canva editor bottom tool rail: ~64pt tall, horizontal scroll of tools (Templates, Elements, Text, Uploads, Photos, Draw, Apps), each a 24pt glyph + 11pt w600 label, inactive `#6F7178`, active `#7D2AE8`. Tapping a tool slides up its bottom-sheet picker. Above the keyboard, show a contextual element toolbar (color swatch, font, size stepper, alignment, transparency, animate, delete) on a `#1F2023` surface with 16pt top corners."

### Iteration Guide
1. Workspace is bright near-white (`#FFFFFF`/`#F7F8FA`) light, warm-neutral `#18191B` dark — NOT pure black, NOT harsh
2. The cyan→purple gradient `#00C4CC → #7D2AE8` is brand + primary action only — logo, create FAB, primary CTA, hero rails
3. Everything is rounded — 12–16pt radii on cards/tiles/sheets, 999pt pills on every button; softness is the brand
4. Home = greeting + big rounded search + horizontal design-type tile rail + stacked full-color template galleries
5. The create FAB is centered in the tab bar, lifted, gradient, with a tinted purple glow — the one elevated element
6. Pro is gold `#FFC24B` and *only* gold — never the brand gradient; the gradient never means "premium"
7. Content thumbnails stay full-color in dark mode — only chrome dims
8. Tools, share flows and design-type choosers are slide-up rounded bottom sheets with a grab handle
9. Headings use heavy display weights (800) with tight negative tracking; app chrome is always Canva Sans / Plus Jakarta Sans
10. Shadows are soft and diffuse, reserved for raised content cards and the FAB — chrome is flat; motion is friendly (220–320ms ease-out)
