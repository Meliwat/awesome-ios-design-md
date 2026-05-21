# Design System Inspiration of Jira (iOS)

## 1. Visual Theme & Atmosphere

Jira's iOS app is a **work-tracking instrument** — dense, structured, and deliberately utilitarian. Where consumer apps chase delight, Jira chases *legibility under load*: a single sprint board can hold dozens of issue cards across five columns, each card carrying an issue type, key, summary, labels, story points, and an assignee avatar. The design system exists to make that density scannable. The atmosphere is "clean engineering tool" — Atlassian's neutral grays, generous internal padding, hairline dividers, and exactly one saturated brand color (Jira Blue `#0052CC`) used sparingly so it always means "primary action."

The signature surface is the **board** — horizontally-scrolling status columns (To Do, In Progress, In Review, Done) each containing a vertical stack of **issue cards**. The issue card is Jira's atomic unit: an 8pt-radius rounded rectangle on a lifted surface, with a one-to-two-line summary in 15pt medium, a row of colored label chips, and a footer row pairing the issue-type icon + issue key (e.g. `MOB-388`) on the left with story points and a circular assignee avatar on the right. Tapping a card pushes the **issue detail** — a full scrollable record with a status lozenge dropdown, fields panel (Assignee, Priority, Sprint, Story points), description, and an activity/comment timeline. Board ⇄ issue detail is the loop the entire app orbits.

The color system is **semantic, not decorative**. Atlassian's design language assigns meaning to color: blue = information / primary, green = success / done, yellow = warning / in-progress-attention, red = blocked / error, purple = epics, plus a neutral gray ramp for everything structural. These appear as **status lozenges** (small uppercase rounded chips: `TO DO`, `IN PROGRESS`, `DONE`, `BLOCKED`), **issue-type icons** (green square = Story, red circle = Bug, blue check = Task, purple lightning = Epic), and **label chips** on cards. The brand blue is reserved for the Create button, links, selected tabs, and primary CTAs — it is never used as a background fill for content.

