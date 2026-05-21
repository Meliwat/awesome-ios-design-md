# Design System Inspiration of ClickUp (iOS)

## 1. Visual Theme & Atmosphere

ClickUp's iOS app is a dense, feature-maximal productivity surface whose personality is the **brand gradient** and an information-rich task list. The default canvas is white (`#FFFFFF`) in light mode and a deep blue-violet (`#1B1B2E`) in dark mode — chosen to complement the gradient, NOT neutral black. The app opens to a List view: tasks grouped under **custom status pills** ("To do", "In progress", "Review", "Complete"), where each task row packs a status checkbox, name, priority flag, tags, due date, and assignee avatar into a compact line. ClickUp's thesis is "one app to replace them all" — so the UI is deliberately dense, with 12+ view types (List, Board, Calendar, Gantt, Timeline, Workload, Table, Doc, Mind Map, Activity, Map, Embed) rendering the same task objects.

The signature visual element is the **3-stop brand gradient**: purple (`#7B68EE`) → pink (`#FD71AF`) → blue (`#49CCF9`). It is the brand's hero moment — used on the floating action button, the primary CTA, the logo, and every **ClickUp AI** surface (the AI bar, AI buttons, AI-generated content highlights). The gradient is reserved and intentional: it never tiles the background; it marks "the most important action" and "AI". The other signature is the **custom status system**: every List defines its own ordered set of colored statuses, and the task list is grouped by them — each group headed by a saturated status pill with a count.

The color system pairs the gradient triad with a functional palette: status green (`#2ECC71`), red (`#F44E6E`), orange (`#FF9F1A`), blue (`#49CCF9`), yellow (`#FFCC00`), teal (`#1ECBE1`), plus a neutral gray (`#5B5B79`) for "to do"/unset. Priority is encoded as a **colored flag triangle**: Urgent (red `#F44E6E`), High (orange `#FF9F1A`), Normal (blue `#49CCF9`), Low (gray). Tags are small rounded pills in any of the palette hues at ~22% opacity with saturated text. Color is functional and dense — it differentiates many simultaneous attributes at a glance.

Typography is **Plus Jakarta Sans** — a geometric, slightly humanist sans (a close free match to ClickUp's product face). On iOS it falls back to SF Pro. Hierarchy is confident: 32pt extrabold screen titles, 26pt extrabold list titles, 22pt section heads, 14pt semibold task names (denser/bolder than Asana's), 13pt metadata, 10pt extrabold status pills (uppercased, tracked). Headings push to 800; task names sit at a deliberate 600 so the dense list still scans.

**Key Characteristics:**
- White canvas (`#FFFFFF`) light / deep blue-violet (`#1B1B2E`) dark — complements the gradient, NOT neutral black
- 3-stop brand gradient — purple `#7B68EE` → pink `#FD71AF` → blue `#49CCF9` on FAB, primary CTA, logo, all AI surfaces
- Custom status pills — every List defines its own ordered colored statuses; the task list is grouped by them
- Dense task rows — status box + name + priority flag + tags + due + assignee packed into one compact line
- Priority flag — a colored triangle: Urgent (red) / High (orange) / Normal (blue) / Low (gray)
- ClickUp AI surfaces — the gradient marks every AI affordance (AI bar, AI buttons, AI content)
- 12+ view types — List / Board / Calendar / Gantt / Timeline / Workload / Table / Doc / Mind Map / Activity / Map
- Tags as ~22% tinted pills with saturated text in any palette hue
- Hierarchy nesting — Spaces ▸ Folders ▸ Lists ▸ Tasks ▸ Subtasks
- Squircle FAB — a rounded-square (18pt radius) gradient button, not a circle
- Minimal but information-dense chrome — bottom tab bar (Home / Tasks / Inbox / Search) + per-list view tabs

## 2. Color Palette & Roles

