# Design System Inspiration of Things 3 (iOS)

## 1. Visual Theme & Atmosphere

Things 3's iOS app is a study in calm. Where most task managers are dense, colorful dashboards, Things is a quiet white room: a pure white canvas (`#FFFFFF`), a single restrained blue accent, and acres of generous whitespace that let a to-do list feel like a calm sheet of paper rather than an anxiety machine. The design philosophy is "serene productivity" — the interface should disappear so that the user's own intentions are the only thing on screen. Surfaces lift only barely (`#F5F6F8`), dividers are whisper-thin (`#ECECEC`), and there is essentially no chrome: no toolbars crowding the top, no badges screaming for attention. The app feels expensive precisely because it withholds.

The accent is Things Blue `#4F97FF` — a soft, friendly blue used with monastic discipline for exactly three things: the active selection, the primary action, and the checkbox fill on completion. The one other color the app allows is the Today-yellow `#FFD60A` star, the single warm note in an otherwise cool, neutral palette — it marks the "Today" list, the heart of the app, and the star a task earns when scheduled for today. Everything else is ink-black text, soft gray metadata, and white. The restraint is the brand: when something turns blue, it is the one thing you should look at; when the yellow star appears, it means *today*.

Typography is San Francisco (SF Pro) — Apple's system face — used at 400 / 600 / 700 across a 13–28pt range, closely approximated by Inter where SF is unavailable. The hierarchy is built for reading a list like prose: list titles at 28pt bold (the "Today" headline feels like a chapter heading), task titles at 17pt regular, notes at 15pt regular gray. Headings *within* a project are a signature device — a bold 17pt heading with a hairline divider that visually chunks a long project into calm sections. The most expressive typographic moment is simply the generous line height and the air around each task; type here is about breathing room, not density.

**Key Characteristics:**
- Pure white canvas (`#FFFFFF`) with serene, generous whitespace — the calm is the product
- Things Blue (`#4F97FF`) as the single accent — selection, primary action, checkbox fill
- Today-yellow (`#FFD60A`) star — the one warm note, marking the "Today" list and scheduled tasks
- Circular checkbox with a satisfying fill-and-check spring animation on completion
- Magic-Plus button — a floating blue "+" that can be dragged to insert a to-do exactly where you want
- Project pie-progress — a small circular ring that fills as a project's to-dos are completed
- Headings within projects — bold section labels with hairline dividers that chunk long lists calmly
- Sidebar / list navigation (no bottom tab bar) — Inbox, Today, Upcoming, Anytime, Someday, Projects

## 2. Color Palette & Roles

### Primary
- **Things Blue** (`#4F97FF`): Active selection, primary action, the Magic-Plus button, checkbox fill on completion, links.
- **Blue Pressed** (`#3D7FE0`): Active/pressed state for blue controls.
- **Blue Tint** (`rgba(79,151,255,0.10)`): Selected-row wash, subtle highlight behind the active item.

### Today
- **Today Yellow** (`#FFD60A`): The "Today" list star, the star badge on a task scheduled for today — the single warm accent.
- **Yellow Pressed** (`#E6BE00`): Pressed state for the Today star control.

### Canvas & Surface (Light — primary)
- **Canvas** (`#FFFFFF`): Pure white app background — the serene room.
- **Surface 1** (`#F5F6F8`): Grouped section backgrounds, search field, the barely-there lift.
- **Surface 2** (`#ECECEC`): Pressed rows, segmented controls.
- **Divider** (`#ECECEC`): Whisper-thin hairline dividers between groups and under headings.
- **Text Primary** (`#1D1D1F`): Task titles, list headlines, primary text — near-black ink, not pure black.
- **Text Secondary** (`#8A8A8E`): Notes, dates, metadata, project subtitles.
- **Text Tertiary** (`#C7C7CC`): Disabled, placeholder, completed-task strike text, very low emphasis.

### Semantic
- **Checkbox Border** (`#C7C7CC`): The unchecked circular checkbox outline.
- **Completed Check** (`#4F97FF`): The filled checkbox + white checkmark on completion.
- **Deadline Red** (`#FF3B30`): A task past its deadline; the deadline flag when overdue.
- **Today Star** (`#FFD60A`): Scheduled-for-today indicator (shared with Today yellow).
- **Tag Pill** (`#8A8A8E` text on `#F5F6F8`): Neutral tag chips — tags are not color-coded by default.
- **Area Accent**: Areas use a subtle neutral; color is structure, not decoration.

