# Design System Inspiration of Trello (iOS)

## 1. Visual Theme & Atmosphere

Trello's iOS app is a tactile, board-first workspace where the content *is* the layout. Unlike most apps, Trello has no single canvas color — the **board background** sets the mood. A board can be a solid Trello Blue (`#0079BF`), a gradient, or a photo, and the entire screen takes on that backdrop. On top of it float pale, near-white lists (`#F1F2F4`) holding white cards. The feeling is of physical index cards pinned to a colored corkboard: you scroll the board horizontally through columns, you drag cards between them, and everything has a slight lift. There is no bottom tab bar — navigation lives in a top bar and the board itself is the primary surface, scrolled left-to-right.

The accent system is restrained against a backdrop that may be loud. Trello Blue for actions sits at `#0C66E4` (the modern Atlassian-era blue, distinct from the classic board blue `#0079BF`), used for the primary button, links, and the active state. Card label colors (a fixed palette of green/yellow/orange/red/purple/blue swatches) are the one place color runs free — they're functional tags, not decoration. Everything else is neutral ink (`#172B4D`) on white cards so cards stay readable regardless of how vivid the board behind them is.

Typography is the system font (SF Pro on iOS; Inter is a clean substitute) — Trello leans on platform type for legibility at small sizes, because a card title must be readable over any backdrop. The hierarchy is compact and weight-driven: 11–24pt at 400 / 600 / 700. The most expressive moment is a **card lifting on drag** — it scales up slightly, gains a strong shadow, and the list beneath shows a placeholder gap as you move it.

**Key Characteristics:**
- No fixed canvas — the **board background** (solid `#0079BF`, gradient, or photo) sets the screen
- Pale lists (`#F1F2F4`) of white cards floating on the board backdrop
- Horizontal board scroll through columns — no bottom tab bar
- Trello Blue (`#0C66E4`) for actions; the classic `#0079BF` is the default board color
- Card label color row — a fixed functional swatch palette (the one place color is free)
- Card lift on drag — scale up, strong shadow, placeholder gap in the list
- Top navigation bar (board name + members + menu) — board is the primary surface
- System font (SF Pro / Inter), weights 400 / 600 / 700
- Card detail opens as a sheet over the dimmed board

## 2. Color Palette & Roles

### Primary
- **Trello Blue (Action)** (`#0C66E4`): Primary CTA, links, active tab/segment, focus ring, "Create" button. The modern Atlassian-era action blue.
- **Action Blue Pressed** (`#0055CC`): Pressed/active state for blue actions.
- **Action Blue Tint** (`#E9F2FF`): Selected-row wash, info banners, blue chip background (on white surfaces).
- **Classic Board Blue** (`#0079BF`): The default board background color (the heritage Trello blue) — a backdrop, not an action color.

### Board Backgrounds (Backdrop)
- **Board Blue** (`#0079BF`): Default solid backdrop.
- **Board Surface Scrim** (`rgba(0,0,0,0.16)`): A subtle dark scrim laid over photo backgrounds so white lists/cards keep contrast.
- **Board Header Tint** (`rgba(255,255,255,0.16)`): Translucent white used for header chips/buttons sitting directly on a colored board.

### Surfaces & Text
- **Canvas White** (`#FFFFFF`): Card fill, sheet backgrounds, the readable surface on top of any board.
- **List Surface** (`#F1F2F4`): The pale list/column container that floats on the board.
- **Surface Sunken** (`#EBECF0`): Pressed list area, skeleton placeholders, card-composer field.
- **Divider** (`#DFE1E6`): Hairline rules between card sections and within sheets.
- **Border Strong** (`#C1C7D0`): Input outline at rest, checklist separators.
- **Text Primary** (`#172B4D`): Card titles, list titles, primary ink (a deep navy-charcoal).
- **Text Secondary** (`#5E6C84`): Metadata, badges, due dates, member counts, helper text.
- **Text Tertiary** (`#8993A4`): Disabled labels, placeholders, low-emphasis captions.
- **Text on Board** (`#FFFFFF`): The board name and header controls sitting directly on a colored backdrop.

### Card Label Palette (Functional — the one free-color zone)
- **Label Green** (`#4BCE97`) / subtle `#BAF3DB`
- **Label Yellow** (`#F5CD47`) / subtle `#F8E6A0`
- **Label Orange** (`#FEA362`) / subtle `#FEDEC8`
- **Label Red** (`#F87168`) / subtle `#FFD5D2`
- **Label Purple** (`#9F8FEF`) / subtle `#DFD8FD`
- **Label Blue** (`#579DFF`) / subtle `#CCE0FF`

