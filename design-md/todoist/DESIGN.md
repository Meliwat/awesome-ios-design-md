# Design System Inspiration of Todoist (iOS)

## 1. Visual Theme & Atmosphere

Todoist's iOS app is a quiet, productive workshop. Where most task apps reach for kanban boards, color-coded blobs, and gamified streak meters, Todoist reaches for the opposite — a near-blank canvas, a single column of checkable rows, and the visual restraint of a Moleskine notebook. The brand has been deliberate about this for over a decade: the app is a tool, not a destination. You open it, capture a thought, and close it. Every pixel exists to keep that loop fast.

The accent is Todoist Red (`#DC4C3E`) — the iconic carmine red of the logo, the floating "+" button, the P1 priority flag, and the rare emphatic moment like the karma flame. It is one of the most disciplined uses of a single brand color in productivity software: Red is reserved for verbs (add a task, mark as urgent, the brand mark itself), never used as ambient decoration. Around it sits a ladder of warm grays (`#FAFAFA` surface, `#202020` ink, `#808080` secondary) and four priority colors that punctuate the inbox rhythm: P1 Red `#DC4C3E`, P2 Orange `#EB8909`, P3 Blue `#246FE0`, P4 transparent.

Typography is the iOS system stack — SF Pro Display for nav titles and SF Pro Text for body and rows. Todoist deliberately uses the platform font rather than a custom face: the app is positioned as a respectful native citizen of iOS, and SF Pro's tight, neutral metrics let task content (which is mostly the user's own prose) carry the visual interest. The one exception is project icons — emoji and the Twemoji-style colored glyphs that users choose for "🍎 Groceries" or "📚 Reading List" — which become the most colorful objects on screen.

**Key Characteristics:**
- White canvas (`#FFFFFF`) and warm gray surface (`#FAFAFA`); zero ambient color
- Todoist Red (`#DC4C3E`) reserved for the brand mark, the FAB "+", and the P1 priority flag — verbs only
- Four-tier priority flag system: P1 Red `#DC4C3E`, P2 Orange `#EB8909`, P3 Blue `#246FE0`, P4 outline gray
- Single-column task list — checkbox left, content middle, priority/date/project metadata in a small row beneath
- Round checkbox with priority-color border (empty) → filled with check (complete) → row strikethrough animation
- Quick-add modal triggered by floating "+" — full natural language input ("buy milk tomorrow at 6pm p1 #groceries")
- Today / Upcoming / Inbox / Projects / Filters & Labels — the canonical sidebar hierarchy
- System font (SF Pro Display / Text) at standard iOS weights
- Karma score and streak — small numbers in the profile, never the home screen
- Subtle shadows (`rgba(0,0,0,0.06)`) — the app is whisper-quiet by design

## 2. Color Palette & Roles

### Primary
- **Todoist Red** (`#DC4C3E`): The brand carmine — logomark, the floating "+" FAB, P1 priority flag, primary CTA, link color in marketing.
- **Red Pressed** (`#B53C30`): Active state on the FAB and primary buttons.
- **Red Tint** (`#FBE5E2`): Background tint for the P1 row highlight on overdue tasks, or the selected-state behind a flag.

### Priority Colors
- **P1 Red** (`#DC4C3E`): Highest priority — emergency, must-do-today.
- **P2 Orange** (`#EB8909`): High priority.
- **P3 Blue** (`#246FE0`): Medium priority.
- **P4 Gray** (transparent flag with `#808080` outline): No priority set — the default state.

### Canvas, Surfaces & Dividers
- **Canvas White** (`#FFFFFF`): Primary canvas for the task list and all detail views.
- **Surface Gray** (`#FAFAFA`): Sidebar background, section header fill, project list zone.
- **Surface Gray 2** (`#F0F0F0`): Tap-state highlight on rows, completed-task background tint.
- **Divider** (`#EEEEEE`): Hairline between task rows, between sections, between sidebar items.
- **Shadow Black** (`rgba(0,0,0,0.06)`): Standard whisper shadow on the FAB and quick-add card.

