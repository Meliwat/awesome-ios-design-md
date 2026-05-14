# Design System Inspiration of Figma (iOS)

## 1. Visual Theme & Atmosphere

Figma's iOS app is not a design tool. The desktop app is the design tool — Figma for iPhone is a *companion app*, the place you go to leave a comment on a teammate's frame at 11pm or fan through your latest projects on the subway. The aesthetic, then, is *Figma minus the chrome*: the same disciplined, content-first sensibility that powers the canvas-tool — neutral background, generous breathing room, geometric precision — but stripped of toolbars, layer panels, and right-click menus. What's left is the *signature* — five primary-colored cubes (the Figma swatches), a thin geometric sans, and the same Macaron-pink "F" lockup that anchors every Figma surface.

The canvas is **Off-White** (`#FFFFFF`) on light mode and **Near-Black** (`#1E1E1E`) on dark mode — Figma's exact Editor background palette. The brand cubes — the five signature colors that have defined Figma since 2016 — appear as **avatar fills**, **file thumbnails** (when no preview is set), **selection cursors**, and as the accent pop on each project card: **Figma Red** (`#F24E1E`), **Figma Orange** (`#FF7262`), **Figma Purple** (`#A259FF`), **Figma Blue** (`#1ABCFE`), **Figma Green** (`#0ACF83`). They are loud, but they are used *as accents on white*, never as canvas fills — the photography-of-a-design-tool restraint. The system grey ramp (`#F5F5F5` through `#1E1E1E`) does 90% of the chrome work.