### Primary (Interactive)
- **Brand Gradient** (`#7B68EE` → `#FD71AF` → `#49CCF9`, 135°): The hero — FAB, primary CTA, logo, AI surfaces. Identical light & dark.
- **Brand Purple** (`#7B68EE`): The single solid brand color — active tab, links, solid secondary CTA, selection.
- **Brand Pink** (`#FD71AF`): Gradient mid-stop; accent on AI / highlights.
- **Brand Blue** (`#49CCF9`): Gradient end-stop; also "Normal" priority & "In progress" default status.
- **Purple Pressed** (`#6A57DD`): Pressed state of solid-purple surfaces.
- **Purple Tint** (`#7B68EE38`): ~22% purple behind selected rows / focused fields.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas, task list background.
- **Surface Gray** (`#F7F7FB`): Grouped surfaces, sidebar, hovered rows.
- **Surface Pressed** (`#EDEDF4`): Pressed/selected rows.
- **Divider** (`#E7E7EF`): Hairline between task rows and groups.
- **Card Border** (`#DEDEE9`): 1pt border on board cards.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#1B1B2E`): Primary dark canvas — deep blue-violet, NOT pure black.
- **Dark Surface 1** (`#232338`): Cards, grouped surfaces.
- **Dark Surface 2** (`#2C2C44`): Elevated chips, hovered rows, board columns.
- **Dark Divider** (`#383852`): Hairline dividers.
- **Dark Card Border** (`#42425C`): 1pt border on board cards in dark.

### Text
- **Text Primary Light** (`#1B1B2E`): Primary task names, body.
- **Text Secondary Light** (`#5E5E7A`): Metadata, due dates, counts, placeholders.
- **Text Tertiary Light** (`#9292A8`): Disabled, completed-task strikethrough.
- **Text Primary Dark** (`#F1F1F6`): Primary text on dark.
- **Text Secondary Dark** (`#A6A6BC`): Metadata on dark.
- **Text Tertiary Dark** (`#6E6E88`): Disabled / completed on dark.

### Functional Palette (Status / Priority / Tags)
The palette every custom status, priority, and tag draws from. Statuses render as **solid pills**; tags as ~22% tints.

| Role | Solid Hue | Tint (22%) | On-Solid Text |
|------|-----------|-----------|---------------|
| Green / Complete | `#2ECC71` | `#2ECC7138` | `#FFFFFF` |
| Red / Urgent | `#F44E6E` | `#F44E6E38` | `#FFFFFF` |
| Orange / High | `#FF9F1A` | `#FF9F1A38` | `#FFFFFF` |
| Blue / Normal | `#49CCF9` | `#49CCF938` | `#0E2A33` |
| Purple / Brand | `#7B68EE` | `#7B68EE38` | `#FFFFFF` |
| Pink | `#FD71AF` | `#FD71AF38` | `#FFFFFF` |
| Yellow | `#FFCC00` | `#FFCC0038` | `#3A2F00` |
| Teal | `#1ECBE1` | `#1ECBE138` | `#06363C` |
| Gray / To-do / Unset | `#5B5B79` | `#5B5B7938` | `#FFFFFF` |

### Priority (Flag Triangle)
| Role | Color |
|------|-------|
| Urgent | `#F44E6E` |
| High | `#FF9F1A` |
| Normal | `#49CCF9` |
| Low | `#9292A8` (gray) |
| No priority | outline gray, no fill |

### Semantic
- **Success Green** (`#2ECC71`): "Complete" status, success toast.
- **Error / Urgent Red** (`#F44E6E`): Overdue date, delete, urgent priority, error toast.
- **Warning Orange** (`#FF9F1A`): High priority, warning callout.
- **AI Glow** (`#7B68EE` → `#FD71AF` → `#49CCF9` at ~18% on a panel): backdrop tint behind ClickUp AI content.

## 3. Typography Rules

