# Design System Inspiration of Asana (iOS)

## 1. Visual Theme & Atmosphere

Asana's iOS app is a calm, structured productivity surface whose entire personality comes from one warm color and a disciplined list. The default canvas is white (`#FFFFFF`) in light mode and a near-neutral charcoal (`#1E1F21`) in dark mode — Asana's signature dark, NOT pure black. The app opens to **My Tasks**: a vertically scrolling list of task rows, each with a leading circular checkbox, a task name, and a row of small metadata pills (project, due date, assignee avatar). This list IS the product. Everything else — the project board, the timeline, the inbox — is a different rendering of the same task objects.

The one piece of chrome that defines Asana visually is the **completion circle**. Every task row begins with a 20pt hollow circle that, when tapped, fills with the success green (`#62D26F`) and draws a white checkmark with a soft "celebration" animation (occasionally a flying unicorn or narwhal "celebration creature" on full-section completion). This circle is the emotional core of Asana — the moment of marking something done is deliberately satisfying. The other signature moment is the **coral floating action button (FAB)** — a 56pt circular `#F06A6A` button bottom-right that creates a new task from anywhere.

The color system is built around **Asana Coral** (`#F06A6A`) — a soft, warm red-pink that is the single brand accent. It is identical in light and dark mode. Around it sits a **multicolor object palette**: every project, tag, and custom field can be assigned one of a fixed set of hues — Plum Blue (`#4573D2`), Aqua (`#4ECBC4`), Green (`#62D26F`), Yellow (`#F8DF72`), Orange (`#F1BD6C`), Magenta (`#F26FD3`), Indigo (`#5A3FFF`), and more. In the UI these appear as low-opacity tint chips: a green pill with green text on a 16% green background, an aqua "project" tag, etc. Project **status updates** use a four-state semantic system: On track (aqua/green), At risk (yellow), Off track (red), Complete (green). Asana avoids heavy fills — color is a soft glow, never a slab.

Typography is **Inter** — the modern geometric-humanist workhorse Asana adopted for its product UI and marketing site (its older custom face being phased toward Inter on web/mobile). On iOS it falls back to SF Pro when Inter isn't bundled. Hierarchy is product-UI: a large 32pt screen title ("My Tasks"), 26pt nav titles, 22pt section heads, 15pt task-row names, 14pt metadata, 12pt pills. Headings are weight 700-800; task names sit at a confident 500; body at 400. The type system is quiet and legible — it gets out of the way so the list reads fast.

**Key Characteristics:**
- White canvas (`#FFFFFF`) light / charcoal (`#1E1F21`) dark — calm, near-neutral, NOT pure black
- The task list IS the product — a scroll of rows, each = checkbox + name + metadata pills
- Completion circle — 20pt hollow circle fills green (`#62D26F`) with a celebratory checkmark on tap
- Coral FAB — 56pt circular `#F06A6A` "create task" button bottom-right, identical in both themes
- Multicolor object palette — projects/tags/fields each get a fixed hue (plum/aqua/green/yellow/orange/magenta/indigo)
- Color as soft tint — pills are 16-18% opacity backgrounds with saturated text, never solid slabs
- Four-state status updates — On track / At risk / Off track / Complete with a colored badge
- View switcher tabs — List / Board / Calendar / Timeline / Files — same tasks, different renderings
- Section headers with collapse caret — "Today", "Upcoming", "Later" group the task list
- Assignee avatars — 22pt circular initials/photo on the trailing edge of each task row
- Minimal navigation chrome — bottom tab bar (Home / My Tasks / Inbox / Search) + per-screen view tabs

## 2. Color Palette & Roles