### Dark Mode
Things ships a true dark mode for users who prefer it; the primary identity is the white room.
- **Dark Canvas** (`#1C1C1E`)
- **Dark Surface** (`#2C2C2E`)
- **Dark Divider** (`#38383A`)
- **Dark Text Primary** (`#F2F2F7`)
- **Dark Text Secondary** (`#98989F`)
- Things Blue and Today yellow stay identical — they read well on both.

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro` (Apple system) — Text and Display optical variants, used at 400 / 600 / 700
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'Inter', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Optical sizing**: SF Pro Display at 20pt+ (titles), SF Pro Text below 20pt (rows, notes)

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| List Title (Large) | SF Pro Display | 28pt | 700 | 1.2 | 0.3pt | "Today", "Upcoming" — feels like a chapter title |
| Project Title | SF Pro Display | 24pt | 700 | 1.2 | 0.3pt | Project detail screen hero title |
| Section Heading | SF Pro Text | 17pt | 700 | 1.3 | -0.2pt | A heading inside a project (with hairline divider) |
| Task Title | SF Pro Text | 17pt | 400 | 1.35 | -0.2pt | A to-do row's primary text |
| Task Title (done) | SF Pro Text | 17pt | 400 | 1.35 | -0.2pt | Completed — color tertiary, strikethrough |
| Body / Notes | SF Pro Text | 15pt | 400 | 1.45 | -0.1pt | To-do notes, descriptions |
| List Row Subtitle | SF Pro Text | 14pt | 400 | 1.35 | -0.1pt | Project name under a to-do, area subtitles |
| Metadata | SF Pro Text | 13pt | 400 | 1.3 | 0pt | Date, "3 to-dos", tag text |
| Button (Primary) | SF Pro Text | 17pt | 600 | 1.0 | -0.2pt | "Add to-do", confirm actions |
| Sidebar Item | SF Pro Text | 16pt | 400 | 1.3 | -0.1pt | "Inbox", "Today" sidebar rows (active = 600) |
| Date Pill | SF Pro Text | 13pt | 600 | 1.0 | 0pt | "Today", "Tomorrow" scheduled-date chip |
| Tag Pill | SF Pro Text | 13pt | 400 | 1.0 | 0pt | Tag chip text |
| Count / Meta | SF Pro Text | 15pt | 400 | 1.0 | 0pt | Sidebar count badge, tabular |
| Tiny Label (UPPER) | SF Pro Text | 12pt | 700 | 1.2 | 0.5pt | "THIS EVENING", small caps section labels |

### Principles
- **Weights concentrated at 400 / 600 / 700**: Regular for tasks and notes, semibold for buttons and active sidebar, bold for titles and headings — no light, no black
- **Titles breathe**: List titles at 28pt with positive 0.3pt tracking feel like calm chapter headings, not UI labels
- **Tasks read like prose**: 17pt regular with 1.35 line height and lots of vertical air — this is a reading surface
- **Headings chunk, dividers separate**: A bold 17pt heading + a hairline divider is how a long project stays serene
- **Completed = quiet, not gone**: Done tasks dim to tertiary with a strikethrough — present but receded

## 4. Component Stylings

### Buttons

**Magic-Plus Button (the signature control)**
- Shape: Circle, 56pt diameter, floating bottom-right (or bottom-center) above content
- Background: `#4F97FF`
- Icon: a `plus` glyph, 24pt, `#FFFFFF`
- Shadow: `rgba(79,151,255,0.35) 0 8px 20px` — a soft blue glow
- Pressed: scale 0.94, background `#3D7FE0`, medium haptic
- Drag behavior: press-and-drag lifts the button; a blue insertion line follows the finger so a new to-do drops exactly where released

**Primary Text Button ("Add", confirm)**
- Background: transparent (text-only) or `#4F97FF` filled for modal confirms
- Text: `#4F97FF` (text) / `#FFFFFF` (filled), SF Pro Text 17pt weight 600
- Filled padding: 12pt vertical, 24pt horizontal; corner radius 10pt
- Pressed: text `#3D7FE0` / filled `#3D7FE0`, no scale on text buttons

**Icon Button (toolbar — search, more, calendar)**
- Size: 22pt glyph, 44pt hit target
- Default: `#8A8A8E`
- Active/pressed: `#4F97FF`
- No background; the generous hit area is the affordance

**Today Star Toggle**
- A `star` glyph, 18pt
- Off: outline `#C7C7CC`; On: filled `#FFD60A`
- Tapping animates a quick fill + 1.0 → 1.15 → 1.0 bounce

### Cards & Containers