### Font Family
- **Primary**: `Plus Jakarta Sans` (by Tokotype, 2020, SIL OFL) — geometric humanist sans; the closest free match to ClickUp's product typeface.
- **Numeric**: Plus Jakarta Sans tabular figures for due dates, counts, points, time-tracking (`font-feature-settings: "tnum"`).
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **iOS Note**: falls back to SF Pro Text/Display when Plus Jakarta Sans isn't bundled.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Plus Jakarta Sans | 32pt | 800 | 1.15 | -0.5pt | "Everything", "Home" — large nav title |
| List Title | Plus Jakarta Sans | 26pt | 800 | 1.2 | -0.3pt | List/Folder name in header (+ color square) |
| Section | Plus Jakarta Sans | 22pt | 700 | 1.25 | -0.2pt | "Today's tasks", subsection headings |
| Subsection | Plus Jakarta Sans | 17pt | 700 | 1.3 | -0.1pt | "Assigned to me", group labels |
| Body | Plus Jakarta Sans | 15pt | 400 | 1.5 | 0pt | Task descriptions, comments, Docs |
| Task Name | Plus Jakarta Sans | 14pt | 600 | 1.35 | 0pt | The task name in a dense list row |
| Status Group Label | Plus Jakarta Sans | 10pt | 800 | 1.1 | 0.6pt | Status-pill text heading a group (uppercased) |
| Meta | Plus Jakarta Sans | 13pt | 400 | 1.35 | 0pt | Due date, subtask count, time estimate |
| Tag | Plus Jakarta Sans | 10pt | 700 | 1.0 | 0.2pt | Tag pill text |
| Button | Plus Jakarta Sans | 15pt | 700 | 1.0 | 0pt | Primary / secondary buttons |
| Text Action | Plus Jakarta Sans | 14pt | 600 | 1.0 | 0pt | "Add subtask", "More options" |
| Assignee Initials | Plus Jakarta Sans | 9pt | 700 | 1.0 | 0.2pt | Initials inside an assignee avatar |
| Tab Label | Plus Jakarta Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab bar labels |
| Comment Author | Plus Jakarta Sans | 13pt | 700 | 1.3 | 0pt | Comment / activity author name |

### Principles
- **Dense but legible**: ClickUp packs many attributes per row — task names at 14pt/600 (bolder than typical) so the eye anchors fast.
- **Status pills are tight & loud**: 10pt/800, uppercased, +0.6pt tracking, white-on-saturated — they head every group.
- **Weight concentrated at 400 / 600 / 700 / 800**: regular body, semibold task names & actions, bold buttons/headings, extrabold titles & status pills. No thin.
- **Tabular numerals for the data**: due dates, points, time-tracked, counts must not jitter in the dense grid.
- **The gradient never appears in type**: text is always solid; the gradient is for surfaces (FAB/CTA/AI), never letterforms in-app.
- **Dynamic Type first-class**: titles, sections, body, task names, comments scale; status pills, tags, tab labels, assignee initials stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (Create task / Save / Generate with AI)**
- Shape: Rounded rectangle, 10pt corner radius
- Background: the 3-stop brand gradient (135°, `#7B68EE` → `#FD71AF` → `#49CCF9`)
- Text: `#FFFFFF`, Plus Jakarta Sans 15pt weight 700
- Padding: 13pt vertical, 26pt horizontal
- Pressed: brightness 0.94 + scale 0.98
- Disabled: gradient at 40% opacity

**Solid Secondary Button (Save / Apply)**
- Background: `#7B68EE` (Brand Purple, solid)
- Text: `#FFFFFF`, Plus Jakarta Sans 15pt weight 700
- Corner radius: 10pt; padding 13/24pt
- Pressed: `#6A57DD` + scale 0.98

**Outline Button (Add subtask / Add field)**
- Background: transparent
- Text: `#1B1B2E` light / `#F1F1F6` dark, Plus Jakarta Sans 14pt weight 600
- Border: 1pt `#E7E7EF` light / `#383852` dark
- Corner radius: 10pt; padding 12/20pt
- Pressed: background `#EDEDF4` / `#2C2C44`

**Text Button (More options / Add subtask)**
- Font: Plus Jakarta Sans 14pt weight 600
- Color: `#7B68EE` (brand purple)
- Pressed: 60% opacity

**Floating Action Button (Create — squircle, not circle)**
- Size: 56pt, 18pt corner radius (rounded-square / squircle)
- Background: the 3-stop brand gradient
- Icon: white `plus` 24pt, stroke 2.6
- Shadow: `rgba(123,104,238,0.50) 0 8px 22px`
- Position: 16pt from trailing edge, 16pt above the tab bar
- Pressed: scale 0.94 + brightness 0.94

### Task Row (the core atom)

Every task in any list view is a dense row:
- Height: ~52pt with one metadata line
- Leading: 18pt status checkbox — a 5pt-radius rounded square, 2pt border in the status color; checkmark when done
- Name: Plus Jakarta Sans 14pt w600 `#1B1B2E` / `#F1F1F6`, truncates with ellipsis
- Metadata row (6pt gap): priority flag triangle, tag pills, due date
- Trailing: 22pt circular assignee avatar (initials or photo)
- Divider: 1pt `#E7E7EF` / `#383852` between rows
- Swipe-left: Complete (green) + More; swipe-right: set priority
- Tap row: opens the full task panel (description, subtasks, custom fields, checklists, comments, activity)