### Primary (Interactive)
- **Asana Coral** (`#F06A6A`): The single brand accent — FAB, primary CTAs, active tab, selection underline, links-as-buttons. Identical in light & dark.
- **Coral Pressed** (`#D85B5B`): Pressed/active state of coral surfaces.
- **Coral Tint** (`#F06A6A1F`): 12% coral wash behind selected rows / coral chips.
- **Link / Action Blue** (`#4573D2`): Inline text links, "Mark complete" text actions, mention chips.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas, task list background.
- **Surface Gray** (`#F9F8F8`): Sidebar, grouped surfaces, hovered rows.
- **Surface Pressed** (`#F0EFEF`): Pressed/selected rows.
- **Divider** (`#EDEBE9`): Hairline between task rows and sections.
- **Card Border** (`#E8E6E4`): 1pt border on board cards.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#1E1F21`): Primary dark canvas — Asana's signature charcoal, NOT pure black.
- **Dark Surface 1** (`#252628`): Cards, grouped surfaces.
- **Dark Surface 2** (`#2E2F31`): Board columns, elevated chips, hovered rows.
- **Dark Divider** (`#35363A`): Hairline dividers.
- **Dark Card Border** (`#3A3B3F`): 1pt border on board cards in dark.

### Text
- **Text Primary Light** (`#1E1F21`): Primary body text in light mode.
- **Text Secondary Light** (`#6D6E6F`): Metadata, section counts, placeholders.
- **Text Tertiary Light** (`#9CA3A6`): Disabled, completed-task strikethrough.
- **Text Primary Dark** (`#F5F4F2`): Primary body on dark — warm off-white.
- **Text Secondary Dark** (`#A9A9AA`): Metadata on dark.
- **Text Tertiary Dark** (`#6F7073`): Disabled / completed on dark.

### Object Palette (Project / Tag / Custom-field Hues)
These are the fixed colors a user assigns to projects, tags, sections, and custom fields. In UI they appear as a saturated text on a ~16% opacity fill of the same hue.

| Role | Solid Hue | Tint Fill (Light) | Tint Fill (Dark) |
|------|-----------|-------------------|------------------|
| Plum Blue | `#4573D2` | `#4573D214` | `#4573D229` |
| Aqua | `#4ECBC4` | `#4ECBC414` | `#4ECBC429` |
| Green | `#62D26F` | `#62D26F14` | `#62D26F29` |
| Yellow | `#F8DF72` | `#F8DF7224` | `#F8DF7229` |
| Orange | `#F1BD6C` | `#F1BD6C1F` | `#F1BD6C29` |
| Magenta | `#F26FD3` | `#F26FD314` | `#F26FD329` |
| Indigo | `#5A3FFF` | `#5A3FFF14` | `#5A3FFF29` |
| Cool Gray | `#8DA3B0` | `#8DA3B014` | `#8DA3B029` |

### Project Status (Semantic — Status Updates)
| Role | Color | Use |
|------|-------|-----|
| On Track | `#4ECBC4` | Green/aqua status badge — project healthy |
| At Risk | `#F8DF72` | Yellow status badge — needs attention |
| Off Track | `#E8384F` | Red status badge — blocked / slipping |
| Complete | `#62D26F` | Green status badge — project done |

### Semantic
- **Success Green** (`#62D26F`): Completed checkbox fill, success toast.
- **Error Red** (`#E8384F`): Overdue due-date pill, delete confirmation, off-track.
- **Warning Yellow** (`#F8DF72`): At-risk badge, warning callout.
- **Overdue Tint** (`#E8384F2E`): 18% red behind an overdue date pill.
- **Mention Background** (`#4573D21A`): 10% blue behind @-mention chips.

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (by Rasmus Andersson, 2020, SIL OFL) — geometric humanist sans optimized for UI; the face Asana uses across its product and marketing surfaces.
- **Numeric**: Inter tabular figures for due-dates, counts, and task numbers (`font-feature-settings: "tnum"`).
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **iOS Note**: On iOS the system falls back to SF Pro Text/Display when the bundled Inter face is absent — the metrics are close enough to preserve rhythm.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Inter | 32pt | 800 | 1.15 | -0.5pt | "My Tasks", "Inbox" — large nav title |
| Nav Title | Inter | 26pt | 700 | 1.2 | -0.3pt | Project / section detail title |
| Section Header | Inter | 22pt | 700 | 1.25 | -0.2pt | "Project status update" headings |
| Subsection | Inter | 18pt | 600 | 1.3 | -0.1pt | "Assigned to you", group labels |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Task descriptions, comments |
| Task Row | Inter | 15pt | 500 | 1.35 | 0pt | The task name in a list row |
| List Section Label | Inter | 13pt | 700 | 1.2 | 0.2pt | "Today" / "Upcoming" group caret rows |
| Meta | Inter | 14pt | 400 | 1.35 | 0pt | Due date, project name, counts |
| Pill | Inter | 12pt | 600 | 1.0 | 0.1pt | Project tag, status pill, due chip |
| Button | Inter | 15pt | 600 | 1.0 | 0pt | Primary / secondary buttons |
| Text Action | Inter | 14pt | 600 | 1.0 | 0pt | "Mark complete", "Add subtask" |
| Avatar Initials | Inter | 10pt | 700 | 1.0 | 0.2pt | Initials inside assignee circle |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab bar labels |
| Comment Author | Inter | 14pt | 700 | 1.3 | 0pt | Comment / story author name |