### Text
- **Ink** (`#202020`): Primary text — task content, project names, nav titles.
- **Secondary** (`#808080`): Date metadata, project tag, label text, comment counts.
- **Tertiary** (`#B0B0B0`): Placeholder text, disabled labels, "no due date".
- **Completed Strike** (`#B0B0B0`): The strikethrough color on a checked-off task.

### Semantic
- **Success Green** (`#058527`): Karma "Goal completed!" toast, sync-success indicators.
- **Warning Amber** (`#EB8909`): Sync warnings, P2 flag — shares the priority color.
- **Error Red** (`#D1453B`): Form validation, "Failed to sync" banners.
- **Info Blue** (`#246FE0`): Hyperlinks inside task content, P3 flag.

### Project Color Palette (User-Selectable)
Todoist gives users a fixed 12-swatch palette for projects and labels — these become the project-icon tints and the colored dots in the sidebar:
- **Berry Red** `#B8255F`, **Red** `#DB4035`, **Orange** `#FF9933`, **Yellow** `#FAD000`
- **Olive Green** `#AFB83B`, **Lime Green** `#7ECC49`, **Green** `#299438`, **Mint Green** `#6ACCBC`
- **Teal** `#158FAD`, **Sky Blue** `#14AAF5`, **Light Blue** `#96C3EB`, **Blue** `#4073FF`
- **Grape** `#884DFF`, **Violet** `#AF38EB`, **Lavender** `#EB96EB`, **Magenta** `#E05194`
- **Salmon** `#FF8581`, **Charcoal** `#808080`, **Grey** `#B8B8B8`, **Taupe** `#CCAC93`

### Dark Mode
Todoist's dark mode is a true near-black with warm gray text — engineered for late-night planning sessions.
- **Dark Canvas** (`#1F1F1F`): Primary dark background.
- **Dark Surface 1** (`#282828`): Sidebar, sheets, quick-add card.
- **Dark Surface 2** (`#363636`): Pressed states, completed-task tint.
- **Dark Divider** (`#3D3D3D`): Hairlines.
- **Dark Text Primary** (`#E8E8E8`): Task content, nav titles.
- **Dark Text Secondary** (`#A0A0A0`): Date metadata, project tags.
- **Todoist Red (dark)** (`#E44332`): Slightly brighter for dark mode contrast.

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro Display` for sizes ≥20pt; `SF Pro Text` for body, rows, metadata
- **Weights available**: Regular (400), Medium (500), Semibold (600), Bold (700)
- **Fallback stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Helvetica Neue', Arial, sans-serif`
- **Web/marketing Google Fonts substitute**: `Inter` — closest open-source equivalent in metrics and rhythm; falls back gracefully on the marketing site
- **Monospace** (for the rare code/inline date display in quick-add hints): `SF Mono`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Nav Title | SF Pro Display | 34pt | 700 | 1.15 | -0.4pt | "Today", "Upcoming", "Inbox" |
| Inline Nav Title | SF Pro Display | 17pt | 600 | 1.2 | -0.2pt | Collapsed title after scroll |
| Section Header | SF Pro Text | 13pt | 600 | 1.2 | 0.6pt UPPERCASE | "Overdue", "Today", date dividers |
| Task Content | SF Pro Text | 16pt | 400 | 1.35 | -0.1pt | The main row text — the user's task |
| Task Content (Bold marked) | SF Pro Text | 16pt | 600 | 1.35 | -0.1pt | When task text contains **markdown bold** |
| Sub-task | SF Pro Text | 15pt | 400 | 1.3 | -0.1pt | Indented under a parent task |
| Date Metadata | SF Pro Text | 13pt | 400 | 1.2 | 0pt | "Tomorrow", "Mon 12 Jun", "Today 3:00 PM" |
| Date Overdue | SF Pro Text | 13pt | 500 | 1.2 | 0pt | Same size, weight bumps and color flips to Red |
| Project Tag | SF Pro Text | 13pt | 400 | 1.2 | 0pt | Inline "# Groceries" w/ colored dot |
| Comment Count | SF Pro Text | 13pt | 400 | 1.2 | 0pt | "💬 2" next to the task |
| Sidebar Item | SF Pro Text | 15pt | 500 | 1.25 | 0pt | "Today", "Upcoming", "Inbox" — left rail |
| Project Name (Sidebar) | SF Pro Text | 15pt | 400 | 1.25 | 0pt | User-named project rows |
| Quick-Add Input | SF Pro Display | 17pt | 400 | 1.3 | 0pt | The natural-language entry field |
| Button Label | SF Pro Text | 16pt | 600 | 1.0 | 0pt | "Add task", "Complete", "Schedule" |
| FAB Plus Glyph | SF Pro Display | 28pt | 400 | 1.0 | 0pt | The "+" inside the floating action button |
| Tab Label | SF Pro Text | 10pt | 500 | 1.0 | 0.1pt | Bottom tab bar (iPad/tablet only) |
| Caption | SF Pro Text | 12pt | 400 | 1.3 | 0pt | "No tasks today" empty-state copy |
| Karma Number | SF Pro Display | 28pt | 700 | 1.0 | -0.2pt | Profile screen karma points |