Typography is **Atlassian Sans** (Atlassian's 2023 proprietary humanist sans; falls back to SF Pro on iOS, with Inter as the closest free analog). Hierarchy is product-UI, not editorial: screen titles at 28-32pt bold, section/board titles at 20-22pt bold, issue summaries at 15-16pt medium, metadata at 12-14pt regular, and the distinctive 11-12pt uppercase letter-spaced labels used for column headers and field names. Numerals are tabular so story-point totals and counts align in columns.

**Key Characteristics:**
- Neutral-gray system — Jira Blue (`#0052CC`) is the only saturated brand color, used only for primary actions
- Board + issue card as the atomic surface — horizontal status columns of vertical card stacks
- Issue card: issue-type icon + key + summary + label chips + story points + assignee avatar
- Status lozenges — small uppercase rounded chips with semantic color (blue / green / red / yellow / neutral)
- Issue-type iconography — green square Story, red circle Bug, blue check Task, purple lightning Epic, gray Sub-task
- Light mode is near-white (`#FFFFFF` / `#F7F8F9`); dark mode is Atlassian neutral charcoal (`#1D2125`), never true black
- Warm-neutral ink `#172B4D` for primary text on light — Atlassian's signature "Navy/ink"
- Uppercase letter-spaced micro-labels (11-12pt) for column headers and field names
- Field-panel + activity-timeline issue detail — status dropdown, assignee, priority, comments
- Backlog list view — sprint sections with collapsible groups and inline issue rows
- Minimal chrome: bottom tab bar (Boards / Backlog / Search / Notifications / You), top breadcrumb + filter row

## 2. Color Palette & Roles

### Primary (Interactive)
- **Jira Blue** (`#0052CC`): Primary brand & action color — Create button, primary CTA fill, selected states, links (light mode).
- **Jira Blue Bold** (`#1868DB`): Brighter interactive blue used on dark surfaces — links, active tab, focus ring.
- **Jira Blue Pressed** (`#09326C`): Pressed state of primary buttons.
- **Link Blue (light)** (`#0C66E4`): Inline hyperlinks and tappable text in light mode.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas — issue detail, modals.
- **Surface Sunken** (`#F7F8F9`): Board background, list background — subtle gray behind cards.
- **Surface Raised** (`#FFFFFF`): Issue cards (white on sunken gray, lifted by 1px border + soft shadow).
- **Surface Hover** (`#F1F2F4`): Pressed/selected rows and hovered cards.
- **Divider** (`#091E4224`): Hairline dividers between rows, fields, and sections (≈14% navy).
- **Border** (`#091E420F`): Card and input borders (≈6% navy).

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#1D2125`): Primary dark canvas — Atlassian neutral, NOT pure black.
- **Dark Surface 1** (`#22272B`): Issue cards, raised surfaces.
- **Dark Surface 2** (`#2C333A`): Columns, input fields, hovered rows.
- **Dark Surface 3** (`#38414A`): Pressed states, neutral lozenge fill.
- **Dark Divider** (`#38414A`): Hairline dividers between rows and sections.

### Text
- **Text Primary Light** (`#172B4D`): Primary body text — Atlassian Navy ("ink"), NOT pure black.
- **Text Subtle Light** (`#44546F`): Secondary text, field labels, metadata.
- **Text Subtlest Light** (`#626F86`): Placeholder, timestamps, low-emphasis.
- **Text Disabled Light** (`#091E424F`): Disabled labels.
- **Text Primary Dark** (`#C7D1DB`): Primary body on dark.
- **Text Subtle Dark** (`#9FADBC`): Secondary text on dark.
- **Text Subtlest Dark** (`#738496`): Placeholder/timestamps on dark.

### Semantic / Status
| Role | Light Fill | Light Text | Dark Fill | Dark Text |
|------|-----------|------------|-----------|-----------|
| Neutral (To Do) | `#DCDFE4` | `#44546F` | `#38414A` | `#9FADBC` |
| Information (In Progress) | `#E9F2FF` | `#0055CC` | `#092957` | `#8FB8F6` |
| Success (Done) | `#DCFFF1` | `#216E4E` | `#1C3329` | `#7EE2B8` |
| Warning (Attention) | `#FFF7D6` | `#7F5F01` | `#3D3000` | `#F5CD47` |
| Danger (Blocked / Error) | `#FFECEB` | `#AE2A19` | `#5D1F1A` | `#FD9891` |
| Discovery (Epic) | `#F3F0FF` | `#5E4DB2` | `#352C63` | `#B8ACF6` |

### Issue-Type Accent Colors
- **Story Green** (`#1F845A` icon on `#FFFFFF` square): Stories.
- **Bug Red** (`#C9372C`): Bugs.
- **Task Blue** (`#1868DB`): Tasks.
- **Epic Purple** (`#8270DB` / `#6E5DC6`): Epics.
- **Sub-task Blue-Gray** (`#5E6C84`): Sub-tasks.

### Label / Avatar Palette
Avatars and labels rotate through Atlassian's accent set: Purple `#5E4DB2`, Teal `#1D7F8C`, Magenta `#943D73`, Orange `#A54800`, Lime `#4C6B1F`, Blue `#0C66E4`.

### Accent Functional
- **Sprint Active** (`#0052CC`): Active sprint pill.
- **Story-Point Badge** (`#DDE1E6` light / `#2C333A` dark): Rounded point counter.
- **Flag/Blocker Stripe** (`#E2483D`): Left edge stripe on flagged cards.

## 3. Typography Rules

### Font Family
- **Primary**: `Atlassian Sans` (Atlassian, 2023) — humanist sans engineered for product UI density and tabular numerals.
- **Mono**: `Atlassian Mono` — used in code blocks and inline code within descriptions/comments.
- **iOS Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'SF Pro Display'`
- **Closest Free Analog**: `Inter` (by Rasmus Andersson, SIL OFL) — geometric humanist with tabular figures and excellent small-size hinting.
- **Fallback Stack**: `'Atlassian Sans', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Inter', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Atlassian Sans | 28-32pt | 700 | 1.2 | -0.4pt | "Your work", "Boards" — top nav large title |
| Board / Section Title | Atlassian Sans | 22pt | 700 | 1.25 | -0.3pt | Board name, backlog header |
| Issue Title (detail) | Atlassian Sans | 20pt | 600 | 1.3 | -0.2pt | Issue summary in the detail screen |
| Subsection | Atlassian Sans | 16pt | 600 | 1.4 | 0pt | "Details", "Activity", "Description" headers |
| Body | Atlassian Sans | 16pt | 400 | 1.5 | 0pt | Description text, comment body |
| Card Summary | Atlassian Sans | 15pt | 500 | 1.4 | 0pt | Issue card title — 2-line clamp |
| Meta / Secondary | Atlassian Sans | 14pt | 400 | 1.4 | 0pt | Timestamps, "Updated by", field values |
| Field Label | Atlassian Sans | 12pt | 600 | 1.3 | 0.4pt | UPPERCASE — "ASSIGNEE", "PRIORITY", "SPRINT" |
| Column Header | Atlassian Sans | 12pt | 700 | 1.3 | 0.6pt | UPPERCASE — "IN PROGRESS", with count badge |
| Lozenge / Label | Atlassian Sans | 11pt | 700 | 1.0 | 0.4pt | UPPERCASE — status lozenges & label chips |
| Issue Key | Atlassian Sans | 11pt | 700 | 1.0 | 0.2pt | Tabular — `MOB-388` |
| Tab Label | Atlassian Sans | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Code Inline | Atlassian Mono | 13pt | 400 | 1.4 | 0pt | `inline code` in descriptions |

### Principles
- **Tabular numerals everywhere**: story points, counts, issue keys, and totals must align — enable `font-variant-numeric: tabular-nums`.
- **Uppercase micro-labels are structural**: 11-12pt uppercase with +0.4-0.6pt tracking signals "this is a field/column/status", not content.
- **Weight ladder 400 / 500 / 600 / 700**: regular body, medium card summaries, semibold subsection/field labels, bold titles & lozenges. No thin, no black.
- **2-line clamp on card summaries**: never let a card grow unbounded — truncate at 2 lines with ellipsis.
- **Dynamic Type on content, fixed on chrome**: titles, body, descriptions scale; lozenges, issue keys, column headers, tab labels stay fixed (column layout is width-sensitive).

## 4. Component Stylings

### Buttons

**Primary Button (Create / Save / primary CTA)**
- Shape: Rounded rectangle, 6pt corner radius (Atlassian uses `3pt` for compact, `6pt` for prominent — prefer 6pt on mobile touch)
- Background: `#0052CC` light / `#1868DB` dark
- Text: `#FFFFFF`, Atlassian Sans 15pt weight 600
- Padding: 12pt vertical, 24pt horizontal
- Pressed: background `#09326C` light / `#0C66E4` dark + 0.98 scale
- Disabled: background `#091E420F` / text `#091E424F`

**Subtle Button (Assign to me / secondary)**
- Background: `#091E420F` light / `#2C333A` dark (subtle neutral)
- Text: `#172B4D` light / `#C7D1DB` dark, weight 500, 14pt
- Padding: 10pt vertical, 18pt horizontal, 6pt radius
- Pressed: background `#091E4224` / `#38414A`

**Outline / Default Button (Add child)**
- Background: transparent
- Border: 1pt `#091E4224` light / `#38414A` dark
- Text: `#172B4D` / `#C7D1DB`, weight 500, 14pt, 6pt radius
- Pressed: background `#F1F2F4` / `#22272B`

**Text / Link Button (View workflow)**
- No background, no border
- Text: `#0C66E4` light / `#579DFF` dark, weight 600, 14pt
- Pressed: underline + `#F1F2F4` faint background

**Icon Button (overflow ⋯ / add)**
- 24pt glyph in 44pt hit area, color `#626F86` / `#9FADBC`
- Pressed: circular `#091E420F` / `#2C333A` background

### Core Atoms

**Status Lozenge**
- Shape: 3pt corner radius pill-rect
- Size: auto-width, 4pt vertical / 8pt horizontal padding
- Text: 11pt weight 700 UPPERCASE, +0.4pt tracking
- Color: semantic pair from §2 table (Neutral / Information / Success / Warning / Danger / Discovery)
- Dropdown variant: trailing 12pt chevron, tap opens status transition menu

**Issue-Type Icon**
- 16pt glyph in a 16-20pt rounded container (3pt radius)
- Story = white square on `#1F845A`; Bug = white circle on `#C9372C`; Task = white check on `#1868DB`; Epic = white lightning on `#8270DB`; Sub-task = link glyph on `#5E6C84`

**Label Chip**
- 3pt radius, 2pt/7pt padding, 10pt weight 700 UPPERCASE
- Tinted pastel background + saturated text from the label palette (e.g. design = `#3B2C5E`/`#C0B6F2` dark)

**Avatar**
- Circle, 22-26pt on cards, 32pt in detail header
- Initials in 10-12pt weight 700 white on a palette color, or photo
- Overlapping stack: -8pt overlap, 2pt canvas-colored border, "+N" overflow chip

**Story-Point Badge**
- Rounded pill (500pt radius), min 18pt, 11pt weight 700
- Background `#DDE1E6` light / `#2C333A` dark, text subtle

### Issue Card

- Surface: `#FFFFFF` light / `#22272B` dark
- Corner radius: 8pt
- Border: 1pt `#091E420F` / `#38414A`
- Shadow: `0 1px 1px rgba(9,30,66,0.13), 0 0 1px rgba(9,30,66,0.16)` light / `0 1px 2px rgba(0,0,0,0.4)` dark
- Padding: 12pt all
- Structure (top → bottom): optional cover/epic-color top stripe → summary (15pt/500, 2-line clamp) → label chips row (8pt gap above) → footer row (12pt gap above): left = issue-type icon + issue key (11pt/700 subtle); right = story-point badge + assignee avatar
- Flagged variant: 3pt left edge stripe `#E2483D` + flag glyph
- Drag state: lifts to elevation 200 (`0 8px 12px rgba(9,30,66,0.15)`), 2° tilt, ghost placeholder in source column

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` / `#1D2125` with 0.5pt top border `#091E4224` / `#38414A`
- Tabs (5): Boards, Backlog, Search, Notifications, You
- Icon: 22-24pt; active fill, inactive stroke
- Active color: `#0052CC` light / `#1868DB` dark; inactive `#626F86` / `#738496`
- Labels: 10pt weight 500, always shown

**Top Bar (board/issue)**
- Height: 44pt + safe area
- Leading: back chevron OR project breadcrumb (12pt subtle)
- Center: board/issue title (compact when scrolled)
- Trailing: filter icon + overflow ⋯ + (board) avatar stack

**Board Column Header**
- 12pt UPPERCASE weight 700 column name + count badge (rounded, subtle bg)
- Sticky at top of each column while its cards scroll
- WIP-limit variant: count turns red `#C9372C` when over limit

**Sprint / Tab Strip (under title)**
- Horizontal pills: active = `#0052CC` filled white text; inactive = `#F1F2F4`/`#2C333A` subtle
- "Sprint 24", "Backlog", "Filters"

### Input Fields

**Text Field (search / inline edit)**
- Height: 40pt
- Background: `#FFFFFF` with 1pt `#091E4224` border light / `#22272B` with 1pt `#38414A` dark (Atlassian fields are bordered, not filled)
- Corner radius: 6pt
- Leading icon (search): `magnifyingglass` 16pt subtle
- Placeholder: 14pt `#626F86` / `#738496`
- Focus: 2pt `#0052CC` / `#1868DB` border ring, no shadow

**Field Row (issue detail Details panel)**
- Label: 12pt UPPERCASE weight 600 subtle, fixed ~110pt leading column
- Value: 14-16pt primary; tappable values open a picker (assignee, priority, sprint, status)
- 44pt row height, hairline divider between fields

**Dropdown / Select**
- Trigger looks like a value with a trailing 12pt chevron
- Menu: 8pt radius card, elevation 300, 40pt rows, selected row checkmark + `#E9F2FF`/`#092957` tint

**Comment Composer**
- Multi-line, bordered, 6pt radius, "Add a comment…" placeholder
- Trailing Save (primary) + Cancel (subtle) on focus; @-mention picker on `@`

### Distinctive Components

**Board (horizontal status columns)**
- Columns scroll horizontally; each column is a fixed ~232-280pt wide vertical card stack
- Column header sticky; "+ Create" affordance at bottom of each column
- Drag-and-drop: long-press a card → lifts → drag across columns → drop transitions issue status with haptic + status lozenge update

**Issue Detail**
- Header: issue-type icon + issue key + breadcrumb, status lozenge dropdown, title (20pt/600), action row (Assign, Watch, Share, ⋯)
- Details panel: collapsible field grid (Assignee, Status, Priority, Sprint, Story points, Labels, Reporter)
- Description block: rich-text body 16pt
- Activity tab strip: All / Comments / History / Worklog
- Comment timeline: avatar + name + timestamp + body, newest-first or oldest-first toggle

**Backlog List**
- Collapsible sprint sections ("Sprint 24 · 12 issues · 34 pts") with chevron
- Inline issue rows: issue-type icon + key + summary + status lozenge + assignee + points
- "Start sprint" / "Complete sprint" primary action per sprint section

**Priority Indicator**
- Up/down chevron stack: Highest (double-up red `#C9372C`), High (up red), Medium (equals orange `#E2B203`), Low (down blue), Lowest (double-down blue) — 14pt glyph

**Filter / Quick Filter Bar**
- Horizontal scroll of toggleable pills (Only my issues, Recently updated, Type, Epic) — selected = `#0052CC` outline + tint

**Sprint Progress Bar**
- Segmented bar: Done (green) / In Progress (blue) / To Do (neutral) proportional to points, with legend

## 5. Layout Principles

### Spacing System
- Base unit: 4pt (Atlassian's `space` scale)
- Scale: 2, 4, 8, 12, 16, 24, 32, 40, 48, 64
- Card internal padding: 12pt
- Card-to-card gap: 8pt
- Column gap: 12pt
- Screen horizontal inset: 16pt
- Field row height: 44pt; section gap: 24pt

### Grid & Container
- iPhone: 16pt horizontal insets; board columns ~232-280pt fixed width, horizontal scroll
- iPad: board shows 3-4 columns without scroll; issue detail uses a 2-pane split (list + detail) at ≥768pt
- Issue detail content: max 700pt centered on large screens
- Backlog rows: full-width, 16pt insets

### Whitespace Philosophy
- **Density with breathing room**: cards are dense internally but separated by clear 8pt gaps and sit on a sunken gray so each reads as a discrete object
- **Hairlines over boxes**: field rows and list rows are separated by 1px dividers, not nested cards
- **Color is rationed**: the canvas and chrome are neutral gray so the few semantic colors (lozenges, type icons, blue CTA) pop and carry meaning
- **Sticky structure**: column headers and the status lozenge stay pinned so context never scrolls away

### Border Radius Scale
- Square (0pt): full-bleed dividers, sprint progress segments
- Tight (3pt): status lozenges, label chips, issue-type icon containers
- Standard (6pt): buttons, input fields, dropdown triggers
- Card (8pt): issue cards, menus, bottom sheets
- Comfortable (12-16pt): modals, large sheets
- Pill (500pt): story-point badges, sprint pills, avatars-as-buttons
- Circle (50%): avatars

## 6. Depth & Elevation

Jira uses Atlassian's restrained elevation system — shadows communicate interaction layers, never decoration.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (0) | No shadow; 1px border only | Board background, list rows, field rows |
| Raised (100) | `0 1px 1px rgba(9,30,66,0.13), 0 0 1px rgba(9,30,66,0.16)` | Issue cards at rest |
| Overlay (200) | `0 4px 8px rgba(9,30,66,0.15), 0 0 1px rgba(9,30,66,0.31)` | Dragged card, hovered card |
| Menu (300) | `0 8px 12px rgba(9,30,66,0.15), 0 0 1px rgba(9,30,66,0.31)` | Dropdowns, status menu, popovers |
| Sheet/Modal (400) | `0 20px 32px rgba(9,30,66,0.15)` + `rgba(9,30,66,0.54)` scrim | Bottom sheets, create-issue modal |

**Shadow Philosophy**: On light mode shadows are tinted with Atlassian Navy (`rgba(9,30,66,…)`) rather than pure black, keeping them cool and crisp. On dark mode shadows are nearly invisible, so elevation is signaled by a lighter surface step (`#22272B` → `#2C333A` → `#38414A`) plus a 1px border on overlays/menus.

### Motion
- **Card drag**: long-press 200ms → card lifts (scale 1.02, elevation 200, 2° tilt) following finger 1:1; sibling cards reflow over 200ms ease-out; drop snaps with 150ms ease-out + medium haptic; status lozenge cross-fades to the new column's status
- **Status transition (lozenge dropdown)**: menu fades + 4pt slide-down 150ms ease-out; on select, lozenge color cross-fades 200ms
- **Screen push (board → issue)**: iOS native push, 300ms horizontal slide
- **Column scroll**: native momentum horizontal scroll with rubber-band; column headers stay pinned
- **Section collapse (backlog)**: chevron rotates 90° in 150ms; rows expand/collapse height 0↔auto 200ms ease-out
- **Pull-to-refresh**: Atlassian spinner; 250ms settle
- **Filter pill toggle**: instant tint + 100ms scale 0.97 tap feedback
- **Sprint progress bar**: segments animate width on load over 400ms ease-out
- **Haptics**: medium impact on card drop & status change; light impact on filter toggle and section collapse
- **Reduce Motion**: replace drag tilt and slide transitions with crossfades; keep instant state changes

## 7. Do's and Don'ts

### Do
- Use Jira Blue (`#0052CC`) ONLY for primary actions, links, and selected states — never as a content background
- Keep the canvas and chrome neutral gray so semantic colors carry meaning
- Render status as a lozenge — uppercase, tracked, semantic-colored, 3pt radius
- Use the correct issue-type icon + color (green Story / red Bug / blue Task / purple Epic / gray Sub-task)
- Pair every card footer with issue-type icon + key on the left, points + assignee on the right
- Clamp card summaries to 2 lines with ellipsis
- Use tabular numerals for points, counts, and issue keys so they align
- Use uppercase 11-12pt tracked micro-labels for columns and field labels
- Use Atlassian Navy `#172B4D` for primary text on light — not pure black
- Use neutral charcoal `#1D2125` for dark mode — not true black
- Brighten interactive blue to `#1868DB` on dark surfaces for AA contrast
- Make the board horizontally scrollable with sticky column headers and per-column Create

### Don't
- Don't use Jira Blue as a card or section background — it must read as "action only"
- Don't introduce new accent colors — the semantic set (blue/green/red/yellow/purple/neutral) is the whole palette
- Don't show status as plain text — always a colored lozenge
- Don't let issue cards grow unbounded — 2-line summary clamp is mandatory
- Don't use pure black text (`#000000`) — `#172B4D` navy ink is correct on light
- Don't use true black (`#000000`) for dark mode — `#1D2125` Atlassian neutral is correct
- Don't mix issue-type colors (e.g. a red Story) — the type→color mapping is fixed
- Don't nest cards inside cards — field/list rows use hairline dividers, not boxes
- Don't proportional-space numerals — points and counts must align in columns
- Don't animate aggressively — Atlassian motion is 150-300ms ease-out, functional
- Don't drop shadows with pure black on light — tint them with `rgba(9,30,66,…)` navy
- Don't hide the issue key — it is the primary reference users speak and search by

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Board columns ~248pt; 1.x columns visible; compact card footer |
| iPhone 13/14/15 | 390pt | Standard layout; ~1.4 columns visible |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top bar |
| iPhone 15/16 Pro Max | 430pt | Wider columns (~280pt); ~1.6 visible |
| iPad (portrait) | 768pt | 3 columns no horizontal scroll; backlog can split list + detail |
| iPad (landscape) | 1024pt+ | 4-5 columns; 2-pane (board/backlog list + issue detail) persistent |

### Dynamic Type
- Scales: screen titles, issue title, body/description, comment text, field values
- Fixed: status lozenges, label chips, issue keys, column headers, tab labels (column width-sensitive)
- Code blocks scale to XL but stay monospace

### Orientation
- iPhone: portrait-primary; landscape shows more columns
- iPad: split-view controller — primary backlog/board list + secondary issue detail; both rotate freely

### Touch Targets
- Issue card: full-card tap (≥64pt tall), long-press to drag
- Status lozenge dropdown: 32pt tall, 44pt hit
- Tab bar icons: 24pt glyph, 44pt hit
- Field row: 44pt full-row tap
- Overflow / icon buttons: 24pt glyph, 44pt hit

### Safe Area Handling
- Top: top bar + breadcrumb respect safe area and Dynamic Island
- Bottom: tab bar + home indicator respected
- Keyboard: comment composer and inline edit lift above keyboard; board scroll preserved
- Sides: 16pt content inset; board columns can bleed to edge on scroll

## 9. Agent Prompt Guide

### Quick Color Reference
- Primary / Jira Blue: `#0052CC` (light) · `#1868DB` (interactive on dark)
- Link blue: `#0C66E4` (light) · `#579DFF` (dark)
- Canvas: `#FFFFFF` light · `#1D2125` dark
- Sunken board bg: `#F7F8F9` light · card surface `#22272B` dark
- Surface raised (card): `#FFFFFF` light · `#22272B` dark
- Divider: `#091E4224` light · `#38414A` dark
- Text primary: `#172B4D` (Atlassian Navy) light · `#C7D1DB` dark
- Text subtle: `#44546F` light · `#9FADBC` dark
- Story green icon: `#1F845A` · Bug red: `#C9372C` · Task blue: `#1868DB` · Epic purple: `#8270DB`
- Lozenge Information: `#E9F2FF`/`#0055CC` light · `#092957`/`#8FB8F6` dark
- Lozenge Success: `#DCFFF1`/`#216E4E` light · `#1C3329`/`#7EE2B8` dark
- Lozenge Danger: `#FFECEB`/`#AE2A19` light · `#5D1F1A`/`#FD9891` dark

### Example Component Prompts
- "Build the Jira issue card in SwiftUI: `#FFFFFF` surface (`#22272B` dark), 8pt corner radius, 1pt `#091E420F` border, elevation-100 navy-tinted shadow, 12pt padding. Top: summary in Atlassian Sans (Inter fallback) 15pt weight 500 `#172B4D`, clamped to 2 lines. Below: 8pt gap then a wrapping row of label chips (3pt radius, 2/7pt padding, 10pt weight 700 uppercase, tinted bg). Footer 12pt above: HStack — leading issue-type icon (16pt, green `#1F845A` rounded square for Story) + issue key `MOB-388` in 11pt weight 700 `#626F86`; trailing story-point pill (`#DDE1E6`, 11pt/700) + 22pt circular assignee avatar with white initials."

- "Create the Jira status lozenge: auto-width pill, 3pt corner radius, 4pt vertical / 8pt horizontal padding, text 11pt weight 700 uppercase +0.4pt tracking. Variants by semantic pair — To Do `#DCDFE4`/`#44546F`, In Progress `#E9F2FF`/`#0055CC`, Done `#DCFFF1`/`#216E4E`, Blocked `#FFECEB`/`#AE2A19` (dark: `#38414A`/`#9FADBC`, `#092957`/`#8FB8F6`, `#1C3329`/`#7EE2B8`, `#5D1F1A`/`#FD9891`). Dropdown variant adds a trailing 12pt chevron and opens a status-transition menu on tap with a 200ms color cross-fade."

- "Render the Jira board: horizontally scrolling columns, each ~248pt fixed width with a sticky header — column name in 12pt weight 700 uppercase `#738496` + count badge (rounded, `#2C333A` bg). Vertical stack of issue cards with 8pt gaps on a `#F7F8F9` sunken background (`#1D2125` dark). Long-press a card to lift it (scale 1.02, elevation 200, 2° tilt) and drag across columns; on drop, snap with 150ms ease-out, medium haptic, and cross-fade the card's status lozenge to the destination column's status."

- "Build the Jira issue detail header: issue-type icon + breadcrumb (`Mobile Apps / MOB`) in 12pt `#626F86`, a tappable status lozenge dropdown (semantic-colored), issue summary in Atlassian Sans 20pt weight 600 `#172B4D`, then an action row of subtle buttons: Assign, Watch, Share, ⋯ (`#091E420F` bg, 6pt radius, 14pt weight 500)."

- "Create the Jira Details field panel: a list of 44pt rows separated by 1px `#091E4224` dividers. Each row: leading fixed ~110pt label in 12pt weight 600 uppercase `#44546F`, trailing tappable value 14-16pt `#172B4D`. Fields: Assignee (avatar+name), Status (lozenge), Priority (chevron indicator), Sprint, Story points, Labels (chips), Reporter. Tapping a value opens an elevation-300 dropdown (8pt radius card, 40pt rows, selected row checkmark + `#E9F2FF` tint)."

- "Render the Jira bottom tab bar: 56pt + safe area, `#FFFFFF`/`#1D2125`, 0.5pt top divider. Five tabs — Boards, Backlog, Search, Notifications, You — 22-24pt icons (active filled, inactive stroked), labels 10pt weight 500 always shown. Active tint `#0052CC` (light) / `#1868DB` (dark); inactive `#626F86`/`#738496`. No pill indicator — just icon fill + color change."

### Iteration Guide
1. Canvas is neutral — `#FFFFFF`/`#F7F8F9` light, `#1D2125` dark (Atlassian charcoal, NOT true black)
2. Text primary is Atlassian Navy `#172B4D` light / `#C7D1DB` dark — never pure black/white
3. Jira Blue `#0052CC` is action-only; brighten to `#1868DB` for interactive elements on dark
4. Status is ALWAYS a semantic lozenge — uppercase, tracked, 3pt radius, color-paired
5. Issue-type icon→color is fixed: green Story / red Bug / blue Task / purple Epic / gray Sub-task
6. The issue card footer pattern is mandatory: type-icon + key (left), points + avatar (right)
7. Clamp card summaries to 2 lines; use tabular numerals for all counts/points/keys
8. Board = horizontal columns of vertical card stacks with sticky headers + drag-to-transition
9. Field/list rows use 1px hairline dividers — never nested cards
10. Motion is functional Atlassian ease-out 150-300ms; shadows are navy-tinted on light, surface-step on dark