### Principles
- **List legibility first**: the 15pt/500 task row is the most-read text in the app — it must stay crisp at scanning speed.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: regular body, medium task rows, semibold buttons & pills, bold headings, extrabold screen titles. No thin.
- **Tabular numerals everywhere there are dates/counts**: due dates, section counts, "3 of 8 complete" must not jitter.
- **Quiet by design**: type carries no brand expression — Inter is neutral; the personality is the coral accent and the completion circle.
- **Dynamic Type first-class**: titles, headings, body, task rows, comments all scale; pills, tab labels, avatar initials stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (Add task / Save / Send update)**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#F06A6A` (Asana Coral) — identical light & dark
- Text: `#FFFFFF`, Inter 15pt weight 600
- Padding: 13pt vertical, 26pt horizontal
- Pressed: background `#D85B5B` + scale 0.98
- Disabled: `#F06A6A` at 40% opacity, text at 60%

**Secondary / Outline Button (Assign / Set due date)**
- Background: transparent
- Text: `#1E1F21` light / `#F5F4F2` dark, Inter 14pt weight 600
- Border: 1pt `#EDEBE9` light / `#35363A` dark
- Corner radius: 8pt
- Padding: 12pt vertical, 20pt horizontal
- Pressed: background `#F0EFEF` light / `#2E2F31` dark

**Pill Button (Filter / Sort / Group by)**
- Background: `#F9F8F8` light / `#2E2F31` dark
- Border: 1pt `#EDEBE9` / `#35363A`
- Corner radius: 999pt (full pill)
- Text: `#1E1F21` / `#F5F4F2`, Inter 14pt weight 600
- Padding: 10pt vertical, 18pt horizontal

**Text Button (Mark complete / Add subtask)**
- Font: Inter 14pt weight 600
- Color: `#4573D2` (action blue) — or `#62D26F` for "Mark complete"
- No background, no underline
- Pressed: 60% opacity

**Floating Action Button (Create Task)**
- Size: 56pt circle
- Background: `#F06A6A`
- Icon: white `plus` 24pt, stroke 2.5
- Shadow: `rgba(240,106,106,0.45) 0 8px 20px`
- Position: 18pt from trailing edge, 16pt above the tab bar
- Pressed: scale 0.94 + `#D85B5B`

### Task Row (the core atom)

Every task in any list view is a row:
- Height: auto, ~58pt with one metadata line
- Leading: 20pt completion circle, 12pt gap to content
- Content: task name (Inter 15pt w500), below it a metadata row (8pt gap)
- Metadata pills: project tag (tinted hue), due-date pill (orange normal / red overdue), inline subtask count
- Trailing: 22pt assignee avatar (circular initials or photo)
- Divider: 1pt `#EDEBE9` / `#35363A` between rows
- Swipe-left: reveals "Complete" (green) + "More" (gray) actions