**To-Do Row (the unit)**
- Min height: 44pt (expands with notes/subtitle)
- Layout: 22pt circular checkbox (leading) → task title (17pt w400) → optional subtitle (14pt) / tag pills / date pill (trailing)
- Background: transparent (canvas `#FFFFFF`)
- Selected: background `rgba(79,151,255,0.10)`, corner radius 8pt, title color unchanged
- Swipe actions: swipe-right to schedule (blue), swipe-left for "when"/move
- Completed: checkbox fills `#4F97FF` with white check; title goes `#C7C7CC` strikethrough; row fades and drops out of the list after a beat

**Circular Checkbox**
- Diameter: 22pt; 1.5pt border `#C7C7CC` when unchecked
- Checked: fills `#4F97FF`, a white `checkmark` scales in with a spring (the signature micro-interaction)
- Tapping anywhere on the checkbox or its 44pt hit area toggles completion with a soft haptic

**Section Heading (inside a project)**
- A bold 17pt `#1D1D1F` label, 24pt top padding, followed by a hairline `#ECECEC` divider
- Tap to collapse/expand the heading's to-dos; a small chevron rotates
- The signature device that keeps long projects calm and scannable

**Project Row (in a list)**
- 56pt row: a pie-progress ring (leading) → project name (17pt w400) → area / count subtitle (14pt secondary)
- The ring fills proportionally to completed to-dos; a fully-complete project shows a solid blue ring with a check

**List Title Header**
- The big 28pt w700 list name ("Today", "Upcoming") with generous 24pt top / 8pt bottom padding
- For "Today", a `#FFD60A` star sits beside the title; for "Upcoming", a small calendar glyph
- No background, no divider — the whitespace is the separation

**Quick-Find / Search**
- A `#F5F6F8` rounded field, 10pt radius, 40pt tall
- Leading `magnifyingglass` 16pt `#8A8A8E`; placeholder "Quick Find" 16pt w400 `#8A8A8E`
- Focus: subtle, no heavy border; caret `#4F97FF`

### Navigation

**Sidebar (no bottom tab bar)**
- A slide-over / left panel listing: Inbox, Today (★), Upcoming, Anytime, Someday, Logbook, then Areas & Projects
- Item height: 40pt; icon 18pt + label 16pt w400
- Active item: background `rgba(79,151,255,0.10)`, label 16pt w600, icon `#4F97FF`; trailing count in 15pt `#8A8A8E`
- Inactive: label `#1D1D1F`, icon `#8A8A8E`
- Opened via the top-left back/menu affordance or an edge swipe; canvas dims `rgba(0,0,0,0.2)` behind

**Top Bar**
- Height: 44pt + safe area
- Leading: back / sidebar affordance ; Trailing: search + more (22pt `#8A8A8E`)
- No center title (the big 28pt list title lives in the scroll content, not the bar)
- Fully transparent over white; on scroll the list title shrinks toward the bar gently

### Input Fields

**To-Do Title Field (composer)**
- Appears inline as a new row or in a card sheet
- Text: 17pt w400 `#1D1D1F`; placeholder "New To-Do" 17pt w400 `#C7C7CC`
- No border, no box — it is just text on white; the checkbox sits to its left
- Below: a notes line (15pt), then a row of quick controls (When, tag, deadline, checklist)

**When / Schedule Picker**
- A calm sheet: "Today" (★ yellow), "This Evening", "Tomorrow", "Upcoming" (calendar), "Someday", "Clear"
- Each row a 44pt tappable item with a leading glyph; the chosen option gets a `#4F97FF` check

**Tag Field**
- Tags render as neutral pills (`#F5F6F8` background, 13pt `#8A8A8E` text, full-pill radius)
- Add-tag opens a calm list of existing tags with a search field

### Distinctive Components

**Magic-Plus**
- The signature creation gesture: a floating 56pt blue "+" that you can tap to add a to-do at the top, or press-and-drag to drop a new to-do precisely between existing rows (a blue insertion line tracks the finger)
- On release it morphs into an inline editable to-do row with the keyboard rising

**Circular Checkbox Fill Animation**
- On completion the 22pt circle fills with `#4F97FF` from center outward over ~180ms, and a white checkmark draws/scales in with a spring (response 0.3, damping 0.6)
- A soft haptic fires on toggle; the completed row then gently fades + collapses out of the list

**Project Pie-Progress**
- A small (18–22pt) circular ring, `#ECECEC` track + `#4F97FF` fill arc proportional to completed to-dos
- It animates the arc forward whenever a to-do in the project is completed — a quiet sense of momentum

**Headings + Hairline Dividers**
- Bold 17pt headings with a thin `#ECECEC` rule that segment a long project into serene chunks
- Collapsible: tapping a heading folds its to-dos with a 200ms height ease