### Semantic
- **Success Green** (`#1F845A`): Completed checklist, "done" badges.
- **Due Soon Amber** (`#B65C02`): Due-date badge approaching.
- **Overdue Red** (`#C9372C`): Overdue due-date badge.
- **Info Blue** (`#0C66E4`): Informational emphasis (same as action blue).

### Dark Mode (Supported)
Trello has a real dark mode for the UI chrome; board backgrounds still drive the board area.
- **Dark Canvas / Sheet** (`#1D2125`)
- **Dark List Surface** (`#22272B`)
- **Dark Card** (`#2C333A`)
- **Dark Text** (`#B6C2CF`)
- **Action Blue (dark-adjusted)** (`#579DFF`)

## 3. Typography Rules

### Font Family
- **Primary**: System font — `SF Pro Text` / `SF Pro Display` on iOS (Trello uses the platform font for maximum legibility on any backdrop)
- **Fallback / Web Substitute**: `'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Proportional in most UI; tabular only where counts align (checklist progress)
- **CJK/Non-Latin**: System stack handles all scripts — Trello ships globally

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen / Sheet Title | SF Pro Display | 24pt | 700 | 1.2 | -0.3pt | Card detail title, modal title |
| Board Name | SF Pro Display | 20pt | 700 | 1.2 | -0.2pt | In the top bar, on the board backdrop |
| Section Header | SF Pro Text | 17pt | 700 | 1.3 | -0.1pt | "Checklist", "Activity" in card detail |
| List Title | SF Pro Text | 15pt | 600 | 1.3 | 0pt | Column header ("To Do", "Doing") |
| Card Title | SF Pro Text | 15pt | 600 | 1.35 | 0pt | The text on a card, up to ~4 lines |
| Body | SF Pro Text | 15pt | 400 | 1.45 | 0pt | Card description, comments |
| Button (Primary) | SF Pro Text | 16pt | 600 | 1.0 | 0pt | "Create", "Save" pill/button |
| Meta / Badge | SF Pro Text | 12pt | 600 | 1.2 | 0pt | Due date, member count, checklist x/y |
| Subtitle | SF Pro Text | 13pt | 400 | 1.3 | 0pt | "Updated 2h ago", list card count |
| Label (UPPER) | SF Pro Text | 11pt | 700 | 1.2 | 0.4pt | "MEMBERS", section labels in detail |
| Card Composer | SF Pro Text | 15pt | 400 | 1.4 | 0pt | The "Add a card" inline input |
| Caption | SF Pro Text | 11pt | 400 | 1.3 | 0pt | Timestamps, low-emphasis legal |

### Principles
- **Legibility over personality**: System font is intentional — a card title must read over a photo or a vivid color
- **Compact, weight-driven**: 11–24pt, hierarchy via 400/600/700, not big size jumps — boards pack many cards
- **Navy-charcoal ink, not black**: Primary text is `#172B4D` on white cards
- **Color is functional, not typographic**: Label colors carry meaning; type stays neutral ink
- **On-board text is white with a scrim**: The board name and header controls use white plus the dark scrim for contrast
- **Dynamic Type respected** on card titles, body, sheet content; badges and labels fixed

## 4. Component Stylings

### Buttons

**Primary CTA ("Create" / "Save")**
- Background: `#0C66E4`
- Text: `#FFFFFF`, SF Pro Text 16pt weight 600
- Height: 44pt; corner radius 8pt (rounded rect — Trello buttons are not full pills)
- Pressed: background `#0055CC`, subtle scale 0.98
- Disabled: background `#091E420F` (faint), text `#8993A4`

**Secondary / Subtle Button ("Cancel")**
- Background: `#091E420F` (a faint neutral wash on white) or transparent on sheets
- Text: `#172B4D`, weight 600
- Height: 44pt, radius 8pt
- Pressed: `#091E4224`

**On-Board Button (header chips: Filter, Members)**
- Background: `rgba(255,255,255,0.16)` (translucent over the colored board)
- Text/icon: `#FFFFFF`
- Height: 32pt, radius 8pt
- Pressed: `rgba(255,255,255,0.28)`

**Text Link**
- SF Pro Text 15pt weight 600, color `#0C66E4`, no underline; pressed `#0055CC`

**Icon Button (card actions, overflow)**
- 20–24pt glyph, 44pt hit area
- Default `#5E6C84` on white; `#FFFFFF` on board
- Active `#0C66E4`

### Cards & Containers