**Completion Circle**
- Default: 20pt circle, 1.5pt border `#9CA3A6` / `#6F7073`, transparent fill
- Tapped: fills `#62D26F`, white checkmark stroke 3pt, 200ms scale-bounce + soft haptic
- Completed task: name turns `#9CA3A6` / `#6F7073` with strikethrough
- Section-complete: optional "celebration creature" flies across (unicorn/narwhal) — 1.2s

### Section Header (List grouping)
- Leading caret: 5pt triangle `#6D6E6F` (points right collapsed, down expanded)
- Label: Inter 13pt w700 `#1E1F21` / `#F5F4F2`
- Trailing count: Inter 12pt `#9CA3A6`
- Padding: 14pt top, 8pt bottom, 18pt horizontal
- Tap caret: collapse/expand children with 200ms ease-out

### Pills & Tags

**Project / Tag Pill**
- Background: object hue at ~16% opacity (e.g., `#4ECBC414`)
- Text: the solid hue (e.g., `#4ECBC4`), Inter 12pt w600
- Padding: 3pt vertical, 9pt horizontal
- Corner radius: 999pt

**Due-Date Pill**
- On-time: text `#F1BD6C` on `#F1BD6C1F`
- Overdue: text `#E8384F` on `#E8384F2E`
- Same shape as project pill

**Status Badge (Status Update)**
- On track: `#4ECBC4` text on `#4ECBC42E`
- At risk: `#F8DF72` text on `#F8DF7229` (dark text on dark — yellow uses a deeper tint)
- Off track: `#E8384F` text on `#E8384F2E`
- Complete: `#62D26F` text on `#62D26F2E`
- Inter 12pt w700, 6pt vertical / 14pt horizontal, 999pt radius

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `#FFFFFF` / `#1E1F21` with 1pt top border `#EDEBE9` / `#35363A`
- Tabs (4): Home, My Tasks, Inbox, Search
- Icon size: 22pt; active filled, inactive stroked
- Active color: `#F06A6A`; inactive `#9CA3A6` / `#6F7073`
- Labels: Inter 10pt w600, always shown

**View Switcher Tabs (per screen)**
- Below the screen title: a horizontal strip — List, Board, Calendar, Timeline, Files
- Active tab: `#1E1F21` / `#F5F4F2` text with a 2pt coral `#F06A6A` underline
- Inactive: `#6D6E6F` / `#A9A9AA`
- Inter 14pt w600, 22pt gap, 1pt bottom divider

**Top Nav (detail screens)**
- Height: 44pt + safe area
- Leading: back chevron / close X
- Center: compact title (shown when scrolled past large title)
- Trailing: 30pt user avatar or ⋯ actions menu

### Input Fields

**Search Bar**
- Height: 40pt
- Background: `#F9F8F8` / `#2E2F31`
- Border: none
- Corner radius: 10pt
- Leading icon `magnifyingglass` 16pt `#9CA3A6`
- Placeholder: "Search tasks, projects, people" 14pt `#9CA3A6`
- Focus: 1.5pt `#F06A6A` border

**Task Name / Comment Field**
- No box in inline-edit (text sits directly on canvas)
- Comment composer: `#F9F8F8` / `#2E2F31` fill, 12pt radius, 12pt padding
- Placeholder: "Add a comment" `#9CA3A6`
- Send button: coral text "Comment" appears when non-empty

### Distinctive Components

**Completion Circle + Celebration**
- The emotional core — see Task Row. The tap-to-complete moment is deliberately delightful: green fill, checkmark draw, haptic, occasional celebration creature.

**My Tasks List**
- The default screen: sectioned (Today / Upcoming / Later or custom sections), each task a row, FAB anchored bottom-right.

**Project Board (Kanban)**
- Horizontally scrolling columns (`#F9F8F8` / `#2E2F31`, 12pt radius)
- Column header: section name uppercased, Inter 12pt w700 `#6D6E6F`
- Cards: white `#FFFFFF` / `#252628`, 1pt border, 10pt radius, 12pt padding; title + tinted tag chips + assignee avatar
- Drag card between columns: lift shadow + 200ms settle

**Status Update Card**
- A composed update post: status badge (On track / At risk / etc.), title, rich-text body, "Highlights" section
- Posted to the project's "Status" tab; appears in followers' Inbox