### Principles
- **System font, no exceptions**: Todoist intentionally uses SF Pro to feel native — no custom face, no exotic weights.
- **Weight stack 400 / 500 / 600 / 700**: Regular for task content, Medium for sidebar, Semibold for nav, Bold for the largest title only.
- **Tight tracking at display sizes**: -0.4pt at 34pt, -0.1pt on task content, 0 at body.
- **Date metadata is the second-loudest element** after task content — it's color-coded (overdue red, today green, future gray) but weight stays at 400 unless overdue.
- **Section headers are always 13pt SEMIBOLD UPPERCASE with 0.6pt tracking** — this is the section-divider rhythm used across Today / Upcoming / Projects.
- **Markdown in task content is honored**: `**bold**` and `_italic_` render inline at the same 16pt size with weight 600 / italic style.
- **Strikethrough on completion**: text color shifts to `#B0B0B0`, weight stays the same, line-through stroke is 1pt at midline.
- **Dynamic Type respected on task content, sidebar, and large titles**; fixed on section headers, date metadata, tab labels, and the FAB glyph.

## 4. Component Stylings

### The Task Row (The Hero Component)

The single most important component in Todoist. Built for a list of 5, 50, or 500 tasks with consistent rhythm and zero visual noise.

- **Height**: 52pt minimum; expands to fit wrapped content
- **Layout**: 16pt leading padding → 22pt circular checkbox → 12pt gap → task content column → trailing chevron / count icons → 16pt trailing padding
- **Checkbox**:
  - 22pt circle, 1.5pt stroke
  - Empty + P4: `#B0B0B0` stroke, transparent fill
  - Empty + P3: `#246FE0` stroke (Blue), `rgba(36,111,224,0.08)` fill
  - Empty + P2: `#EB8909` stroke (Orange), `rgba(235,137,9,0.08)` fill
  - Empty + P1: `#DC4C3E` stroke (Red), `rgba(220,76,62,0.10)` fill
  - Complete (any priority): filled with the priority color, white check glyph `checkmark` 12pt
  - Hit area: 44pt
- **Task content column**:
  - Line 1: SF Pro Text 16pt Regular `#202020` — task content
  - Line 2 (metadata row, only if any meta exists): horizontal row with — date pill (left), project tag with colored dot (right), comment icon + count, label chips