**Status Checkbox**
- Default: 18pt rounded square, 5pt radius, 2pt border in the status's color, transparent fill
- "In progress" group: orange/blue border; "Complete": filled `#2ECC71` + white check
- Tap: cycles to the next status (or opens the status picker on long-press) + soft haptic

**Status Pill (group header)**
- A saturated solid pill in the status color: Plus Jakarta Sans 10pt w800, uppercased, +0.6pt tracking, white (or dark on yellow/blue) text
- 4pt vertical / 9pt horizontal padding, 5pt corner radius
- Followed by a count `#9292A8` and a trailing "+ add task" affordance

**Priority Flag**
- A small filled triangle (≈12pt) in the priority color: Urgent `#F44E6E`, High `#FF9F1A`, Normal `#49CCF9`, Low gray; "No priority" = gray outline
- Tap: opens the priority picker

**Tag Pill**
- ~22% tint of a palette hue with saturated text, Plus Jakarta Sans 10pt w700
- 2pt vertical / 7pt horizontal, 999pt radius (full pill)

### Status Group

- A status pill (see above) + item count + "+ add task"
- Tap the pill chevron: collapse/expand the group's tasks (200ms ease-out)
- Lists define an ordered set of statuses; "Complete"-type statuses collapse by default

### ClickUp AI Bar / Surfaces

- A horizontal bar (10pt radius) with a ~18% gradient backdrop tint + 1pt purple border
- Leading sparkle glyph in brand purple; label "ClickUp AI · {action}" in Plus Jakarta Sans 12pt w600
- AI-generated content blocks get the same subtle gradient tint to mark provenance
- AI action buttons use the full gradient fill

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `#FFFFFF` / `#1B1B2E` with 1pt top border `#E7E7EF` / `#383852`
- Tabs (4): Home, Tasks (My Work), Inbox (Notifications), Search
- Icon size: 22pt; active filled, inactive stroked
- Active color: `#7B68EE` (brand purple); inactive `#9292A8` / `#6E6E88`
- Labels: Plus Jakarta Sans 10pt w600, always shown

**View Switcher Tabs (per list)**
- Below the list title: List, Board, Calendar, Gantt, Timeline, Doc, Table, … (12+; horizontally scrollable)
- Active tab: `#7B68EE` text with a 2.5pt purple `#7B68EE` underline
- Inactive: `#5E5E7A` / `#A6A6BC`
- Plus Jakarta Sans 13pt w600, 18pt gap

**Top Nav (list / task detail)**
- Height: 44pt + safe area
- Leading: back chevron / close X + breadcrumb (Space ▸ Folder ▸ List)
- Center / leading: compact list or task title
- Trailing: ⋯ actions / share / AI

### Input Fields

**Search Bar**
- Height: 40pt
- Background: `#F7F7FB` / `#2C2C44`
- Border: none
- Corner radius: 10pt
- Leading icon `magnifyingglass` 16pt `#9292A8`
- Placeholder: "Search tasks, docs, people" 14pt `#9292A8`
- Focus: 1.5pt `#7B68EE` border

**Inline Field / Composer**
- Tapping a custom-field or name cell turns it into an inline field with a 1.5pt `#7B68EE` focus ring
- Comment composer: `#F7F7FB` / `#2C2C44` fill, 10pt radius, "Comment or type / for commands" placeholder; a `/` slash menu and `@`-mention picker are available
- AI compose: a gradient-bordered field with the sparkle prefix

### Distinctive Components

**Brand-Gradient FAB & AI Surfaces**
- The hero — see Buttons & AI Bar. The gradient is reserved for "most important action" and "AI"; never a background.

**Custom-Status Grouped List**
- The default screen: tasks grouped by the List's ordered custom statuses, each headed by a saturated pill.

**Board View**
- Columns = one status each; cards carry the status-color top accent + priority flag + tags + assignee
- Drag a card to another column = change its status; lift shadow + 200ms settle

**Task Panel**
- A full-screen / bottom-sheet detail: title, status pill, assignees, priority, dates, custom fields, subtasks, checklists, attachments, a description (rich-text Doc), comments + activity

**Custom Field Cell**
- Inline labeled value in the field's type: dropdown (tinted pill), number, money, progress bar, rating, formula — each compact