**Inbox Notification Row**
- Leading: actor avatar (28pt)
- Title: bold actor + action ("assigned you a task")
- Subtitle: task name + project tag
- Trailing: timestamp `#9CA3A6`; unread = coral 6pt dot on leading edge

**Subtask / Checklist Nesting**
- A task can contain subtasks — indented 24pt, each its own completion circle
- Progress: "3/8" tabular count next to parent

**Custom Field Chip**
- Inline on a task row or detail: a labeled value pill in the field's assigned hue (e.g., Priority = High in red tint, Stage = Review in plum tint)

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 16, 18, 22, 24, 32, 40, 56
- Task row vertical padding: 11pt (dense but tappable)
- Section header: 14pt top / 8pt bottom
- Screen horizontal inset: 18pt

### Grid & Container
- iPhone: content at 18pt horizontal insets, full-width list
- iPad: master-detail — left task/project list (360pt) + right detail pane
- Board columns: ~280pt wide, horizontal scroll, 12pt gap
- Detail pane: max-width 760pt centered on large screens

### Whitespace Philosophy
- **Scannable list rhythm**: 11pt row padding + 1pt divider — dense enough to see many tasks, airy enough to tap accurately
- **Pills breathe, slabs don't exist**: metadata pills are small tinted lozenges with generous internal padding; Asana never uses full-bleed colored slabs
- **The FAB owns the bottom-right**: a persistent 56pt coral anchor; content list scrolls behind it
- **Status updates get room**: the status card uses 16-20pt internal padding so it reads as a composed post, not a list row

### Border Radius Scale
- Square (0pt): dividers, full-bleed media
- Subtle (4pt): inline avatars-as-squares (rare), color dots
- Standard (8pt): buttons, cards-in-detail
- Card (10pt): board cards, search bar
- Comfortable (12-16pt): bottom sheets, modals, comment composer
- Pill (999pt): all pills, tags, status badges, the FAB
- Circle (50%): completion circle, assignee avatars

## 6. Depth & Elevation

Asana is mostly flat with restrained, soft shadows reserved for floating and dragged elements.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Task rows, list, section headers, board columns |
| Card (Level 1) | `rgba(0,0,0,0.06) 0 1px 3px` | Board cards at rest (light); 1pt border in dark |
| Floating (Level 2) | `rgba(0,0,0,0.12) 0 4px 12px` | Dropdown menus, date picker, the FAB |
| FAB | `rgba(240,106,106,0.45) 0 8px 20px` | Coral FAB — a tinted colored shadow, not gray |
| Dragged (Level 3) | `rgba(0,0,0,0.20) 0 8px 24px` | A board card / task being dragged |
| Sheet (Level 4) | `rgba(0,0,0,0.18) 0 -4px 24px` | Bottom sheets (task detail, filters) |
| Modal Scrim | `rgba(0,0,0,0.40)` | Dim behind modals/sheets |

**Shadow Philosophy**: Shadows say "this is liftable / floating", nothing decorative. The FAB is the one place Asana uses a *colored* shadow — a soft coral glow that ties the button to the brand. On dark mode, gray shadows nearly vanish, so cards rely on a 1pt `#3A3B3F` border for separation while the FAB keeps its coral glow.

### Motion
- **Task complete**: circle scales 0.8 → 1.1 → 1.0 over 200ms ease-out, checkmark stroke draws in 150ms, soft success haptic; name fades to tertiary + strikethrough over 150ms
- **Celebration creature**: on completing all tasks in a section, a creature (unicorn/narwhal/yeti/phoenix) flies across the screen left→right over ~1.2s, then fades
- **FAB → new task**: FAB scales up and morphs into the new-task sheet rising from bottom over 280ms ease-out
- **Section collapse/expand**: caret rotates 90° in 150ms; children height 0 → auto over 200ms ease-out
- **Board card drag**: 1:1 finger tracking at 60fps; other cards shift over 200ms to make room; drop snaps with 150ms ease-out + soft haptic
- **View tab switch**: 2pt coral underline slides to the tapped tab over 200ms; content cross-fades 150ms
- **Inbox read**: tapping a notification fades its coral unread dot out over 120ms
- **Push navigation**: iOS-native push, 300ms horizontal slide
- **Haptic feedback**: soft success on task complete, soft impact on card drag start & section toggle, light tick on tab switch