**Card (the core object)**
- Background: `#FFFFFF`, corner radius 8pt
- Shadow at rest: `rgba(9,30,66,0.13) 0 1px 1px, rgba(9,30,66,0.06) 0 0 1px`
- Structure (top→bottom): label color row (4pt-tall pills or full chips) → title (15pt w600 `#172B4D`, up to 4 lines) → badge row (due date, checklist x/y, comments, member avatars)
- Pressed: background `#F1F2F4`
- Dragging: scale 1.03, shadow `rgba(9,30,66,0.25) 0 12px 24px`, slight rotation optional; a placeholder gap (`#091E4224` dashed/filled) opens in the list

**List / Column**
- Background: `#F1F2F4`, corner radius 12pt, width ~272pt, top-aligned
- Header: list title 15pt w600 `#172B4D` + card count + overflow (⋯)
- Body: vertically stacked cards with 8pt gaps, scrolls vertically within the column
- Footer: "+ Add a card" composer (tap expands an inline `#FFFFFF` field with a `#0C66E4` "Add card" button)
- Lists scroll horizontally as a row across the board

**Label Chip / Row**
- Compact mode: 8pt-tall colored bars across the card top (color only)
- Expanded mode: full chips with label text in dark ink on the subtle tint (e.g., text on `#BAF3DB`)
- Tap a label in card detail to toggle; the six-color functional palette

**Card Detail Sheet**
- Bottom sheet `#FFFFFF` (or full-screen on compact), top grabber, radius 16pt top corners, presented over a dimmed board (`rgba(0,0,0,0.4)`)
- Sections: cover/labels, title (24pt w700), description, checklist (with a `#1F845A` progress bar), activity/comments
- Action row: Members, Labels, Checklist, Due date, Attachment, Move

**Board Header**
- Sits on the board backdrop: board name 20pt w700 `#FFFFFF`, member avatars (28pt circles), Filter chip, overflow — all using the translucent-white on-board style with the scrim ensuring contrast

### Navigation

**Top Bar (no bottom tab)**
- Trello deliberately has **no bottom tab bar**; the board is the primary surface
- On a board: back chevron, board name (20pt w700 white on backdrop), member avatars, search/filter, overflow menu
- App-level: a top bar with Boards / Home / Notifications / Search reached via the menu or a segmented header — but inside a board, the board owns the screen and scrolls horizontally
- Board switching: a left-edge drawer or a "Boards" list screen

**Board Horizontal Scroll**
- The defining navigation: lists are a horizontally-paged/scrolling row; the last list shows a peek + an "+ Add list" affordance
- Smooth horizontal momentum scroll; vertical scroll happens inside each list

**Card Composer**
- Inline at the list footer: tapping "+ Add a card" expands a white multiline field with a `#0C66E4` "Add card" button and a close (×); Enter adds and keeps the composer open for rapid entry

### Input Fields

**Card Composer Field**
- Background: `#FFFFFF`, radius 8pt, 1pt `#DFE1E6`
- Placeholder "Enter a title for this card…" 15pt `#5E6C84`
- Focus: 2pt `#0C66E4` ring
- Multiline, grows with content

**Search Bar**
- Background: `rgba(255,255,255,0.16)` on board / `#F1F2F4` on white screens
- Height 40pt, radius 8pt
- Leading `magnifyingglass` 18pt (white on board, `#5E6C84` on white)

**Text Field (card detail / settings)**
- Filled, background `#EBECF0`, height 44pt, radius 8pt
- Rest: no border; focus: 2pt `#0C66E4`
- Error: 2pt `#C9372C`, helper `#C9372C`

### Distinctive Components

**Horizontal Kanban with Drag**
- The signature interaction: lists arranged left-to-right, cards drag vertically within and horizontally across lists. While dragging, the card lifts (scale 1.03 + strong shadow), the source position collapses, and a destination placeholder opens — the whole model is "move the physical card".

**Board-Background Header**
- The board's background (solid `#0079BF`, gradient, or photo) bleeds behind the header and lists; a subtle scrim keeps white content readable. The backdrop *is* the board's identity.

**Card Label Row**
- The fixed six-color functional palette shown as bars or chips on the card top — the one place Trello lets color run, because here color is data.

**Card Detail Sheet**
- The card expands into a structured sheet (title, description, checklist with progress, activity) over the dimmed board — the "open the card" moment.

**Inline List Add / Card Composer**
- Add-a-list and add-a-card are inline, rapid, and keep focus — boards are built fast, in place.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56
- List width: ~272pt; gap between lists: 8pt; board horizontal padding: 8pt
- Card internal padding: 8pt 12pt; gap between cards in a list: 8pt
- Card detail content margin: 16pt