- **Date pill**: text only — no background. Color: green `#058527` for today, red `#DC4C3E` for overdue, gray `#808080` for future
- **Project tag**: small 8pt colored dot from the project's color + `# Project Name` in SF Pro Text 13pt
- **Trailing**: a `chevron.right` 14pt `#B0B0B0` if the task has subtasks/comments
- **Tap states**: row background fades to `#F0F0F0` for 80ms; on complete, the checkbox fills, then the entire row strikes through and animates out with a 250ms ease — the row collapses upward, the next row slides up
- **Long-press**: drag-to-reorder with the row lifted at `rgba(0,0,0,0.12) 0 4px 12px` shadow
- **Swipe right**: schedule date — reveals a green `#058527` zone with a calendar icon; lift to confirm "Tomorrow"
- **Swipe left**: reveals two zones — yellow `#EB8909` (postpone) and gray (more)
- **Dividers**: 0.5pt `#EEEEEE` between rows, full-width minus 64pt leading inset (aligned with task content, NOT the checkbox)

### The Floating Add Button (FAB — The Icon)

- **Diameter**: 56pt
- **Position**: bottom-right, 24pt inset from right edge, 24pt above the tab bar (or 88pt above safe area on phones without a tab bar)
- **Background**: `#DC4C3E` solid Todoist Red
- **Glyph**: "+" SF Pro Display 28pt Regular, color `#FFFFFF`, dead-centered
- **Shadow**: `rgba(220,76,62,0.35) 0 6px 16px` — the shadow is a tinted red, NOT generic black; this is a signature detail
- **Pressed**: background `#B53C30`, scale 0.94, shadow tightens to 4px
- **Tap haptic**: `.impactOccurred(.medium)` on tap; on quick-add commit `.notificationOccurred(.success)`

### Quick-Add Card (The Natural-Language Input)

- **Trigger**: tap on the FAB → modal sheet slides up from the bottom with spring damping
- **Container**: white card, 16pt corner radius (top corners only on partial sheet), 16pt padding, full content width
- **Title row**: "Add task" in SF Pro Display 17pt Semibold, project context in Secondary if applicable
- **Input**: multi-line text field, SF Pro Display 17pt Regular `#202020`, placeholder "Take out the trash today p1 #home" in `#B0B0B0`
- **Smart parsing chips** (appear inline as the user types matches): "tomorrow" → blue date chip; "p1" → red priority chip; "#groceries" → project chip with color dot
- **Bottom action bar** (left-aligned, horizontal scroll): Date · Priority · Project · Labels · Reminders — each a 32pt-tall pill button with icon + label
- **Right side**: "Add task" pill button — disabled `#F0F0F0` `#B0B0B0` text until input has content, enabled `#DC4C3E` `#FFFFFF` once it does
- **Cancel**: drag-down or tap outside; haptic `.selectionChanged()` on each smart-parsed chip

### Sidebar (iPad and "Menu" Drawer on iPhone)

- **Width**: 280pt on iPad; full-screen drawer on iPhone
- **Background**: Surface Gray `#FAFAFA`
- **Header row**: 64pt — user avatar (40pt circle), name SF Pro Text 16pt Semibold, premium badge if applicable
- **System filters** (always at top): Inbox, Today, Upcoming, Filters & Labels — each a 44pt row, 24pt SF Symbol icon in `#202020`, label 15pt Medium, count badge `#808080` 13pt right-aligned
- **Today** count badge: when > 0 and includes overdue, badge is `#DC4C3E` background `#FFFFFF` text
- **Projects header**: "My Projects" SF Pro Text 13pt Semibold UPPERCASE `#808080` with 0.6pt tracking, "+" button to add project on right
- **Project row**: 44pt, 12pt colored dot (project color) + project name 15pt Regular + count badge
- **Sub-projects**: indented 16pt with a thin tree line connecting to parent
- **Selected state**: row background `rgba(220,76,62,0.08)` (red 8% tint), 3pt leading red bar
- **Drag to reorder**: 250ms scale-up on long-press