## 7. Do's and Don'ts

### Do
- Use Asana Coral (`#F06A6A`) as the single brand accent — FAB, active tab, primary CTA — identical in light & dark
- Use the charcoal `#1E1F21` for dark canvas — NOT pure black
- Make the completion circle a delight: green fill (`#62D26F`) + checkmark draw + haptic on tap
- Render projects/tags/fields with their assigned hue as a ~16% tint fill + saturated text — never solid slabs
- Use the four-state status system (On track / At risk / Off track / Complete) for project updates
- Group the task list with collapsible section headers (caret + label + count)
- Keep the FAB persistently bottom-right with its soft coral shadow
- Give each task row an assignee avatar on the trailing edge
- Use tabular numerals for due dates, counts, and progress
- Offer the view-switcher tabs (List / Board / Calendar / Timeline / Files) over the same task data

### Don't
- Don't introduce a second brand accent — coral is the only one; other hues are object colors, not UI accents
- Don't fill pills with solid saturated color — Asana uses ~16% tint + colored text
- Don't use pure black (`#000000`) for the dark canvas or body text
- Don't skip the completion-circle delight — a flat checkbox loses Asana's emotional core
- Don't make status updates look like list rows — they're composed posts with internal padding
- Don't hide the FAB behind content on scroll — it stays anchored and tappable
- Don't use heavy drop shadows on rows — only floating / dragged elements get shadow
- Don't use gray shadow as the FAB shadow — it must be a soft coral glow
- Don't bold task-row names heavily — 500 weight is correct; headings are 700-800
- Don't tint the whole row background per project — the tint lives in the small pill only

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Single column; metadata pills may wrap to a second line |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | Larger task rows; two metadata lines comfortable |
| iPad (portrait) | 768pt | Master-detail: project/task list (360pt) + detail pane |
| iPad (landscape) | 1024pt+ | Three-pane optional: sidebar + list + task detail |

### Dynamic Type
- Screen title, nav title, section headers, body, task-row names, comments: full scale
- Pills, tab labels, avatar initials, list section labels: FIXED (layout-sensitive)
- Due dates / counts: scale but stay tabular

### Orientation
- All screens support rotation
- iPad landscape unlocks the three-pane split (sidebar / list / detail)
- Board view scrolls horizontally in both orientations

### Touch Targets
- Completion circle: 20pt glyph, 44pt hit area
- Tab bar icons: 22pt glyph, 44pt hit
- Task row: full-row tap opens detail; circle tap completes (separate 44pt zones)
- FAB: 56pt (exceeds minimum)
- Section caret: 16pt glyph, 44pt hit

### Safe Area Handling
- Top: large title & top nav respect safe area + Dynamic Island
- Bottom: tab bar + home indicator respected; FAB sits 16pt above the tab bar
- Keyboard: comment composer & task-name field rise above keyboard
- Sides: 18pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#1E1F21`
- Surface (dark): `#252628` / `#2E2F31`
- Divider: `#EDEBE9` light / `#35363A` dark
- Text primary: `#1E1F21` light / `#F5F4F2` dark
- Text secondary: `#6D6E6F` light / `#A9A9AA` dark
- Brand coral: `#F06A6A` (pressed `#D85B5B`)
- Action blue: `#4573D2`
- Object aqua: `#4ECBC4`
- Object green / success: `#62D26F`
- Object yellow / at-risk: `#F8DF72`
- Object orange / due: `#F1BD6C`
- Object magenta: `#F26FD3`
- Error / off-track: `#E8384F`