### Grid & Container
- Board: a horizontally scrolling row of fixed-width (~272pt) lists, top-aligned
- Lists scroll vertically internally; the board scrolls horizontally
- Card detail: single-column sheet, 16pt margins

### Whitespace Philosophy
- **Dense board, calm card**: Lists pack cards tightly (8pt gaps) so many are visible; the card *detail* sheet is roomy
- **The backdrop breathes the color**: Whitespace on a board is the board background itself — lists are islands of calm pale surface
- **Cards stay readable**: White cards with navy ink guarantee legibility no matter how vivid the backdrop

### Border Radius Scale
- Soft (4pt): Label bars, tiny chips
- Standard (8pt): Cards, buttons, inputs, on-board chips
- Comfortable (12pt): Lists/columns
- Sheet (16pt): Card detail sheet (top corners)
- Circle (50%): Member avatars, icon-button hit circles

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Board (Level 0) | Backdrop (color/photo) + optional scrim | The board background itself |
| List (Level 1) | `rgba(9,30,66,0.08) 0 1px 2px` | Pale list columns floating on the board |
| Card (Level 2) | `rgba(9,30,66,0.13) 0 1px 1px, rgba(9,30,66,0.06) 0 0 1px` | White cards at rest |
| Card Dragging (Level 3) | `rgba(9,30,66,0.25) 0 12px 24px` | The lifted card while being dragged |
| Sheet (Level 4) | `rgba(9,30,66,0.31) 0 16px 40px` | Card detail sheet over the dimmed board |
| Scrim | `rgba(0,0,0,0.40)` | Dim behind the card detail sheet / menus |

**Shadow Philosophy**: Shadows use Trello's signature navy tint (`rgba(9,30,66,…)`) rather than pure black, so they read correctly on both pale lists and colored backdrops. Elevation is meaningful: a card at rest has a whisper-thin shadow; the moment you pick it up the shadow jumps dramatically — depth communicates "this is now in your hand". Lists have just enough shadow to lift off the backdrop.

### Motion
- **Card lift on drag**: scale 1.0 → 1.03, shadow ramps to the Level 3 value over ~120ms, source collapses, destination placeholder animates open (~160ms)
- **Card drop**: settles into the gap with a short spring (~220ms), shadow returns to rest
- **List/board scroll**: native horizontal momentum; vertical inside lists
- **Card detail present**: sheet slides up ~300ms ease, board dims behind
- **Composer expand**: "+ Add a card" expands the inline field ~180ms ease
- **Checklist toggle**: checkbox fills `#1F845A`, progress bar animates width ~200ms

## 7. Do's and Don'ts

### Do
- Let the **board background** (solid `#0079BF`, gradient, or photo) define the screen — there is no fixed canvas
- Float pale lists (`#F1F2F4`) of white cards on the backdrop
- Use Trello Blue `#0C66E4` for actions/links; reserve the classic `#0079BF` for the default board color
- Keep navy ink (`#172B4D`) on white cards so they read over any backdrop
- Use the fixed six-color label palette as functional tags — the one place color is free
- Lift the card dramatically on drag (scale 1.03 + strong navy-tinted shadow) and open a placeholder gap
- Scroll the board horizontally through lists; do **not** add a bottom tab bar
- Apply a subtle scrim over photo backdrops so white content keeps contrast
- Use the system font for card titles — legibility on any backdrop is the point

### Don't
- Don't impose a single canvas color — the board backdrop owns the screen
- Don't add a bottom tab bar — Trello navigates via the top bar and the horizontal board
- Don't tint card text with label colors — labels are bars/chips; titles stay navy ink
- Don't use pure black shadows — use the navy `rgba(9,30,66,…)` tint so they work on color and pale surfaces
- Don't make the drag lift subtle — picking up a card must feel like lifting a physical card
- Don't use full-pill buttons — Trello buttons are 8pt rounded rects
- Don't let lists be transparent — they're pale solid islands so cards stay readable
- Don't use a custom display font for card content — system font is intentional for legibility
- Don't animate the board scroll with custom easing — native momentum only

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | ~1.1 lists visible, list width ~256pt, card detail full-screen |
| iPhone 13/14/15 | 390pt | ~1.2 lists visible, list width ~272pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the on-board top bar |
| iPhone 15/16 Pro Max | 430pt | ~1.4 lists visible, more peek of the next list |
| iPad | 768pt+ | Multiple full lists visible at once; card detail as a centered modal, not full-screen |