**Slash / Mention Menu**
- `/` opens a command menu (turn into, add field, embed); `@` mentions people/tasks/docs — a floating panel with the AI gradient border on AI options

**Inbox Notification Row**
- Leading actor avatar (28pt); bold actor + action; subtitle = task + List breadcrumb; trailing timestamp; unread = purple dot

**Hierarchy Breadcrumb**
- Space ▸ Folder ▸ List path in the top nav; each segment tappable; the Space carries its color square

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 6, 8, 10, 14, 16, 18, 22, 26, 32, 40, 56
- Task row vertical padding: 11pt (dense)
- Status group header: 10pt top / 6pt bottom
- Screen horizontal inset: 16pt

### Grid & Container
- iPhone: full-width dense list; metadata wraps if narrow
- iPad: master-detail — Spaces/Lists sidebar (300pt) + list/task detail
- Board columns: ~270pt wide, horizontal scroll, 12pt gap
- Task panel: max-width 760pt centered on large screens
- Gantt/Timeline: horizontally scrolling timeline canvas

### Whitespace Philosophy
- **Density is the point**: ClickUp deliberately surfaces many attributes per row — tight 11pt padding, small pills, compact flags — so power users see everything without tapping in
- **The gradient is rare and loud**: exactly one gradient element is visible at rest (the FAB) plus AI surfaces — its scarcity is what makes it read as "primary"
- **Status pills bucket the list**: saturated pill headers create strong visual groups in a dense scroll
- **Squircle, not circle**: the FAB's 18pt-radius rounded square is a deliberate brand signature distinct from circular FABs

### Border Radius Scale
- Square (0pt): dividers, full-bleed media
- Subtle (5pt): status checkbox, status pills, color squares
- Standard (8-10pt): buttons, search bar, cards, AI bar
- Comfortable (14pt): bottom sheets, modals, pickers
- Squircle (18pt): the FAB, large feature cards
- Pill (999pt): tags, filter pills
- Circle (50%): assignee avatars

## 6. Depth & Elevation

ClickUp is mostly flat — density + status color provide structure; shadows are reserved for floating, dragged, and the gradient FAB.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Task rows, list, status group headers |
| Card (Level 1) | `rgba(0,0,0,0.07) 0 1px 4px` | Board cards at rest (light); 1pt border in dark |
| Floating (Level 2) | `rgba(0,0,0,0.14) 0 4px 14px` | Slash/mention menu, status & priority pickers, dropdowns |
| FAB | `rgba(123,104,238,0.50) 0 8px 22px` | Gradient FAB — a purple-tinted colored shadow |
| Dragged (Level 3) | `rgba(0,0,0,0.22) 0 8px 24px` | A board card / task being dragged |
| Sheet (Level 4) | `rgba(0,0,0,0.20) 0 -4px 24px` | Bottom sheets (task panel, filters, pickers) |
| Modal Scrim | `rgba(0,0,0,0.45)` | Dim behind modals/sheets |

**Shadow Philosophy**: Shadows mean "liftable / floating", never decoration. The board itself is flat — status color + density encode structure. The FAB gets a soft *purple* shadow tying it to the brand gradient. On dark, gray shadows vanish, so board cards rely on a 1pt `#42425C` border + a status-color top accent while the FAB keeps its purple glow.

### Motion
- **Status change**: tapping the checkbox cycles status with a 180ms color crossfade + soft haptic; long-press opens the picker (fade + 4pt slide-up, 150ms); "Complete" plays a quick check-draw
- **Drag task / board card**: 1:1 finger tracking at 60fps; neighbors shift over 200ms ease-out; drop snaps with 150ms ease-out + soft haptic; status recolors on cross-column drop
- **Status group collapse/expand**: chevron rotates 90° in 150ms; tasks height 0 → auto over 200ms ease-out
- **FAB → create**: squircle scales up and the create sheet rises from bottom over 280ms ease-out
- **AI generation**: the AI bar's gradient shimmers (a 1.2s sweep) while generating; result fades in over 200ms with the gradient-tint backdrop
- **View tab switch**: 2.5pt purple underline slides over 200ms; the view cross-fades 150ms
- **Push navigation / breadcrumb drill**: iOS-native push, 300ms horizontal slide
- **Haptic feedback**: soft impact on status change, drag start, group toggle; light tick on tab switch; success tick on task complete