### Empty States

- **Today (no tasks)**: cartoon illustration of a calm illustration (a person reading a book in a hammock), 200pt square, centered. Heading "Looks like the day is yours." SF Pro Display 22pt Semibold. Subhead "No tasks scheduled for today." SF Pro Text 15pt `#808080`. Below: a "Plan my day" link in `#DC4C3E`.
- **Inbox empty**: smaller illustration, "Your Inbox is empty" — celebrating the zero-inbox moment.

### Cards & Containers

**Project Detail View**
- Top of screen: large project name title (34pt Bold), color dot inline
- Below: section toggles (List / Board) — Board is reserved for premium
- Sections within a project: collapsible — "Section name" 18pt Semibold `#202020`, chevron-down/up 14pt right, divider 0.5pt below

**Date Picker Sheet**
- 50% sheet from bottom
- Calendar grid — 7-col, day numbers in SF Pro Display 16pt Regular `#202020`
- Today: red ring 2pt outline, no fill
- Selected date: filled `#DC4C3E` circle, white number
- Quick-pick chips above the calendar: "Today · Tomorrow · This Weekend · Next Week" — each a 32pt-tall outlined pill

### Buttons

**Primary CTA ("Add task", "Save", "Complete")**
- Background: `#DC4C3E`
- Text: `#FFFFFF`, SF Pro Text 16pt Semibold
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: 8pt
- Height: 44pt minimum
- Pressed: background `#B53C30`, scale 0.97
- Haptic: `.impactOccurred(.medium)`

**Secondary Outline Button ("Cancel", "Reschedule")**
- Background: transparent
- Border: 1pt `#EEEEEE`
- Text: `#202020`, SF Pro Text 16pt Semibold
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: 8pt

**Text-Only Action ("Skip", "Remove date")**
- No background or border
- Text: `#DC4C3E`, SF Pro Text 15pt Regular
- 44pt hit target

**Smart Chip (in quick-add)**
- Background: tint of the chip color at 12% (e.g., `rgba(36,111,224,0.12)` for blue date chip)
- Text: solid chip color, SF Pro Text 13pt Medium
- Corner radius: 6pt
- Padding: 4pt vertical, 8pt horizontal
- Dismissable via tap → "x" icon appears

### Navigation

**Large-Title Nav Bar**
- Height: 96pt collapsed → 44pt scrolled
- Background: `#FFFFFF`, blurs to `.regularMaterial` when content scrolls beneath
- Title: SF Pro Display 34pt Bold `#202020`
- Leading: hamburger menu icon (iPhone) — 22pt SF Symbol `line.horizontal.3`
- Trailing: 22pt sort/filter icon `slider.horizontal.3`