### Dynamic Type
- Card titles, body, sheet content: full scale (cards grow taller to fit)
- Badges, label chips, list count: fixed
- Board name: scales modestly, truncates with ellipsis

### Orientation
- iPhone: board supports both, but is **primarily portrait**; landscape shows more lists
- iPad: landscape ideal — many lists across; card detail centered modal

### Touch Targets
- Card: full card is tappable (open detail); long-press initiates drag
- Drag handle: the whole card; 44pt minimum effective grab
- On-board chips/buttons: 32pt height with 44pt effective hit
- Checklist checkboxes: 24pt glyph, 44pt hit
- Overflow / icon buttons: 44pt hit area

### Safe Area Handling
- Top: the on-board header respects safe area / Dynamic Island; the board scrolls beneath
- Bottom: no tab bar — the board and the card composer respect the home indicator
- Sides: 8pt board padding; card detail sheet uses 16pt content margins

## 9. Agent Prompt Guide

### Quick Color Reference
- Board backdrop (default): `#0079BF`
- List surface: `#F1F2F4`
- Card: `#FFFFFF`
- Divider: `#DFE1E6`
- Text primary: `#172B4D`
- Text secondary: `#5E6C84`
- Action blue (CTA/links): `#0C66E4`
- Action blue pressed: `#0055CC`
- Label green / yellow / orange: `#4BCE97` / `#F5CD47` / `#FEA362`
- Label red / purple / blue: `#F87168` / `#9F8FEF` / `#579DFF`
- Card shadow tint: `rgba(9,30,66,…)`

### Example Component Prompts
- "Create a SwiftUI Trello card: white background, 8pt corner radius, shadow rgba(9,30,66,0.13) 0 1px 1px. Top: a row of three 8pt-tall label bars (#4BCE97, #F5CD47, #579DFF). Title 'Design the onboarding flow' in SF Pro Text 15pt weight 600 #172B4D, up to 4 lines. Badge row: a due-date pill (#B65C02 text), a checklist badge '3/8', a comment count, and two 24pt member avatars. Pressed background #F1F2F4."
- "Build a Trello list/column: background #F1F2F4, 12pt corner radius, width 272pt, top-aligned. Header: list title 'In Progress' 15pt weight 600 #172B4D + a card-count badge + overflow ⋯. Body: vertically stacked white cards with 8pt gaps. Footer: a '+ Add a card' row that, when tapped, expands an inline white multiline field with a #0C66E4 'Add card' button."
- "Design the Trello board view: a horizontally scrolling row of #F1F2F4 lists on a board background of solid #0079BF (no bottom tab bar). The on-board top bar has the board name in SF Pro Display 20pt weight 700 white, 28pt member avatars, and a translucent rgba(255,255,255,0.16) Filter chip. The last list peeks with an '+ Add list' affordance."
- "Animate a Trello card drag: on long-press the card scales 1.0 → 1.03 and its shadow ramps to rgba(9,30,66,0.25) 0 12px 24px over 120ms; the original slot collapses and a filled placeholder gap (#091E4224) opens at the drop target over ~160ms. On drop, the card settles with a 220ms spring and the shadow returns to rest."
- "Build the Trello card detail sheet: a #FFFFFF bottom sheet with a top grabber, 16pt top-corner radius, over a rgba(0,0,0,0.4) dimmed board. Title in SF Pro Display 24pt weight 700 #172B4D, a description block, a checklist with a #1F845A progress bar, and an action row (Members, Labels, Checklist, Due date, Attachment, Move)."

### Iteration Guide
1. There is no fixed canvas — the **board background** (solid `#0079BF`, gradient, or photo) sets the screen
2. Float pale `#F1F2F4` lists of white cards on the backdrop; cards keep navy `#172B4D` ink for legibility over anything
3. Action blue is `#0C66E4` (CTA, links, active); the classic `#0079BF` is the default *board* color, not an action color
4. Card label colors are the only free-color zone — a fixed six-color functional palette (bars or chips), never applied to text
5. No bottom tab bar — navigate via the top bar; the board scrolls horizontally through lists
6. The drag lift is the signature motion: scale 1.03 + a strong navy-tinted shadow + a destination placeholder gap
7. Shadows use the navy tint `rgba(9,30,66,…)` so they read on both color backdrops and pale lists; elevation jumps when a card is picked up
8. Buttons are 8pt rounded rects (not pills); apply a subtle scrim over photo backdrops so white content stays readable