## 7. Do's and Don'ts

### Do
- Reserve the 3-stop gradient (`#7B68EE` → `#FD71AF` → `#49CCF9`) for the FAB, primary CTA, logo, and AI surfaces only
- Use the deep blue-violet `#1B1B2E` for dark canvas — it complements the gradient, NOT neutral black
- Group the task list by the List's ordered custom status pills (saturated, uppercased, 10pt/800)
- Make the FAB a squircle (56pt, 18pt radius) — a deliberate brand signature, not a circle
- Encode priority as a colored flag triangle (Urgent red / High orange / Normal blue / Low gray)
- Embrace density — surface priority + tags + due + assignee per row; power users want it all visible
- Mark every AI affordance with the gradient (AI bar tint, AI buttons, AI content backdrop)
- Use tags as ~22% tinted pills with saturated text in any palette hue
- Offer the full view switcher (List / Board / Calendar / Gantt / Timeline / Doc / …) over the same tasks
- Use tabular numerals for due dates, points, time-tracked, and counts in the dense grid
- Show the Space ▸ Folder ▸ List breadcrumb so users always know where they are

### Don't
- Don't tile the gradient as a background or use it on body text — it marks primary action + AI only
- Don't use pure black (`#000000`) for the dark canvas — `#1B1B2E` blue-violet is correct
- Don't render statuses as faint tint chips — group-header status pills are SOLID and saturated (tags are the tinted ones)
- Don't make the FAB a circle — the squircle is the brand signature
- Don't over-sparsify the list — ClickUp's value is information density; don't strip metadata to "clean it up"
- Don't use a flat checkbox for status — it's a status-colored rounded square that cycles statuses
- Don't use heavy shadows on rows — only floating / dragged elements and the FAB get shadow
- Don't use gray as the FAB shadow — it's a soft purple glow tied to the gradient
- Don't bold task names beyond 600 or lighten below it — 14pt/600 keeps the dense list scannable
- Don't introduce a second gradient — there is exactly one brand gradient; functional colors are solids
- Don't hide the breadcrumb — hierarchy (Space/Folder/List) is core to ClickUp's mental model

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Metadata may wrap; fewer tags shown, "+N" overflow |
| iPhone 13/14/15 | 390pt | Standard dense layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | One extra metadata column without wrap |
| iPad (portrait) | 768pt | Master-detail: Spaces/Lists sidebar (300pt) + list/task detail |
| iPad (landscape) | 1024pt+ | Three-pane: sidebar + list + task panel; full Gantt/Table |

### Dynamic Type
- Screen/list/section titles, body, task names, comments, Docs: full scale
- Status pills, tags, tab labels, assignee initials, status group labels: FIXED (dense-layout-sensitive)
- Due dates / points / counts: scale but stay tabular

### Orientation
- All screens support rotation
- iPad landscape unlocks the three-pane (sidebar / list / task panel)
- Board / Gantt / Timeline scroll horizontally in both orientations

### Touch Targets
- Status checkbox: 18pt glyph, 44pt hit area (separate from row tap)
- Tab bar icons: 22pt glyph, 44pt hit
- Task row: full-row tap opens the task panel
- Status group chevron: 16pt glyph, 44pt hit
- FAB: 56pt squircle (exceeds minimum)
- Priority flag / tag: 44pt hit even though the glyph is small

### Safe Area Handling
- Top: list title & top nav respect safe area + Dynamic Island
- Bottom: tab bar + home indicator respected; FAB 16pt above the tab bar
- Keyboard: inline field, comment composer, and AI compose rise above the keyboard
- Sides: 16pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#1B1B2E`
- Surface (dark): `#232338` / `#2C2C44`
- Divider: `#E7E7EF` light / `#383852` dark
- Text primary: `#1B1B2E` light / `#F1F1F6` dark
- Text secondary: `#5E5E7A` light / `#A6A6BC` dark
- Brand gradient: `#7B68EE` → `#FD71AF` → `#49CCF9` (135°)
- Brand purple (solid): `#7B68EE` (pressed `#6A57DD`)
- Status green / Complete: `#2ECC71`
- Status red / Urgent: `#F44E6E`
- Status orange / High: `#FF9F1A`
- Status blue / Normal: `#49CCF9`
- Gray / To-do / unset: `#5B5B79`
- Tag tint: hue at ~22% (`#7B68EE38`) with saturated text