**Section Header (within a day's tasks)**
- Height: 36pt
- Sticky on scroll
- Label: SF Pro Text 13pt Semibold UPPERCASE `#808080`, 0.6pt tracking
- For "Overdue" section: label is `#DC4C3E`, with a small chevron to collapse
- Background: `#FFFFFF` with 0.5pt divider below

**Tab Bar (iPad — Sidebar replaces this on phones)**
- Tabs: Today, Upcoming, Search, Browse — 4 tabs
- Active color: `#DC4C3E`, inactive `#808080`
- Tab labels visible always, SF Pro Text 10pt Medium

### Distinctive Components

**Priority Flag Glyph**
- Used on every task to indicate priority — appears as a small flag icon (SF Symbol `flag.fill`) at 14pt
- P1: filled `#DC4C3E`
- P2: filled `#EB8909`
- P3: filled `#246FE0`
- P4: outlined `flag` (not filled), color `#B0B0B0`
- Appears in the swipe-action UI and as a tap option in the metadata bar of quick-add

**Karma Flame**
- Small flame icon in the profile, SF Pro display 14pt — animates with a subtle 800ms ember pulse when streak is active
- Color: a gradient from `#FFB400` (yellow) at the base to `#DC4C3E` (red) at the tip

**Streak Indicator**
- Number + flame in the profile header, 24pt SF Pro Display Bold `#202020` for the number
- Tap opens karma history

**Sub-task Tree Lines**
- 1pt vertical line in `#EEEEEE` connecting indented subtasks to their parent
- Drawn from the parent checkbox down, with a 12pt horizontal stub to the subtask's checkbox

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 56, 64
- Standard horizontal margin: 16pt on phones, 24pt on iPad
- Vertical spacing between tasks: 0 (they share dividers); 16pt between sections; 32pt between groups (e.g., Today / Tomorrow)

### Grid & Container
- Content width: full device width minus 0pt horizontal margins (rows are edge-to-edge)
- Task rows: single column, full width
- Sidebar: 280pt on iPad, full-screen drawer on iPhone

### Whitespace Philosophy
- **Density is the point**: Todoist is one of the densest task apps — a 52pt row with content at 16pt and metadata at 13pt fits 12+ tasks on an iPhone screen
- **Vertical rhythm via dividers, not gaps**: hairline 0.5pt `#EEEEEE` between rows; rows themselves are touching
- **Sections breathe with 16pt header padding** above the section title and 8pt below before the first task
- **The FAB is the only floating element** — everything else is inline

### Border Radius Scale
- Square (0pt): task row backgrounds, dividers
- Small (4pt): smart-parsing chips in quick-add, badge backgrounds
- Standard (8pt): primary buttons, quick-add input, date picker date cells
- Medium (12pt): bottom sheets, popovers
- Comfortable (16pt): modal sheet top corners, quick-add card
- Full Pill (500pt): action chips in quick-add bottom bar, count badges
- Circle (50%): checkboxes, project color dots, FAB

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Task rows, section backgrounds, canvas |
| Card (Level 1) | `rgba(0,0,0,0.06) 0 1px 4px` | Quick-add card, date picker, popovers |
| Floating (Level 2) | `rgba(220,76,62,0.35) 0 6px 16px` | **The FAB only** — a tinted red shadow, not black |
| Overlay (Level 3) | `rgba(0,0,0,0.12) 0 8px 24px` | Sheets, modals on tap |
| Sheet (Level 4) | `rgba(0,0,0,0.16) 0 -8px 24px` | Bottom sheet (quick-add, date picker) |
| Blur Material | `.regularMaterial` over `#FFFFFF` at 92% | Nav bar when content scrolls beneath |

**Shadow Philosophy**: Todoist is content-first and almost flat. The only meaningfully elevated element is the FAB, which gets a TINTED RED shadow (not generic black) to reinforce its identity — this is one of the most-copied details in the app. Everything else uses whisper shadows (0.06 opacity) or none at all. Cards don't pop; they sit.

### Motion
- **Checkbox tap**: 200ms ease-out — fill color animates from transparent to priority color, check glyph fades in from 0 to 1
- **Task complete**: 80ms hold → 250ms ease-out — entire row strikes through, fades to 60% opacity, then collapses upward; the next row slides up
- **Task uncomplete (undo)**: reverse animation, row slides back into place
- **Swipe schedule (right swipe)**: row drags right, green zone reveals, lift to confirm — `.notificationOccurred(.success)` haptic
- **FAB tap**: scale 1.0 → 0.94 → 1.0 over 150ms, `.impactOccurred(.medium)` haptic
- **Quick-add sheet appear**: spring damping 0.8, response 0.4s, slides from bottom
- **Smart-parsing chip appear**: 200ms ease-out, scale 0.8 → 1.0 with subtle fade
- **Karma flame pulse**: 800ms ease-in-out, opacity 0.7 → 1.0, infinite when streak active
- **Project drag-reorder**: long-press 400ms → scale 1.0 → 1.03, shadow lifts to Level 2

## 7. Do's and Don'ts

### Do
- Use Todoist Red `#DC4C3E` for the FAB, the brand mark, the P1 flag, and primary CTAs — verbs only
- Give the FAB a tinted red shadow `rgba(220,76,62,0.35) 0 6px 16px` — not generic black
- Use the four-tier priority system exactly as defined: P1 `#DC4C3E`, P2 `#EB8909`, P3 `#246FE0`, P4 gray outline
- Use SF Pro Display for display sizes and SF Pro Text for body — no custom face
- Keep the task list edge-to-edge with 0.5pt `#EEEEEE` dividers; the row is the layout
- Use the colored-dot pattern for projects — 8pt dot from the project color + "# Name" in 13pt
- Honor Markdown in task content — `**bold**` and `_italic_` render inline
- Use `#202020` for primary text — slightly warm, never pure black
- Use Surface Gray `#FAFAFA` for the sidebar and section header zones
- Use the strikethrough + fade-to-`#B0B0B0` pattern for completed tasks
- Animate task completion with a 250ms collapse-upward — the next row slides up

### Don't
- Don't use pure black `#000000` for body text — `#202020` is the correct value
- Don't use Todoist Red for ambient decoration — it's reserved for the brand mark, FAB, P1, and primary CTAs
- Don't add color blocks behind task rows — task rows are always white, the color comes from the priority checkbox stroke and the project dot
- Don't replace the FAB shadow with a generic black drop-shadow — the tinted red shadow is signature
- Don't use a custom font — Todoist is intentionally a native iOS citizen with SF Pro
- Don't introduce kanban boards on phones — Todoist is a vertical list on iPhone; Board view is reserved for iPad and premium
- Don't gamify the home screen with karma — karma lives in the profile, never the task list
- Don't widen the project-color palette — stick to the 20-swatch system palette so user projects are recognizable
- Don't use heavy shadows — only the FAB gets meaningful elevation
- Don't compress the row below 52pt — touchability is non-negotiable
- Don't show priority flags larger than 14pt — they punctuate, they don't shout

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Sidebar is a full-screen drawer triggered by hamburger; FAB pinned bottom-right |
| iPhone 13/14/15 | 390pt | Standard layout — single-column task list, FAB bottom-right |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected in nav bar |
| iPhone 15/16 Pro Max | 430pt | Slightly wider task rows; same hierarchy |
| iPad Mini | 744pt | Sidebar appears at 280pt, task list takes the remaining column |
| iPad Pro 11" | 834pt | 3-column layout: sidebar + task list + detail pane for selected task |
| iPad Pro 13" | 1024pt | 3-column persistent, detail pane shows full task notes/comments |

### Dynamic Type
- Large nav titles, task content, sidebar items, subtask: full scale
- Section headers, date metadata, project tags, tab labels, FAB glyph: fixed (layout-sensitive)
- Quick-add input: scales but clamps at 22pt max

### Orientation
- iPhone: portrait-locked
- iPad: rotates — sidebar persistent in landscape, collapses to overlay in portrait
- Quick-add modal works in both orientations

### Touch Targets
- Task row: 52pt minimum height — the whole row is tappable, with the checkbox as a separate hit zone
- Checkbox: 44pt hit area on a 22pt glyph
- FAB: 56pt — full-button hit
- Sidebar row: 44pt
- Smart chip: 32pt height, 8pt internal padding, hit area expands to 44pt vertically

### Safe Area Handling
- Top: safe area honored — large nav title starts at safe-area top + 16pt
- Bottom: FAB sits 24pt above tab bar or 88pt above home indicator if no tab bar
- Sides: 0pt horizontal margin on rows; 16pt on nav and FAB inset

## 9. Quick Reference Cheat Sheet

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface Gray: `#FAFAFA`
- Divider: `#EEEEEE`
- Ink (primary text): `#202020`
- Secondary text: `#808080`
- Tertiary / Completed: `#B0B0B0`
- **Todoist Red** (FAB / P1 / brand): `#DC4C3E`
- **Red Pressed**: `#B53C30`
- **P2 Orange**: `#EB8909`
- **P3 Blue**: `#246FE0`
- Success Green: `#058527`
- Error Red: `#D1453B`
- Project dot: any of the 20-swatch project palette

### Example Component Prompts
- "Create a SwiftUI Todoist task row: 52pt tall, with a 22pt circular checkbox on the left (P1 red `#DC4C3E` stroke, transparent fill when empty; filled solid `#DC4C3E` with a white check when complete). To the right of the checkbox, task content in SF Pro Text 16pt Regular `#202020` ('Buy milk and eggs'). Below the content, a metadata row with a date pill 'Today' in `#058527` green and a project tag with a small 8pt blue dot and '# Groceries' in SF Pro Text 13pt Regular `#808080`. The whole row has a 0.5pt `#EEEEEE` bottom divider inset 64pt from the left."
- "Build the Todoist floating action button: 56pt circle, position bottom-right with 24pt insets, background solid `#DC4C3E`. The '+' glyph is SF Pro Display 28pt Regular white, dead-centered. Critically: the shadow is `rgba(220,76,62,0.35) 0 6px 16px` — a tinted red shadow, not black. On press, scale to 0.94 and shadow tightens. Haptic `.impactOccurred(.medium)`."
- "Design the quick-add card: 50%-height bottom sheet with 16pt top corner radius, white background. At the top, 'Add task' in SF Pro Display 17pt Semibold. Below, a multi-line text input in SF Pro Display 17pt Regular with placeholder 'Take out the trash today p1 #home' in `#B0B0B0`. As the user types matches, render inline smart-parsing chips: date matches get a blue `rgba(36,111,224,0.12)` background pill with `#246FE0` text; '#projectname' gets a tinted chip with the project color dot. Bottom action bar with 32pt outlined pills for Date · Priority · Project · Labels · Reminders. Right side: 'Add task' button in `#DC4C3E`."
- "Create the section header for 'Today' tasks: 36pt tall, sticky on scroll, white background with 0.5pt `#EEEEEE` bottom divider. Label is 'TODAY' in SF Pro Text 13pt Semibold UPPERCASE `#808080` with 0.6pt tracking. For an 'Overdue' header, the label color flips to `#DC4C3E` and gets a small chevron to collapse."
- "Build the priority flag swipe action: when the user swipes left on a task row, reveal a horizontal action zone. The left half is an orange `#EB8909` zone with a small `flag.fill` icon and 'Postpone'; the right half is gray with 'More'. Lift to confirm. On swipe right, reveal a green `#058527` zone with 'Today' for instant scheduling. Haptic `.notificationOccurred(.success)` on confirm."

### Iteration Guide
1. Canvas is `#FFFFFF`; sidebar and section backgrounds are Surface Gray `#FAFAFA`
2. Todoist Red `#DC4C3E` is reserved for verbs — FAB, P1, primary CTAs, brand mark
3. Priority is communicated via the checkbox stroke color, NEVER the row background
4. The FAB shadow is tinted red `rgba(220,76,62,0.35) 0 6px 16px` — this is the signature
5. Use SF Pro Display ≥20pt and SF Pro Text ≤17pt — no custom face
6. Rows are 52pt tall, edge-to-edge, divided by 0.5pt `#EEEEEE` hairlines inset 64pt
7. Completed task: strikethrough + color `#B0B0B0` + 250ms collapse-upward animation
8. Quick-add is a bottom sheet — natural-language input with inline smart-parsing chips
9. Dark mode is `#1F1F1F` canvas with `#E8E8E8` text; Todoist Red brightens slightly to `#E44332`
10. Keep it whisper-quiet — Todoist is a tool, not a destination