**This Evening Section**
- Within "Today", a calm "This Evening" sub-section (12pt w700 UPPER label + divider) separates evening tasks — a uniquely Things organizing device for the daily rhythm

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 44, 56
- Standard margin: 20pt horizontal (more generous than typical 16pt — part of the calm)
- To-do rows: 12pt vertical breathing inside the 44pt min height; 0pt between rows (whitespace + dividers carry it)

### Grid & Container
- Content width: full device width with generous 20pt horizontal margins
- Single column lists; iPad uses sidebar + list (+ optional project detail) panes
- The Magic-Plus floats above the list, bottom-trailing, clearing the safe area

### Whitespace Philosophy
- **Whitespace IS the product**: Things deliberately uses more air than competitors — generous top padding on list titles, roomy rows, wide margins. The calm is the feature.
- **Restraint over information density**: It would be easy to cram more metadata into a row; Things chooses not to. A to-do is mostly its title and a lot of quiet space.
- **Headings create rhythm**: Long projects stay serene because bold headings + hairline dividers chunk them into digestible sections rather than one dense wall.

### Border Radius Scale
- Sharp (0pt): Hairline dividers
- Soft (8pt): Selected-row highlight, segmented controls
- Standard (10pt): Search field, filled buttons, sheets' inner cards
- Comfortable (14pt): Bottom sheets (When picker, tag picker)
- Circle (50%): Checkbox, Magic-Plus button, pie-progress ring

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | To-do rows, list, canvas — the white room is flat |
| Selection (Level 1) | Background tint only (`rgba(79,151,255,0.10)`), no shadow | The selected to-do row |
| Magic-Plus (Level 2) | `rgba(79,151,255,0.35) 0 8px 20px` (soft blue glow) | The floating + button |
| Sheet (Level 3) | `rgba(0,0,0,0.12) 0 -8px 32px` | When picker, tag picker, modal cards |
| Scrim | `rgba(0,0,0,0.2)` (gentle — Things keeps even dimming soft) | Behind the sidebar and sheets |

**Shadow Philosophy**: Things is almost entirely flat — the white room has no card stacks, no drop shadows on rows, no skeuomorphic depth. The single intentional shadow is the soft blue glow under the Magic-Plus button, which signals "this is the live creation tool." Sheets get a gentle, low-opacity shadow as they rise. Even the modal scrim is unusually light (0.2) because Things never wants to feel heavy or alarming. Elevation is communicated by whitespace and a barely-there tint, not by shadow.

### Motion
- **Checkbox completion**: circle fills center-out ~180ms + white check spring (response 0.3, damping 0.6) + soft haptic; the row then fades + collapses out over 250ms
- **Magic-Plus tap**: scale 0.94 → 1.0 spring (response 0.25, damping 0.7) + `.impactOccurred(.medium)`
- **Magic-Plus drag**: the button lifts (scale 1.05) and a blue insertion line eases to the nearest gap as the finger moves; release drops the new row in with a 200ms settle
- **Heading collapse/expand**: child to-dos animate height over 200ms ease-in-out; chevron rotates
- **Pie-progress**: the fill arc animates forward over 300ms when a project to-do completes
- **Sidebar slide-over**: translateX spring 240ms (response 0.35, damping 0.9) with a soft scrim fade
- **Schedule swipe**: the row reveals a blue action with a rubber-band spring; commit triggers a light haptic

## 7. Do's and Don'ts

### Do
- Use a pure white canvas (`#FFFFFF`) and protect the generous whitespace — the calm is the product
- Reserve Things Blue (`#4F97FF`) for selection, primary action, and the checkbox fill
- Use the Today-yellow (`#FFD60A`) star only for the Today list and scheduled-today markers
- Animate the circular checkbox fill + check spring on completion — it is the signature delight
- Provide the Magic-Plus button with press-and-drag insertion (blue line tracks the finger)
- Chunk long projects with bold 17pt headings and hairline dividers
- Keep list titles big (28pt w700) with roomy top padding so they read like chapter headings
- Show project progress as a quiet circular pie ring that animates forward on completion
- Keep the app flat — the only shadow is the soft blue glow under Magic-Plus