### Example Component Prompts
- "Build a ClickUp task row in SwiftUI: leading 18pt status checkbox (a 5pt-radius rounded square, 2pt border in the status color, transparent fill — filled `#2ECC71` + white check when done). 10pt gap to a VStack: task name Plus Jakarta Sans 14pt w600 `#1B1B2E` (truncating); below, an HStack — a priority flag triangle (filled, Urgent `#F44E6E` / High `#FF9F1A` / Normal `#49CCF9` / Low gray), tag pills (Plus Jakarta Sans 10pt w700 saturated text on a ~22% tint, 999pt radius), and a due date (`#5E5E7A`, or `#F44E6E` if overdue). Trailing: 22pt circular avatar. 11pt vertical padding, 1pt `#E7E7EF` divider, ~52pt tall. Tap checkbox cycles status with a 180ms color crossfade + soft haptic."

- "Create the ClickUp custom-status group header: a saturated solid status pill — e.g. `#49CCF9` 'IN PROGRESS' in Plus Jakarta Sans 10pt w800 uppercased +0.6pt tracking, dark `#0E2A33` text (white on most colors), 4/9pt padding, 5pt radius — followed by an item count `#9292A8` and a trailing '+ add task'. Tapping the pill chevron collapses the group (tasks height 0 → auto, 200ms ease-out)."

- "Render the ClickUp FAB: a 56pt squircle (18pt corner radius — NOT a circle) filled with the 3-stop brand gradient (135°, `#7B68EE` → `#FD71AF` → `#49CCF9`), a white `plus` icon 24pt, shadow `rgba(123,104,238,0.50) 0 8px 22px`. Position 16pt from trailing edge, 16pt above the tab bar. Press: scale 0.94 + brightness 0.94."

- "Build a ClickUp AI bar: a 10pt-radius horizontal bar with a ~18% 3-stop-gradient backdrop tint + 1pt `#7B68EE` border, a leading brand-purple sparkle glyph, label 'ClickUp AI · {action}' in Plus Jakarta Sans 12pt w600 `#5E5E7A` ('ClickUp AI' bold `#1B1B2E`). While generating, the gradient shimmers over 1.2s; the result fades in (200ms) on a gradient-tint backdrop."

- "Create the ClickUp Board view: horizontally scrolling columns (~270pt, `#F7F7FB` / `#2C2C44`, 10pt radius), one per custom status. Cards: `#FFFFFF` / `#232338`, 1pt border, a 3pt status-color top accent, priority flag + tags + 22pt avatar, lift shadow on drag (`rgba(0,0,0,0.22) 0 8px 24px`). Dropping a card into another column changes its status; drop snaps 150ms + soft haptic; the status accent recolors."

- "Create the ClickUp bottom tab bar: 52pt + safe area, `#FFFFFF` / `#1B1B2E`, 1pt top border `#E7E7EF` / `#383852`. Four tabs — Home, Tasks, Inbox, Search — 22pt icons (filled active, stroked inactive), active `#7B68EE`, inactive `#9292A8` / `#6E6E88`, labels Plus Jakarta Sans 10pt w600 always shown. A 56pt squircle gradient FAB sits bottom-right."

### Iteration Guide
1. Canvas is white in light, deep blue-violet `#1B1B2E` in dark — complements the gradient, NOT neutral black
2. The 3-stop gradient (`#7B68EE` → `#FD71AF` → `#49CCF9`) is reserved for the FAB, primary CTA, logo, and AI — never a background or body text
3. The FAB is a SQUIRCLE (56pt, 18pt radius), not a circle — a deliberate brand signature
4. Group the task list by the List's ordered custom status pills — SOLID, saturated, uppercased 10pt/800
5. Priority is a colored flag triangle (Urgent red / High orange / Normal blue / Low gray)
6. Embrace density — surface priority + tags + due + assignee per row; don't strip metadata
7. Tags are ~22% tinted pills with saturated text; statuses (group headers) are the solid ones
8. Mark every AI affordance with the gradient (AI bar tint + border, AI buttons, AI content backdrop)
9. Typography is Plus Jakarta Sans, confident — task names 14pt/600, titles 800, status pills 10pt/800, tabular numerals
10. Dark mode keeps the gradient + status colors identical, inverts text to `#F1F1F6`, uses 1pt borders + status accents where shadows vanish
