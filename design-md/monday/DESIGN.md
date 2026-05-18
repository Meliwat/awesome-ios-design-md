# Design System Inspiration of monday.com (iOS)

## 1. Visual Theme & Atmosphere

monday.com's iOS app is a loud, color-coded **work operating system** whose entire personality is the colorful board. The default canvas is white (`#FFFFFF`) in light mode and a deep indigo (`#181B34`) in dark mode — distinctly blue-tinted, NOT neutral black. The app opens to a board: a table of **item rows** grouped into colored sections ("This week", "Next sprint"), where each row is a thin leading **group-color stripe** + an item name + a series of **column cells** (Status, Person, Priority, Date, Numbers, Timeline, Battery). The board IS the product. The Kanban, Timeline, Calendar, and Chart views are alternate renderings of the same item objects.

The signature visual element is the **full-bleed status cell** — a solid, fully-saturated rounded rectangle that fills its entire grid cell with a label inside. "Done" is solid green (`#00C875`), "Working on it" solid orange (`#FDAB3D`), "Stuck" solid red (`#E2445C`), "Not started" a muted navy. Unlike Asana's restrained tint chips, monday.com leans into saturated color slabs — the board reads at a glance because color carries the meaning. The other signature is the **group-color stripe**: every group has an assigned hue, and every item in it wears a 6pt vertical stripe of that color on its leading edge, so the eye instantly buckets work.

The color system is monday.com's famous multicolor palette. The brand triad is **red (`#E2445C`) / orange (`#FDAB3D`) / green (`#00C875`)** — the three dots in the logo. Around them: a primary action **blue (`#0073EA`)**, plus purple (`#A25DDC`), yellow (`#FFCB00`), teal (`#00D2D2`), pink (`#FF158A`), and a deep navy chrome (`#333D6E` / `#181B34`). Every status, label, group, and tag column can use any of these. Status columns ship with a default green/orange/red set; users remap freely. Color is never decorative — it is the data.

Typography is **Figtree** — the geometric, friendly sans monday.com adopted for its product and brand (a Poppins-class face). On iOS it falls back to SF Pro. Hierarchy is bold and confident: 32pt extrabold screen titles, 26pt board titles, 22pt extrabold group headers (each in its group color), 14pt medium item names, 11pt bold status labels (often uppercased). Headings push to weight 800; the type has personality without shouting over the color.

**Key Characteristics:**
- White canvas (`#FFFFFF`) light / deep indigo (`#181B34`) dark — blue-tinted, NOT neutral black
- The colorful board IS the product — grouped item rows × column cells (Status / Person / Priority / Date / Battery)
- Full-bleed status cells — solid saturated slabs (green `#00C875` / orange `#FDAB3D` / red `#E2445C`) carry the meaning
- Group-color stripe — a 6pt leading vertical stripe in the group's hue on every item row
- Colored group headers — "This week" rendered in the group's color, extrabold, with an item count
- Multicolor palette — red / orange / green triad + blue / purple / yellow / teal / pink, fully remappable
- Primary blue (`#0073EA`) — the FAB and primary CTAs (the only "action" color amid the data colors)
- View switcher — Main Table / Kanban / Timeline / Calendar / Chart / Form — same items, different renderings
- Person column — circular assignee avatars inline in a cell
- Battery column — a segmented rollup bar (green/orange/red proportions) summarizing a group's status
- Minimal chrome — bottom tab bar (Home / Boards / Inbox / Search) + per-board view tabs

## 2. Color Palette & Roles

### Primary (Interactive)
- **Primary Blue** (`#0073EA`): The action color — FAB, primary CTAs, active view tab, links. The one "UI" color amid the data colors.
- **Primary Blue Pressed** (`#0060C2`): Pressed state.
- **Blue Tint** (`#0073EA1F`): 12% blue behind selected rows / focused cells.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas, board background.
- **Surface Gray** (`#F6F7FB`): Grouped surfaces, sidebar, hovered rows.
- **Surface Pressed** (`#EDEFF5`): Pressed/selected rows.
- **Divider** (`#E6E9F0`): Hairline grid lines between item rows and columns.
- **Cell Border** (`#D0D4E4`): Inner cell separators in the table grid.