Typography is **Inter** (the open-source face Rasmus Andersson originally drew for Figma's marketing) across the marketing site and the mobile app. Inter is geometric, low-contrast, and ships with excellent OpenType features — tabular numerals, slashed zero, alternate one — which Figma exploits in code-like contexts (frame dimensions, hex codes, line/column counts). Type sizes are small and information-dense: file names at 14pt, comment bodies at 13pt, metadata at 11pt — this is a *productivity* app, not a hero-text app.

**Key Characteristics:**
- White canvas (`#FFFFFF`) in light, near-black (`#1E1E1E`) in dark — both modes are first-class and the app is mostly used in dark mode on iPhone
- The five Figma cubes — Red `#F24E1E`, Orange `#FF7262`, Purple `#A259FF`, Blue `#1ABCFE`, Green `#0ACF83` — as the brand pop on avatars, file thumbnails, cursors, project pop
- Blurple `#5551FF` and Blue `#0D99FF` as the actual UI action color — Figma's "Blue" is the Action Blue used on the Editor's selection outlines and primary CTAs (separate from the avatar Blue cube)
- File cards as 16:10 aspect-ratio rectangles with the preview thumbnail filling the card and metadata stacked below — recents grid is the home screen
- Comment threads as the dominant interactive surface — a pinned circular avatar on a frame opens a side-sheet with threaded replies
- Macaron Pink (`#FFC8C8`) as the brand softness; appears as the Community accent and the empty-state illustration palette
- Inter typography at small, dense sizes (11-16pt range carries the app); SF Pro Mono for hex codes and dimensions
- Bottom tab bar: Recents, Drafts, Notifications, Profile — four tabs, content-density first
- Thin 1px (literal, not 1pt) hairlines throughout — Figma is precise, edge-defined, not elevated
- Subtle shadows only on floating elements (`rgba(0,0,0,0.08)` max); the canvas does most of the visual work

## 2. Color Palette & Roles

### Brand Cubes (the iconic five)
- **Figma Red** (`#F24E1E`): Avatar fill, file thumbnail accent, cursor color #1.
- **Figma Orange** (`#FF7262`): Avatar fill, cursor color #2.
- **Figma Purple** (`#A259FF`): Avatar fill, cursor color #3.
- **Figma Blue (cube)** (`#1ABCFE`): Avatar fill, cursor color #4.
- **Figma Green** (`#0ACF83`): Avatar fill, cursor color #5.

These five are *brand accents*, not action colors. They appear on avatars, thumbnails, and cursors, randomly assigned per user. Never use them for UI actions.

### UI Action Colors
- **Action Blue** (`#0D99FF`): Primary CTA on the Editor — "Share", "Open in Figma", "Reply". The button color.
- **Action Blue Pressed** (`#0769B5`): Pressed state.
- **Blurple** (`#5551FF`): The Figma marketing brand purple-blue; appears on plan upgrades, FigJam links, and Slides accents.
- **Selection Blue** (`#5EB6FC`): The Editor's selection-outline blue — appears on selection cursors and the "selected layer" outline in mobile mirroring views.

### Macaron Pinks (softness)
- **Macaron Pink** (`#FFC8C8`): Empty-state illustration palette, Community accent, occasional onboarding fills.
- **Macaron Pink Bright** (`#FFA6A6`): A slightly more saturated variant for hover/press on pink chips.

### Canvas, Surfaces & Dividers
- **Canvas White** (`#FFFFFF`): Light-mode primary canvas — Recents, file cards, sheets.
- **Surface Light 1** (`#F5F5F5`): Section fills, search field bg, sidebar fills.
- **Surface Light 2** (`#E5E5E5`): Chip backgrounds, hover states, dividers used as fills.
- **Divider Light** (`#E5E5E5`): Hairline 1px dividers.
- **Hairline Light** (`#EEEEEE`): Lighter hairlines inside cards.

### Dark Mode Surfaces (Figma Editor's exact tokens)
- **Dark Canvas** (`#1E1E1E`): Primary dark background — matches the Figma Editor canvas exactly.
- **Dark Surface 1** (`#2C2C2C`): Cards, sheets, the file list rows.
- **Dark Surface 2** (`#383838`): Chip fills, hover states.
- **Dark Surface 3** (`#444444`): Highest elevation — popovers, the Comments sidebar inside the editor.
- **Dark Divider** (`#2C2C2C`): Hairlines on dark canvas.

### Text
- **Ink** (`#1E1E1E`): Light-mode primary text — file names, comment bodies, headings.
- **Text Secondary Light** (`#757575`): Secondary — metadata, timestamps, author bylines.
- **Text Tertiary Light** (`#B3B3B3`): Tertiary — placeholders, empty-state labels.
- **Text Dark Primary** (`#FFFFFF`): Dark-mode primary text.
- **Text Dark Secondary** (`#B3B3B3`): Dark-mode secondary.
- **Text Dark Tertiary** (`#757575`): Dark-mode tertiary.

### Semantic
- **Success Green** (`#14AE5C`): "Saved", "Comment resolved", deploy success. Distinct from the cube Green.
- **Warning Amber** (`#F2AC2A`): Caution states, "Unsaved changes", trial countdown.
- **Error Red** (`#E03E1A`): Form validation, "Failed to save". Distinct from cube Red.
- **Info Blue** (`#0D99FF`): Same as Action Blue — informational toasts.

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (Rasmus Andersson, originally for Figma marketing — now open-source)
- **Weights available**: Thin (100), ExtraLight (200), Light (300), Regular (400), Medium (500), SemiBold (600), Bold (700), ExtraBold (800), Black (900)
- **Numbers**: Inter's tabular-nums OpenType feature is enabled on every numeric value
- **Monospace**: `SF Mono` for hex codes (`#FF7262`), pixel dimensions (`120 × 80`), and code blocks. Falls back to `JetBrains Mono`.
- **Fallback stack**: `'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Helvetica Neue', Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Hero Title | Inter | 28pt | 700 | 1.2 | -0.4pt | "Recents", "Drafts" large-title nav |
| Page Section | Inter | 17pt | 700 | 1.3 | -0.2pt | "Recently viewed", "Recent files" |
| Subhead | Inter | 15pt | 600 | 1.3 | -0.1pt | Card section dividers, modal titles |
| File Name | Inter | 14pt | 500 | 1.3 | -0.1pt | Project / file thumbnail labels |
| Body | Inter | 14pt | 400 | 1.5 | 0pt | Comment thread body, description copy |
| Metadata | Inter | 12pt | 400 | 1.4 | 0pt | Timestamps, byline, "Edited 2h ago" |
| Caption | Inter | 11pt | 500 | 1.3 | 0pt | Small labels, badge text |
| Comment Body | Inter | 13pt | 400 | 1.5 | 0pt | The threaded reply body |
| Comment Author | Inter | 13pt | 600 | 1.3 | 0pt | The display name above each reply |
| Pixel Dimension | SF Mono | 12pt | 500 tnum | 1.3 | 0pt | "120 × 80", "16pt" inspect values |
| Hex Code | SF Mono | 12pt | 500 tnum | 1.3 | 0pt | "#FF7262" — always all-caps hex letters |
| Button (Primary) | Inter | 14pt | 600 | 1.0 | 0pt | "Open in Figma", "Reply" |
| Tab Label | Inter | 11pt | 500 | 1.0 | 0pt | Bottom tab bar labels |
| Avatar Initial | Inter | 14pt | 600 | 1.0 | 0pt | The single letter inside the cube avatar |

### Principles
- **Information density over hero scale** — Figma is a productivity app; type sizes cluster in the 11-16pt range
- **Weights cluster at 400 / 500 / 600 / 700** — Regular for body, Medium for file names, SemiBold for emphasis and short headings, Bold for large nav
- **Tabular numerals on every number** — frame dimensions, counts, timestamps; Inter's `tnum` feature handles this
- **Slashed zero in code contexts** — Inter has alternate stylistic sets; `cv11` slashes the zero on hex codes and dimensions
- **All-caps hex letters** — `#FF7262`, never `#ff7262`. This is a Figma convention.
- **Mixed-unit values stay mono** — "120 × 80", "16pt", "1.5×" stay in SF Mono with tabular figures
- **Dynamic Type respected on comment bodies and metadata**; fixed on file-card labels and tab labels for grid integrity

## 4. Component Stylings

### Buttons

**Primary CTA ("Open in Figma", "Reply", "Share")**
- Background: `#0D99FF` (Action Blue)
- Text: `#FFFFFF`, Inter 14pt SemiBold (600)
- Padding: 10pt vertical, 16pt horizontal
- Corner radius: 6pt — Figma's exact button radius (small and precise)
- Height: 36pt minimum (40pt with safe-area on iOS tap targets)
- Pressed: background `#0769B5`, scale 0.98
- Haptic: `.impactOccurred(.light)` on tap

**Secondary Outline Button ("Cancel", "Discard")**
- Background: transparent
- Border: 1pt `#E5E5E5` (light) / `#444444` (dark)
- Text: `#1E1E1E` (light) / `#FFFFFF` (dark), Inter 14pt 500
- Corner radius: 6pt
- Same height/padding as primary

**Tertiary Text Button**
- Text: `#0D99FF` Action Blue, Inter 14pt 500
- No background, 44pt tap slop
- Used in toolbars and lists ("Edit", "Save", "View all")

**Icon Button (toolbar)**
- 32pt square hit area, 18pt SF Symbol or Figma icon inside
- Default: `#757575`
- Hover/press: `#1E1E1E` (light) / `#FFFFFF` (dark) + `#F5F5F5` (light) / `#383838` (dark) background fill
- Corner radius: 6pt

### Cards & Containers

**File Card (the hero component of Recents)**
- Aspect ratio: 16:10 — Figma's exact file-card aspect
- Width: full content width on a 2-col grid (mobile) with 16pt outer margins and 8pt inter-column gap
- Structure: thumbnail preview (full card) → 8pt gap → file name (14pt 500, 1-line truncate) → 2pt → metadata "Edited 2h ago · 4 collaborators" (12pt 400 Secondary)
- Thumbnail background: `#F5F5F5` (light) / `#2C2C2C` (dark) when no preview
- Thumbnail corner radius: 6pt
- When no preview: render the file with a centered Figma cube color (random of the 5) and the first letter of the file name in 28pt 600 white
- Hover/press: background `#F5F5F5`, no shadow
- Tap haptic: `.impactOccurred(.light)`

**Project Card (Recents column)**
- Inline row, 56pt tall
- Layout: 32pt × 32pt cube-color square with the project initial → 12pt gap → project name (15pt 500) + "12 files" metadata (12pt 400 Secondary) → 12pt chevron Secondary
- Tap haptic: `.selectionChanged()`

**Avatar (cube color)**
- Sizes: 24pt, 32pt, 40pt
- Background: randomly assigned from the 5 brand cubes — `#F24E1E`, `#FF7262`, `#A259FF`, `#1ABCFE`, `#0ACF83`
- Initial: white, Inter 14pt 600 (at 24pt) / 16pt 600 (at 32pt) / 18pt 600 (at 40pt)
- Always a perfect circle (50% radius)
- Stack pattern: when showing collaborators, overlap by -8pt, max 3 visible + "+5" pill if more
- The user's own avatar uses their assigned cube color across all surfaces

**Comment Pin (on a frame in mobile mirror)**
- 28pt circle, cube-color background, white "1" / "2" / "3" / etc. number in 13pt 700
- Border: 2pt white (light mode) / 2pt `#1E1E1E` (dark) for legibility over varied frame backgrounds
- Shadow: `rgba(0,0,0,0.16) 0 2px 4px`
- Tap opens the comment thread side sheet

**Comment Thread Sheet**
- Slides up from the bottom or from the right (iPad)
- Background: `#FFFFFF` (light) / `#2C2C2C` (dark)
- Top: 32pt × 32pt original commenter avatar + "Sarah Chen · 2h ago" 13pt 600 + meta 12pt 400 Secondary
- Body: comment text Inter 13pt 400, line-height 1.5
- Replies stack with a 1pt `#E5E5E5` left-rail border 12pt indented
- Reply composer at the bottom: 36pt-tall rounded rectangle with @ mention support and a Send icon button on the right (filled Action Blue when text is present)

**Frame Thumbnail (file detail mirror)**
- Each frame in a file rendered at its native aspect inside a 6pt-corner-radius card
- Background of the card: the actual frame's background color (often white)
- Border: 1pt `#E5E5E5` (light) / `#383838` (dark)
- Tap zooms into the frame in a pinch-zoom mirror view

**Search Bar**
- Background: `#F5F5F5` (light) / `#2C2C2C` (dark)
- Height: 36pt
- Corner radius: 6pt
- Leading 14pt `magnifyingglass` `#757575`
- Placeholder: "Search files and projects" Inter 14pt 400 Tertiary
- Focused border: 1pt `#0D99FF`

### Navigation

**Large-Title Nav Bar**
- Height: variable — 88pt with large title, collapses to 44pt inline
- Background: `#FFFFFF` (light) / `#1E1E1E` (dark)
- Title: Inter 28pt 700 `#1E1E1E` (light) / `#FFFFFF` (dark), left-aligned, 16pt leading inset
- Trailing: user avatar (32pt circle, the user's assigned cube color)
- A subtitle metadata row below the title shows account context: "Personal · 4 teams" in 12pt 400 Secondary

**Bottom Tab Bar**
- Height: 50pt + safe area
- Background: `#FFFFFF` (light) / `#1E1E1E` (dark) with 1pt `#E5E5E5` / `#2C2C2C` top divider
- Tabs (4): Recents, Drafts, Notifications, Profile
- Icon size: 22pt — Figma-styled glyphs (or SF Symbols as substitutes)
- Active tint: `#1E1E1E` (light) / `#FFFFFF` (dark) — Figma's tab is *not* colored; it's a weight/fill swap
- Inactive: `#757575`
- Labels: Inter 11pt 500
- Tap haptic: `.selectionChanged()`

**Editor Bottom Bar (when viewing a file)**
- Floating pill at the bottom: 56pt-tall capsule with three actions — Comment (`bubble.left`), Mirror (`iphone`), Share (`square.and.arrow.up`)
- Background: `#1E1E1E` (always dark even in light mode — this is a tool surface)
- Corner radius: 28pt (full capsule)
- Shadow: `rgba(0,0,0,0.16) 0 4px 12px`

### Input Fields

**Comment Composer**
- Background: `#F5F5F5` (light) / `#2C2C2C` (dark)
- Height: 36pt minimum, grows up to 120pt as content grows
- Corner radius: 18pt (half height)
- Leading 14pt `at.symbol` icon for mentions
- Trailing 28pt circle Action Blue Send button (`paperplane.fill` 14pt white) — appears only when text is present

**Text Input (Generic)**
- Background: `#FFFFFF`
- Border: 1pt `#E5E5E5` default, 2pt `#0D99FF` focused
- Corner radius: 6pt
- Height: 36pt
- Text: Inter 14pt 400 Ink

### Distinctive Components

**The Cube (avatar / logo / brand mark)**
- A 32pt square is the canonical Figma avatar at default size
- Cube colors are randomly assigned per user from the five — the user keeps theirs forever
- For *the Figma brand mark*: render the iconic "F" lockup which is a vertical arrangement of the five colored shapes (red rectangle, orange circle, purple half-circle, blue half-circle, green circle) — this stays at the App Store level

**Cursor (in mirror view)**
- A 12pt × 12pt cursor arrow + the collaborator's name tag below
- Cursor color = the collaborator's cube color
- Name tag: cube color background, white Inter 11pt 500 text, 4pt corner radius, 2pt padding
- The cursor animates with a slight 80ms ease-out lag — Figma's "trailing" effect from the desktop is preserved on mobile mirror

**File Cover (no preview)**
- A 16:10 card filled with a random cube color
- Centered "F" or the first letter of the file name in Inter 32pt 700 white
- Used when a file has no rendered thumbnail yet

**Plan Badge ("Pro", "Org", "Enterprise")**
- A small pill: 18pt height, 4pt radius, 2pt vertical / 6pt horizontal padding
- Background: `#5551FF` (Blurple) for Pro, `#0D99FF` (Action Blue) for Org, `#FFC8C8` (Macaron Pink) with `#1E1E1E` text for Free
- Text: Inter 10pt 600 uppercase white (Pro/Org)

**Empty State Illustration**
- Soft pastel illustrations in the Macaron Pink palette
- Always 200pt × 200pt centered, with copy 16pt below in 15pt 500 Ink and a 14pt 400 Secondary subtitle

## 5. Layout Principles

### Spacing System
- Base unit: 4pt (Figma's literal spacing system uses 4 / 8 / 12 / 16 / 24 / 32 / 48 / 64)
- Scale: 4, 8, 12, 16, 24, 32, 48, 64, 96
- Standard horizontal margin: 16pt on lists, 16pt on Recents grid
- Section vertical gap: 32pt between major sections; 16pt between cards in a grid

### Grid & Container
- Content width: full device width minus 16pt outer margins
- Recents grid: 2-col on iPhone, 3-col on iPhone Pro Max, 4-col on iPad
- Inter-column gap: 8pt on iPhone, 16pt on iPad
- File cards: 16:10 aspect ratio, full grid-cell width

### Whitespace Philosophy
- **Generous outer breathing** — 16pt margins frame everything; 32pt gaps between sections let the dense content breathe
- **Tight inner packing** — within a card, 8pt and 12pt gaps keep the file name, metadata, and thumbnail tightly grouped
- **Edge-defined cards** — 1pt hairline borders separate cards rather than shadows; the canvas is a precise tool, not a soft surface

### Border Radius Scale
- Square (0pt): rare — only on dividers
- Small (4pt): badges, plan pills, hex code chips
- Medium (6pt): file thumbnails, buttons, input fields, search bar — Figma's primary radius
- Standard (12pt): bottom-sheet top corners
- Comfortable (16pt): no major use — Figma stays tight
- Half (50%): cube avatars, comment pins, send button
- Full Pill (500pt): editor bottom bar, plan badges

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, 1pt hairline border | All file/project cards on Recents |
| Subtle (Level 1) | `rgba(0,0,0,0.04) 0 1px 3px` | File card press states |
| Floating (Level 2) | `rgba(0,0,0,0.08) 0 2px 6px` | Comment pins on frames |
| Sticky Element (Level 3) | `rgba(0,0,0,0.16) 0 4px 12px` | Editor bottom bar pill |
| Sheet (Level 4) | `rgba(0,0,0,0.24) 0 -8px 24px` | Comment thread sheet, file detail modal |
| Blur Material | `.regularMaterial` over canvas | Nav bar on scroll, tab bar |

**Shadow Philosophy**: Figma is a precision tool — shadows are quiet and short. Cards rely on 1pt `#E5E5E5` / `#2C2C2C` hairline borders for definition, not elevation. The only meaningfully shadowed elements are the editor bottom bar pill (which floats over content) and comment pins (which need to read over any frame color). In dark mode, borders pull more of the weight because shadows lose contrast on `#1E1E1E`.

### Motion
- **File card tap**: scale 0.98 over 100ms, opens the file detail screen with a 350ms shared-element transition (the thumbnail morphs into the detail header)
- **Comment pin tap**: scale 1.0 → 1.15 → 1.0 spring (response 0.3, damping 0.6), then the comment sheet slides up over 400ms cubic-bezier
- **Cursor trailing in mirror**: 80ms ease-out lag on every position update — feels alive
- **Avatar stack overlap**: when a new collaborator joins, their avatar slides in from the right with a 250ms spring
- **Reply send**: composer text fades, send button bounces (scale 1.0 → 0.9 → 1.0), `.notificationOccurred(.success)` haptic
- **Tab switch**: `.selectionChanged()` haptic, icon weight/fill swap (no animation)
- **Empty state illustration**: slow 4-second loop where one element (e.g., a hand-drawn pencil) gently sways

## 7. Do's and Don'ts

### Do
- Use the five Figma cubes (`#F24E1E`, `#FF7262`, `#A259FF`, `#1ABCFE`, `#0ACF83`) as accent colors on avatars, thumbnails, and cursors — they are the brand
- Use Action Blue `#0D99FF` for primary CTAs and the selection blue in mirror views
- Use Blurple `#5551FF` for Plan upgrade banners, FigJam links, and Slides — *not* for the primary CTA
- Render file cards at 16:10 — Figma's exact aspect
- Use 6pt corner radius on file thumbnails, buttons, input fields — Figma's signature radius
- Use Inter at weights 400/500/600/700 with tabular numerals enabled
- Render hex codes in SF Mono uppercase with a slashed zero ("#FF7262", never "#ff7262")
- Use 1pt hairline borders instead of shadows for card definition
- Randomly assign cube colors to users and persist that assignment — the user is "their cube"
- Use the Macaron Pink palette only for illustration / empty states, never for primary action

### Don't
- Don't use a cube color as a primary CTA — they are avatars/cursors/thumbnails only
- Don't use Action Blue `#0D99FF` as the Figma "Blue cube" — they are different blues with different roles
- Don't render the file thumbnail with a non-16:10 aspect
- Don't replace Inter with a humanist sans — Inter's geometric tabular figures are part of the brand
- Don't lowercase hex codes — Figma is uppercase always
- Don't compress the 16pt outer margins — the breathing room is intentional
- Don't use heavy shadows — Figma is edge-defined; max shadow is `rgba(0,0,0,0.16)`
- Don't break the 4-tab bottom bar — Recents, Drafts, Notifications, Profile are canonical
- Don't use the Figma brand "F" lockup as the in-app logomark on every screen — it appears only at App Store level and in About
- Don't add a 5th cube — there are exactly five colors and they have stayed the same since 2016

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Recents grid 2-col; file thumbnail compresses but keeps 16:10 |
| iPhone 13/14/15 | 390pt | Standard 2-col grid; 16pt outer margins |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected |
| iPhone 15/16 Pro Max | 430pt | 3-col grid possible; comment sheet sits as a side panel on landscape |
| iPad | 768pt+ | 4-col grid; comment sheet always lives as a right-rail side panel; file detail uses split view |

### Dynamic Type
- Comment bodies, file names, metadata: scale up to XL
- **Fixed**: tab labels, hex code chips, dimension labels, avatar initials — layout would break if scaled

### Orientation
- Recents: portrait-locked on iPhone, supports both on iPad
- File mirror: rotates freely on every device — the mirror should match what's on canvas
- Comment threads: portrait on iPhone, side-panel on iPad landscape

### Touch Targets
- File card: full card tap target
- Comment pin: 28pt visual, 44pt hit slop
- Tab bar item: 50pt row, 44pt minimum width
- Avatar in stack: 44pt slop minimum

### Safe Area Handling
- Top: large nav title starts at safe-area top + 16pt
- Bottom: tab bar honors home indicator; editor bottom pill sits 16pt above the home indicator
- Sides: 16pt content insets

## 9. Quick Reference Cheat Sheet

### Color Quick Reference
- **Figma Cubes**: `#F24E1E` Red · `#FF7262` Orange · `#A259FF` Purple · `#1ABCFE` Blue · `#0ACF83` Green
- Action Blue: `#0D99FF`
- Blurple: `#5551FF`
- Macaron Pink: `#FFC8C8`
- Canvas Light: `#FFFFFF`
- Canvas Dark: `#1E1E1E`
- Surface Light 1: `#F5F5F5`
- Dark Surface 1: `#2C2C2C`
- Divider Light: `#E5E5E5`
- Divider Dark: `#2C2C2C`
- Ink: `#1E1E1E`
- Text Secondary: `#757575`
- Success Green: `#14AE5C`
- Warning Amber: `#F2AC2A`
- Error Red: `#E03E1A`

### Example Component Prompts
- "Create a SwiftUI Figma file card: 16:10 aspect ratio, full-width on a 2-col grid with 16pt outer margins and 8pt inter-column gap. The thumbnail is a `#F5F5F5` (light) or `#2C2C2C` (dark) rectangle with 6pt corner radius — when no preview, render a centered Figma cube color (random of the 5) with the file's first letter in Inter 32pt 600 white. Below the thumbnail, 8pt gap, then the file name in Inter 14pt 500 (1-line truncate), 2pt, then metadata 'Edited 2h ago · 4 collaborators' in Inter 12pt 400 `#757575`."
- "Build the Figma avatar component: 32pt circle, background is one of the 5 brand cube colors (`#F24E1E`, `#FF7262`, `#A259FF`, `#1ABCFE`, `#0ACF83`) randomly assigned to the user and persisted. Single white initial in the center, Inter 16pt 600. When showing a stack of collaborators, overlap by -8pt with max 3 visible and a 24pt `+5` pill (background `#F5F5F5`, Inter 11pt 600 `#1E1E1E`) for the remainder."
- "Design the Figma primary CTA: Action Blue `#0D99FF` background, white Inter 14pt 600 text, 10pt vertical and 16pt horizontal padding, 6pt corner radius, 36pt min height. Pressed: bg `#0769B5`, scale 0.98. Haptic `.impactOccurred(.light)` on tap."
- "Build the comment pin: 28pt circle filled with the commenter's cube color, white number ('1', '2', etc.) in Inter 13pt 700 centered. 2pt white border (or 2pt `#1E1E1E` in dark mode) for legibility over varied frame backgrounds. Shadow `rgba(0,0,0,0.16) 0 2px 4px`. On tap: scale 1.0 → 1.15 → 1.0 spring (response 0.3, damping 0.6), then a 400ms cubic-bezier sheet rises with the comment thread."
- "Create the file detail comment composer: 36pt tall minimum, growing up to 120pt as content grows. Background `#F5F5F5` (light) / `#2C2C2C` (dark), 18pt corner radius (half height), leading 14pt `at.symbol` for @mentions, trailing 28pt Action Blue circle with a 14pt white paper-plane icon — appears only when text is non-empty. Inter 13pt 400 input text, placeholder `#757575`."

### Iteration Guide
1. The five Figma cubes are brand accents only — `#F24E1E`, `#FF7262`, `#A259FF`, `#1ABCFE`, `#0ACF83` — never as primary CTAs
2. Primary action is Action Blue `#0D99FF`; Blurple `#5551FF` is reserved for Plan upgrades and FigJam/Slides accents
3. Use Inter at 400/500/600/700 with tabular numerals enabled
4. 6pt corner radius is the Figma signature; 16:10 is the file-card aspect
5. Hex codes are SF Mono, uppercase, slashed zero — "#FF7262"
6. 1pt `#E5E5E5` (light) / `#2C2C2C` (dark) hairline borders instead of shadows
7. Dark mode canvas is `#1E1E1E` — Figma Editor's exact tone
8. The avatar is the user — randomly assigned cube color, persisted forever
9. 4-tab bottom bar: Recents, Drafts, Notifications, Profile
10. Inter at small sizes (11–14pt) carries 80% of the app — information density first