### Don't
- Don't crowd the canvas — resist adding metadata, badges, or chrome that breaks the calm
- Don't introduce extra accent colors — blue for action, the single yellow star for "today"
- Don't color-code tags or projects by default — color is structure here, not decoration
- Don't make completion abrupt — the fill + check spring (and gentle fade-out) is the moment
- Don't add card shadows or skeuomorphic depth — the white room is flat
- Don't shrink the whitespace to fit more rows — density is explicitly not the goal
- Don't use a bottom tab bar — navigation is the sidebar (Inbox / Today / Upcoming / …)
- Don't use heavy modal scrims — even dimming stays soft (≈0.2)
- Don't use light font weights — SF Pro here is 400/600/700 only

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 20pt margins kept; subtitle may truncate before tags |
| iPhone 13/14/15 | 390pt | Standard roomy rows, full 28pt title |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the transparent top bar |
| iPhone 15/16 Pro Max | 430pt | Slightly larger title (30pt), more notes preview |
| iPad | 768pt+ | Sidebar + list (+ project detail) panes; Magic-Plus per pane |

### Dynamic Type
- List titles, task titles, notes: scale fully with Dynamic Type (this is a reading app — honor it)
- Checkbox: fixed 22pt (interaction target, not text)
- Sidebar count badges: tabular, scale modestly
- Tiny section labels ("THIS EVENING"): scale but clamp so the divider rhythm holds

### Orientation
- All lists: **portrait and landscape**
- iPad: landscape unlocks the persistent multi-pane (sidebar + list + detail)
- The Magic-Plus stays bottom-trailing in both orientations

### Touch Targets
- Checkbox: 22pt visual, 44pt hit area
- To-do row: full row tappable (min 44pt)
- Magic-Plus: 56pt — generous and unmissable
- Toolbar icons: 22pt glyph, 44pt hit area
- Sidebar rows: full 40pt height

### Safe Area Handling
- Top: transparent bar respects Dynamic Island / notch; the big title scrolls under it
- Bottom: the Magic-Plus floats above the home indicator with a comfortable inset
- Sides: 20pt content insets (the generous margin is intentional)

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface 1: `#F5F6F8`
- Surface 2 / Divider: `#ECECEC`
- Text primary: `#1D1D1F`
- Text secondary: `#8A8A8E`
- Text tertiary: `#C7C7CC`
- Things Blue (action/selection): `#4F97FF`
- Blue pressed: `#3D7FE0`
- Today yellow (star): `#FFD60A`
- Deadline red: `#FF3B30`

### Example Component Prompts
- "Create a SwiftUI Things to-do row: a 22pt circular checkbox with a 1.5pt #C7C7CC border on the leading side, then the task title 'Email the design feedback' in SF Pro Text 17pt weight 400 #1D1D1F, with a trailing #F5F6F8 'work' tag pill (13pt #8A8A8E, full radius) and a blue 'Today' date pill. The whole row is ≥44pt with 12pt vertical breathing on a pure-white canvas. When checked: fill the circle #4F97FF center-out over 180ms, spring a white checkmark in, dim the title to #C7C7CC with a strikethrough, then fade + collapse the row."
- "Build the Things Magic-Plus button: a 56pt circular button, background #4F97FF, a 24pt white plus glyph, soft blue shadow rgba(79,151,255,0.35) 0 8px 20px, floating bottom-trailing above the list. Tap scales 0.94 with a medium haptic; press-and-drag lifts it (scale 1.05) while a blue insertion line eases to the nearest row gap, and release drops a new editable to-do there."
- "Design the Things 'Today' list header: the word 'Today' in SF Pro Display 28pt weight 700 #1D1D1F with a #FFD60A star beside it, 24pt top padding, 8pt bottom, no divider — surrounded by generous whitespace on white."
- "Create a Things project section heading: a bold SF Pro Text 17pt #1D1D1F label 'Research' with 24pt top padding followed by a 1pt #ECECEC hairline divider; tapping collapses its to-dos with a 200ms height ease and rotates a small chevron."
- "Build the Things project pie-progress ring: a 20pt circle with a #ECECEC track and a #4F97FF arc filled to 60%; when a to-do in the project completes, animate the arc forward over 300ms."

### Iteration Guide
1. Canvas is pure white `#FFFFFF` — protect the generous whitespace, the calm IS the product
2. Things Blue (`#4F97FF`) is the only action color — selection, primary action, checkbox fill
3. The Today-yellow (`#FFD60A`) star is the single warm note — only Today and scheduled-today
4. The circular checkbox fill + spring check (then gentle fade-out) is the signature delight — never make completion abrupt
5. The Magic-Plus is the creation gesture — tap to add, press-drag to insert exactly where you want
6. Chunk long projects with bold 17pt headings + hairline dividers; keep list titles big (28pt w700)
7. The app is flat — the only shadow is the soft blue glow under Magic-Plus; even scrims stay light (≈0.2)
8. Navigation is the sidebar (Inbox / Today / Upcoming / Anytime / Someday) — no bottom tab bar