### Canvas & Surfaces (Dark Mode)
- **Night Canvas** (`#181B34`): Primary dark canvas — deep indigo, NOT pure black.
- **Night Surface 1** (`#20243F`): Cards, grouped surfaces.
- **Night Surface 2** (`#292F4C`): Elevated chips, hovered rows, board chrome.
- **Night Divider** (`#353B5C`): Hairline grid lines.
- **Night Cell Border** (`#3D436A`): Inner cell separators in dark.
- **Night Navy Chrome** (`#333D6E`): "Not started" status, neutral chips.

### Text
- **Text Primary Light** (`#323338`): Primary item names, body — monday.com's near-black gray.
- **Text Secondary Light** (`#676879`): Metadata, counts, placeholders.
- **Text Tertiary Light** (`#9699A6`): Disabled, low-emphasis.
- **Text Primary Dark** (`#F5F6F8`): Primary text on night.
- **Text Secondary Dark** (`#A7AFC6`): Metadata on night.
- **Text Tertiary Dark** (`#6B7394`): Disabled on night.
- **On-Color Text** (`#FFFFFF`): Text inside saturated status/priority cells (yellow/light cells flip to `#323338`).

### Multicolor Data Palette
The core monday.com palette — any status, label, group, tag, or color-picker uses these. They appear as **full-bleed solid fills** with white (or dark) text, NOT tints.

| Role | Color | On-Color Text |
|------|-------|---------------|
| Status Red / Stuck | `#E2445C` | `#FFFFFF` |
| Status Orange / Working | `#FDAB3D` | `#FFFFFF` |
| Status Green / Done | `#00C875` | `#FFFFFF` |
| Primary Blue | `#0073EA` | `#FFFFFF` |
| Bright Blue | `#579BFC` | `#FFFFFF` |
| Purple | `#A25DDC` | `#FFFFFF` |
| Dark Purple | `#401694` | `#FFFFFF` |
| Indigo | `#5559DF` | `#FFFFFF` |
| Yellow | `#FFCB00` | `#323338` |
| Teal | `#00D2D2` | `#323338` |
| Pink | `#FF158A` | `#FFFFFF` |
| Bright Green | `#9CD326` | `#323338` |
| Navy (Not started) | `#333D6E` | `#FFFFFF` |
| Stuck Dark | `#BB3354` | `#FFFFFF` |

### Semantic
- **Success Green** (`#00C875`): "Done" status, success toast, positive battery segment.
- **Error / Stuck Red** (`#E2445C`): "Stuck" status, error toast, delete, negative battery segment.
- **Working Orange** (`#FDAB3D`): "Working on it", warning, mid battery segment.
- **Selection Blue** (`#0073EA`): Selected-cell ring, multi-select checkboxes.

## 3. Typography Rules