### Example Component Prompts
- "Build an Asana task row in SwiftUI: leading 20pt completion circle (1.5pt border `#9CA3A6`, transparent fill). 12pt gap to a VStack: task name in Inter 15pt w500 `#1E1F21`; below, an HStack of pills — a project tag (Inter 12pt w600 `#4ECBC4` on `#4ECBC414`, 999pt radius, 3/9pt padding) and a due-date pill (`#F1BD6C` on `#F1BD6C1F`, or `#E8384F` on `#E8384F2E` if overdue). Trailing: 22pt circular avatar with white initials. 11pt vertical padding, 1pt `#EDEBE9` bottom divider. On circle tap: fill `#62D26F`, draw white checkmark, scale 0.8→1.1→1.0 over 200ms + soft haptic, name → `#9CA3A6` strikethrough."

- "Create the Asana My Tasks screen: 32pt w800 title 'My Tasks' + 30pt user avatar trailing. Below, a view-switcher strip (List / Board / Calendar / Files), active = `#1E1F21` text + 2pt coral `#F06A6A` underline, Inter 14pt w600. Then a sectioned list: section header = 5pt caret + Inter 13pt w700 label + 12pt `#9CA3A6` count. A 56pt coral `#F06A6A` FAB bottom-right (18pt trailing, 16pt above tab bar) with white plus icon and shadow `rgba(240,106,106,0.45) 0 8px 20px`."

- "Render an Asana status update card: top row = status badge ('On track' Inter 12pt w700 `#4ECBC4` on `#4ECBC42E`, 999pt, 6/14pt padding) + posted date. Title in Inter 22pt w700. Body in Inter 16pt w400 `#1E1F21` / `#F5F4F2`. 18pt internal padding, `#FFFFFF` / `#252628` background, 12pt radius, 1pt `#EDEBE9` / `#3A3B3F` border."

- "Build an Asana project board column: `#F9F8F8` light / `#2E2F31` dark, 12pt radius, ~280pt wide. Header: section name uppercased Inter 12pt w700 `#6D6E6F`. Cards: `#FFFFFF` / `#252628`, 1pt `#E8E6E4` / `#3A3B3F` border, 10pt radius, 12pt padding — title Inter 13pt w500, a tinted tag chip, a 22pt assignee avatar. Drag between columns: lift `rgba(0,0,0,0.20) 0 8px 24px`, neighbors shift 200ms ease-out, soft haptic on drop."

- "Create the Asana bottom tab bar: 52pt + safe area, `#FFFFFF` / `#1E1F21`, 1pt top border `#EDEBE9` / `#35363A`. Four tabs — Home, My Tasks, Inbox, Search — 22pt icons (filled active, stroked inactive), active `#F06A6A`, inactive `#9CA3A6` / `#6F7073`, labels Inter 10pt w600 always shown. No tint pill."

- "Render an Asana Inbox notification row: 28pt actor avatar leading; title = bold actor name + action ('assigned you a task') Inter 14pt; subtitle = task name + tinted project tag; trailing timestamp `#9CA3A6` 12pt. Unread state: 6pt coral `#F06A6A` dot on the leading edge; tapping fades the dot out over 120ms."

### Iteration Guide
1. Canvas is white in light, charcoal `#1E1F21` in dark — NOT pure black
2. Coral `#F06A6A` is the ONLY brand accent — identical in both themes; everything else is object color
3. The completion circle must be a delight — green `#62D26F` fill + checkmark draw + haptic on tap
4. Pills are ~16% tint fills with saturated text — never solid slabs; project hues come from the fixed object palette
5. Status updates use four states — On track / At risk / Off track / Complete with a colored badge
6. The task list is sectioned with collapsible caret headers (Today / Upcoming / custom)
7. The FAB is a persistent 56pt coral circle bottom-right with a soft coral-tinted shadow
8. Typography is Inter, quiet and legible — task rows at 15pt/500, headings 700-800, tabular numerals for dates
9. View tabs (List / Board / Calendar / Timeline / Files) render the same tasks differently — active tab gets a 2pt coral underline
10. Dark mode keeps coral identical, inverts text to warm off-white `#F5F4F2`, and uses 1pt borders where gray shadows vanish