### Font Family
- **Primary**: `Figtree` (by Erik Kennedy, 2022, SIL OFL) — geometric, friendly sans; the Poppins-class face monday.com uses across product & brand. (monday.com's brand font is "Poppins"/"Figtree"-family; Figtree is the closest OFL-licensed match for free distribution.)
- **Numeric**: Figtree tabular figures for the Numbers column, counts, and dates (`font-feature-settings: "tnum"`).
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **iOS Note**: falls back to SF Pro Text/Display when Figtree isn't bundled.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Figtree | 32pt | 800 | 1.15 | -0.5pt | "My Work", "Home" — large nav title |
| Board Title | Figtree | 26pt | 700 | 1.2 | -0.3pt | Board name in board header (+ color dot) |
| Group Header | Figtree | 22pt | 800 | 1.2 | -0.2pt | "This week" — rendered in the group's color |
| Section | Figtree | 18pt | 700 | 1.3 | -0.1pt | "Board updates", subsection titles |
| Body | Figtree | 16pt | 400 | 1.5 | 0pt | Update posts, descriptions |
| Item Name | Figtree | 14pt | 500 | 1.35 | 0pt | The item/row name (most-read text) |
| Column Header | Figtree | 13pt | 600 | 1.2 | 0.2pt | "Status", "Person", "Priority" headers |
| Meta | Figtree | 14pt | 400 | 1.35 | 0pt | "Updated 2h ago", item counts |
| Status Label | Figtree | 11pt | 700 | 1.1 | 0.2pt | Text inside a status/priority cell |
| Button | Figtree | 15pt | 600 | 1.0 | 0pt | Primary / secondary buttons |
| Text Action | Figtree | 14pt | 600 | 1.0 | 0pt | "Add item", "Add column" |
| Person Initials | Figtree | 9pt | 700 | 1.0 | 0.2pt | Initials inside a person avatar |
| Tab Label | Figtree | 10pt | 600 | 1.0 | 0.1pt | Bottom tab bar labels |
| Update Author | Figtree | 14pt | 700 | 1.3 | 0pt | Update / post author name |

### Principles
- **Bold and confident**: headings push to 800; monday.com's voice is energetic — the type is part of the brand, not neutral.
- **Group headers wear the group color**: the "This week" title is colored, not just black — color is structural.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: regular body, medium item names, semibold UI, bold/extrabold headings & status labels. No thin.
- **Tabular numerals for the data**: Numbers column, dates, counts, battery percentages must not jitter.
- **Status labels are tight & loud**: 11pt/700, often uppercased, white on a saturated fill — legible at board scale.
- **Dynamic Type first-class**: screen/board/group titles, body, item names, updates scale; status labels, tab labels, person initials, column headers stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (New item / Save / Send update)**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#0073EA` (Primary Blue) — identical light & dark
- Text: `#FFFFFF`, Figtree 15pt weight 600
- Padding: 13pt vertical, 26pt horizontal
- Pressed: `#0060C2` + scale 0.98
- Disabled: `#0073EA` at 40% opacity

**Secondary / Outline Button (Invite / Filter)**
- Background: transparent
- Text: `#323338` light / `#F5F6F8` dark, Figtree 14pt weight 600
- Border: 1pt `#E6E9F0` light / `#353B5C` dark
- Corner radius: 8pt
- Padding: 12pt vertical, 20pt horizontal
- Pressed: background `#EDEFF5` / `#292F4C`

**Pill Button (Filter / Sort / Group by / Person)**
- Background: `#F6F7FB` light / `#292F4C` dark
- Border: 1pt `#E6E9F0` / `#353B5C`
- Corner radius: 999pt
- Text: `#323338` / `#F5F6F8`, Figtree 14pt weight 600
- Padding: 10pt vertical, 18pt horizontal

**Text Button (Add item / Add column)**
- Font: Figtree 14pt weight 600
- Color: `#676879` / `#A7AFC6` (or `#0073EA` for primary actions)
- Leading `+` icon 14pt
- Pressed: 60% opacity

**Floating Action Button (Create)**
- Size: 56pt circle
- Background: `#0073EA`
- Icon: white `plus` 24pt, stroke 2.5
- Shadow: `rgba(0,115,234,0.45) 0 8px 20px`
- Position: 18pt from trailing edge, 16pt above the tab bar
- Pressed: scale 0.94 + `#0060C2`

### Board Item Row (the core atom)

Every item in any board view is a row:
- Height: ~48pt
- Leading: 6pt vertical group-color stripe (full row height)
- Item name: Figtree 14pt w500 `#323338` / `#F5F6F8`, truncates with ellipsis
- Column cells (right-aligned sequence): Person (24pt avatar), Status (full-bleed cell), Priority (full-bleed cell), Date, Numbers
- Cell separators: 1pt `#D0D4E4` / `#3D436A`
- Row divider: 1pt `#E6E9F0` / `#353B5C`
- Tap row: opens the item detail panel (updates, activity, columns)
- Swipe-left: Delete (red) + Duplicate (gray)

**Status / Priority Cell**
- Full-bleed: fills the entire grid cell, 4pt corner radius (or 0 inside a tight grid)
- Background: the assigned solid color (e.g., `#00C875`)
- Text: white (or `#323338` on yellow/light), Figtree 11pt w700, centered, often uppercased
- Tap: opens the label picker (a grid of colored options)
- Empty: muted navy `#333D6E` "Not started" / blank gray

**Person Cell**
- One or more 24pt circular avatars (photo or colored-initials)
- Overlapping by -6pt when multiple; "+N" chip for overflow

**Battery Column (group rollup)**
- A horizontal segmented bar (24pt tall, 4pt radius)
- Segments proportional to the group's status distribution: green `#00C875` / orange `#FDAB3D` / red `#E2445C` / navy
- Sits in the group footer summarizing all items

### Group

- Group header bar: a caret (in group color), the group name (Figtree 22pt w800 in the group color), and an item count `#9699A6`
- Tap caret: collapse/expand items with 200ms ease-out
- Footer: "+ Add item" text action + the Battery rollup
- Each item in the group carries the 6pt group-color stripe

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `#FFFFFF` / `#181B34` with 1pt top border `#E6E9F0` / `#353B5C`
- Tabs (4): Home, Boards (My Work), Inbox (Notifications), Search
- Icon size: 22pt; active filled, inactive stroked
- Active color: `#0073EA`; inactive `#9699A6` / `#6B7394`
- Labels: Figtree 10pt w600, always shown

**View Switcher Tabs (per board)**
- Below the board title: Main Table, Kanban, Timeline, Calendar, Chart, Form
- Active tab: `#323338` / `#F5F6F8` text with a 2.5pt blue `#0073EA` underline
- Inactive: `#676879` / `#A7AFC6`
- Figtree 13pt w600, 20pt gap

**Top Nav (board / item detail)**
- Height: 44pt + safe area
- Leading: back chevron / close X
- Center: compact board/item title
- Trailing: ⋯ board actions / share / automation

### Input Fields

**Search Bar**
- Height: 40pt
- Background: `#F6F7FB` / `#292F4C`
- Border: none
- Corner radius: 8pt
- Leading icon `magnifyingglass` 16pt `#9699A6`
- Placeholder: "Search this board" 14pt `#9699A6`
- Focus: 1.5pt `#0073EA` border

**Inline Cell Edit**
- Tapping a text/number cell turns it into an inline field with a 1.5pt `#0073EA` focus ring
- Status/label cells open a colored-option picker grid instead of a text field
- Update composer: `#F6F7FB` / `#292F4C` fill, 8pt radius, "Write an update" placeholder

### Distinctive Components

**Full-Bleed Status Cell + Label Picker**
- The signature — see Board Item Row. Tapping opens a picker: a grid of solid colored option buttons; selecting one fills the cell.

**Colorful Grouped Board**
- The default screen: groups with colored headers, item rows with stripes + cells, FAB bottom-right.

**Kanban View**
- Columns = one status value each; cards carry their group-color stripe + person + key columns
- Drag a card to another column = change its status; lift shadow + 200ms settle

**Battery / Progress Rollup**
- Segmented bar summarizing a group's status mix — the at-a-glance health indicator.

**Item Detail Panel**
- Slides up: item name, all column values as editable rows, an Updates feed (posts + reactions + files), an Activity log

**Inbox Notification Row**
- Leading actor avatar (28pt); bold actor + action; subtitle = item + board; trailing timestamp; unread = blue dot

**Automation Recipe Chip**
- "When status changes to Done, notify person" — a pill row with inline colored status tokens

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 6, 8, 10, 12, 14, 18, 22, 24, 32, 40, 56
- Item row height: ~48pt (dense grid)
- Group header: 12pt top / 8pt bottom
- Screen horizontal inset: 18pt; the group stripe is flush to the leading edge (0 inset)

### Grid & Container
- iPhone: full-width board; column cells right-aligned, horizontally scrollable when many columns
- iPad: master-detail — board list (320pt) + board/item detail
- Kanban columns: ~260pt wide, horizontal scroll, 12pt gap
- Item detail panel: max-width 720pt centered on large screens

### Whitespace Philosophy
- **Color does the work, not whitespace**: the grid is dense because saturated cells separate meaning — no need for airy gaps
- **Full-bleed, not chips**: status/priority fills its whole cell; monday.com deliberately avoids the small-tint-chip look
- **The stripe is the spine**: the 6pt group-color stripe runs the full row height with zero leading inset — it visually threads the group
- **The FAB owns the bottom-right**: a persistent 56pt blue anchor over the scrolling board

### Border Radius Scale
- Square (0pt): grid lines, full-bleed media, tight in-grid cells
- Subtle (4pt): status/priority cells, battery bar, color dots
- Standard (6-8pt): buttons, search bar, cards-in-detail
- Comfortable (12pt): bottom sheets, modals, pickers
- Pill (999pt): filter pills, the FAB
- Circle (50%): person avatars, group caret dots

## 6. Depth & Elevation

monday.com is mostly flat — the color slabs provide separation, so shadows are reserved for floating and dragged elements.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Board grid, item rows, group headers |
| Card (Level 1) | `rgba(0,0,0,0.06) 0 1px 4px` | Kanban cards at rest (light); 1pt border in dark |
| Floating (Level 2) | `rgba(0,0,0,0.14) 0 4px 14px` | Label picker, dropdowns, date picker, the FAB |
| FAB | `rgba(0,115,234,0.45) 0 8px 20px` | Blue FAB — a tinted colored shadow |
| Dragged (Level 3) | `rgba(0,0,0,0.22) 0 8px 24px` | A Kanban card / item being dragged |
| Sheet (Level 4) | `rgba(0,0,0,0.20) 0 -4px 24px` | Bottom sheets (item detail, filters, label picker) |
| Modal Scrim | `rgba(0,0,0,0.45)` | Dim behind modals/sheets |

**Shadow Philosophy**: Shadows mean "liftable / floating", never decoration. The board itself has no shadows — the saturated status cells and the group stripe already encode structure. The FAB gets a soft *blue* shadow tying it to the action color. On dark, gray shadows vanish, so Kanban cards rely on a 1pt `#3D436A` border while the FAB keeps its blue glow.

### Motion
- **Status change**: tapping a cell opens the picker (fade + 4pt slide-up, 150ms); selecting flips the cell color with a 200ms crossfade + soft haptic
- **Drag item between groups / Kanban columns**: 1:1 finger tracking at 60fps; other rows/cards shift over 200ms ease-out; drop snaps with 150ms ease-out + soft haptic; the group stripe recolors on drop
- **Group collapse/expand**: caret rotates 90° in 150ms; items height 0 → auto over 200ms ease-out
- **FAB → new item**: FAB scales up and the new-item sheet rises from bottom over 280ms ease-out
- **Battery rollup**: segments animate width when a status changes (250ms ease-out)
- **View tab switch**: 2.5pt blue underline slides over 200ms; board cross-fades 150ms
- **Confetti / celebration**: completing a board milestone may trigger a brief confetti burst (~1s)
- **Push navigation**: iOS-native push, 300ms horizontal slide
- **Haptic feedback**: soft impact on status change, drag start, group toggle; light tick on tab switch

## 7. Do's and Don'ts

### Do
- Use full-bleed saturated status cells (green `#00C875` / orange `#FDAB3D` / red `#E2445C`) — color carries the meaning
- Give every item a 6pt leading group-color stripe, flush to the edge, full row height
- Render group headers in the group's own color, extrabold, with an item count
- Use the deep indigo `#181B34` for dark canvas — blue-tinted, NOT neutral black
- Reserve blue `#0073EA` for actions only (FAB, CTAs, active tab) amid the data colors
- Keep status/priority cells loud: 11pt/700 white text (or `#323338` on yellow), often uppercased
- Offer the view switcher (Main Table / Kanban / Timeline / Calendar / Chart) over the same items
- Use the Battery segmented bar as the group-health rollup
- Use tabular numerals for the Numbers column, dates, and percentages
- Let users remap any status/label/group color from the full multicolor palette

### Don't
- Don't use small tint chips for status — monday.com is full-bleed solid, not Asana-style 16% tints
- Don't drop the group-color stripe — it's the structural spine of the board
- Don't render group headers in plain black — they wear the group color
- Don't use pure black (`#000000`) for the dark canvas — `#181B34` indigo is correct
- Don't use a data color (green/red/orange) as a primary CTA — blue `#0073EA` is the only action color
- Don't dim status cells in dark mode — they stay fully saturated; meaning must never wash out
- Don't use heavy shadows on board rows — only floating / dragged elements get shadow
- Don't use gray as the FAB shadow — it's a soft blue glow
- Don't make item names bold — 14pt/500 is correct; group headers are the 800-weight moment
- Don't constrain the palette — any column color can be any of the multicolor set

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Fewer columns visible; horizontal scroll for the rest |
| iPhone 13/14/15 | 390pt | Standard layout — name + person + status visible |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | One extra column visible without scroll |
| iPad (portrait) | 768pt | Master-detail: board list (320pt) + board/item detail |
| iPad (landscape) | 1024pt+ | Full multi-column grid; three-pane with sidebar |

### Dynamic Type
- Screen/board/group titles, body, item names, updates: full scale
- Status labels, tab labels, person initials, column headers: FIXED (cell-layout-sensitive)
- Numbers / dates / percentages: scale but stay tabular

### Orientation
- All screens support rotation
- iPad landscape unlocks the three-pane (sidebar / board / item detail)
- Kanban & Timeline scroll horizontally in both orientations

### Touch Targets
- Status / priority cell: full-cell tap (≥ 44pt tall row)
- Tab bar icons: 22pt glyph, 44pt hit
- Item row: full-row tap opens detail
- Group caret: 16pt glyph, 44pt hit
- FAB: 56pt (exceeds minimum)
- Person avatar: 24pt glyph, 44pt hit

### Safe Area Handling
- Top: board title & top nav respect safe area + Dynamic Island
- Bottom: tab bar + home indicator respected; FAB 16pt above the tab bar
- Keyboard: inline cell edit & update composer rise above the keyboard
- Sides: 18pt content inset (stripe flush to the leading edge)

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#181B34`
- Surface (dark): `#20243F` / `#292F4C`
- Divider: `#E6E9F0` light / `#353B5C` dark
- Text primary: `#323338` light / `#F5F6F8` dark
- Text secondary: `#676879` light / `#A7AFC6` dark
- Action blue: `#0073EA` (pressed `#0060C2`)
- Status green / Done: `#00C875`
- Status orange / Working: `#FDAB3D`
- Status red / Stuck: `#E2445C`
- Purple: `#A25DDC`
- Yellow (dark text): `#FFCB00`
- Navy / Not started: `#333D6E`
- On-color text: `#FFFFFF` (or `#323338` on yellow/teal)

### Example Component Prompts
- "Build a monday.com board item row in SwiftUI: a 6pt leading vertical stripe in the group color (full row height, 0 leading inset). Then an HStack: item name Figtree 14pt w500 `#323338` (truncating), a 24pt circular person avatar with white initials, and a full-bleed Status cell — a rounded rect (4pt radius) filling its cell with solid `#00C875` and centered white text 'Done' Figtree 11pt w700 uppercased. 1pt `#D0D4E4` cell separators, 1pt `#E6E9F0` bottom row divider, ~48pt tall. Tap the status cell → open a label picker."

- "Create the monday.com group header: a caret in the group color, the group name 'This week' in Figtree 22pt w800 rendered in that same group color, and an item count Figtree 13pt `#9699A6`. 12pt top / 8pt bottom padding, 18pt leading inset. Tapping the caret collapses items (height 0 → auto, 200ms ease-out)."

- "Render a monday.com status label picker: a bottom sheet with a grid of solid colored option buttons — `#00C875` 'Done', `#FDAB3D` 'Working on it', `#E2445C` 'Stuck', `#333D6E` 'Not started' — each a 4pt-radius full-color rect with centered white (or `#323338` on yellow) Figtree 11pt w700 label. Selecting one flips the cell color with a 200ms crossfade + soft haptic."

- "Build the monday.com Battery rollup: a 24pt-tall, 4pt-radius horizontal bar split into proportional segments — green `#00C875`, orange `#FDAB3D`, red `#E2445C`, navy `#333D6E` — reflecting the group's status distribution. Segment widths animate (250ms ease-out) when an item's status changes."

- "Create the monday.com Kanban view: horizontally scrolling columns (~260pt, `#F6F7FB` / `#292F4C`, 8pt radius), one per status value. Cards carry their group-color stripe + person avatar + key column cells, `#FFFFFF` / `#20243F`, 1pt border, lift shadow on drag (`rgba(0,0,0,0.22) 0 8px 24px`). Dragging a card to another column changes its status; drop snaps 150ms + soft haptic; the stripe/status recolors."

- "Create the monday.com bottom tab bar: 52pt + safe area, `#FFFFFF` / `#181B34`, 1pt top border `#E6E9F0` / `#353B5C`. Four tabs — Home, Boards, Inbox, Search — 22pt icons (filled active, stroked inactive), active `#0073EA`, inactive `#9699A6` / `#6B7394`, labels Figtree 10pt w600 always shown. A 56pt blue `#0073EA` FAB sits bottom-right (18pt trailing, 16pt above tab bar) with shadow `rgba(0,115,234,0.45) 0 8px 20px`."

### Iteration Guide
1. Canvas is white in light, deep indigo `#181B34` in dark — blue-tinted, NOT neutral black
2. Status/priority cells are FULL-BLEED solid saturated slabs — green `#00C875` / orange `#FDAB3D` / red `#E2445C` — never tint chips
3. Every item wears a 6pt leading group-color stripe, flush to the edge, full row height
4. Group headers are rendered in the group's own color, extrabold (22pt/800), with an item count
5. Blue `#0073EA` is the only ACTION color (FAB, CTA, active tab) — the rest of the palette is data
6. The board is dense — color separates meaning, so don't add airy whitespace
7. The Battery segmented bar is the group-health rollup (green/orange/red proportions)
8. Typography is Figtree, bold and confident — item names 14pt/500, group headers 22pt/800, status labels 11pt/700
9. View tabs (Main Table / Kanban / Timeline / Calendar / Chart) render the same items differently — active tab gets a 2.5pt blue underline
10. Dark mode keeps status colors fully saturated (meaning must never wash out), inverts text to `#F5F6F8`, uses 1pt borders where shadows vanish
