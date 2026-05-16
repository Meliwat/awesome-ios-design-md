# Design System Inspiration of Linear (iOS)

## 1. Visual Theme & Atmosphere

Linear's iOS app is a near-black command surface built for speed. The canvas sits at `#08090A` — darker than Spotify's `#121212`, almost an OLED true-black — so the interface disappears and the issue list becomes the entire experience. There is no decorative chrome, no gradient hero, no photography. Every pixel is in service of one thing: moving through work fast. Surfaces lift only slightly (`#141516`, `#1C1D1F`) to separate a sheet or a hovered row, and a single hairline divider (`#23252A`) does the work that boxes and cards do in other apps. The feeling is closer to a terminal than a consumer app — deliberate, dense, and quiet.

The accent system is a single color used with discipline: Linear Purple `#5E6AD2`. It marks the primary action, the focused command-menu row, the active selection, and the in-progress cycle bar — nothing else. Status is communicated through a precise iconographic language (backlog circle, started half-circle, done check) and small priority bar glyphs, not through a rainbow of fills. The restraint is the brand: when something turns purple, it is *the* thing you should act on. Text is the loudest element on screen — `#F7F8F8` near-white for titles, `#8A8F98` cool gray for the metadata that surrounds every issue.

Typography is Inter, used at 400 / 500 / 600 with a compact 11–28pt range. There are no display headlines because the product has no marketing surface — the issue *title* is the headline. The most expressive typographic moment is the command menu (Cmd+K), where a single input line and a tight list of monospace-feeling rows let you navigate the entire app without lifting your hands. Line height is tight, letter-spacing is slightly negative on titles, and density is the governing principle: more issues visible means less scrolling means faster work.

**Key Characteristics:**
- Ultra-dark canvas (`#08090A`) — near-OLED black, darker than typical "dark mode"
- Single accent — Linear Purple (`#5E6AD2`) for primary action, focus, and selection only
- Dense issue-list rows: status icon → identifier → title → priority bars → assignee, ~44pt tall
- Command menu (Cmd+K) — a centered sheet that drives every action in the app
- Iconographic status system (backlog / started / done) instead of colored fills
- Inter at 400/500/600, 11–28pt — no display type, the issue title is the headline
- Sidebar / list navigation — there is no bottom tab bar; navigation is a slide-over sidebar
- Instant motion — transitions are near-zero with a subtle spring; speed is the aesthetic

## 2. Color Palette & Roles

### Primary
- **Linear Purple** (`#5E6AD2`): Primary CTA, focused command-menu row, active list selection, in-progress cycle bar, active toggle.
- **Purple Pressed** (`#4F58B8`): Active/pressed state for purple CTAs and the focused row on key-down.
- **Purple Tint** (`rgba(94,106,210,0.14)`): Selection background on list rows, subtle focus wash behind the active item.

### Canvas & Surface (Dark — primary)
- **Canvas** (`#08090A`): Primary near-black canvas — the default app background.
- **Surface 1** (`#141516`): Elevated rows, the command-menu sheet body, popovers.
- **Surface 2** (`#1C1D1F`): Higher elevation — modals, context menus, the active/hover row.
- **Surface 3** (`#232428`): Pressed state on dark surfaces, input field fill.
- **Divider** (`#23252A`): Hairline dividers between issue rows and section groups.
- **Text Primary** (`#F7F8F8`): Issue titles, screen headlines, primary UI text.
- **Text Secondary** (`#8A8F98`): Issue identifiers, metadata, timestamps, section labels.
- **Text Tertiary** (`#5C5F6A`): Disabled state, placeholder text, very-low-emphasis labels.

### Status System
- **Backlog** (`#8A8F98`): Dashed/empty circle — issue is in the backlog.
- **Todo / Unstarted** (`#8A8F98`): Solid thin circle, no fill.
- **In Progress** (`#F2C94C`): Half-filled circle — actively being worked.
- **In Review** (`#5E6AD2`): Purple ring partial — under review.
- **Done** (`#5E6AD2`): Filled circle with check.
- **Canceled** (`#5C5F6A`): Filled gray circle with cross.

### Priority
- **Urgent** (`#F2C94C` / red dot `#EB5757`): Filled urgent glyph.
- **High / Medium / Low** (`#8A8F98`): Three / two / one ascending bars; filled = set, dimmed = unset.

### Semantic
- **Success Green** (`#4CB782`): Completed cycle, success toast.
- **Warning Amber** (`#F2C94C`): Overdue, attention states.
- **Error Red** (`#EB5757`): Destructive actions, failed sync.
- **Label Pills**: User-defined per workspace; rendered as a 6pt dot + name, color is data not chrome.

### Light Mode (Limited Use)
Linear is dark-first; a light variant exists for users who opt in.
- **Light Canvas** (`#FFFFFF`)
- **Light Surface** (`#F4F5F8`)
- **Light Text** (`#08090A`)
- **Light Secondary** (`#6B6F76`)

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (by Rasmus Andersson) — variable, used at 400 / 500 / 600
- **Monospace**: `Berkeley Mono` / `SF Mono` fallback — issue identifiers, code blocks, command-menu shortcuts
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Feature Settings**: `cv11`, `ss03`, tabular numbers on counts and identifiers (`font-variant-numeric: tabular-nums`)

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Inter | 28pt | 600 | 1.2 | -0.4pt | "Inbox", "My Issues" nav large title |
| View Title | Inter | 22pt | 600 | 1.25 | -0.3pt | "Cycle 14", project / team header |
| Section Header | Inter | 17pt | 600 | 1.3 | -0.2pt | "In Progress" group header in a list |
| Issue Title | Inter | 15pt | 500 | 1.35 | -0.1pt | The issue row primary text |
| Body | Inter | 15pt | 400 | 1.5 | 0pt | Issue description, comments |
| Identifier | SF Mono | 13pt | 500 | 1.3 | 0pt | "ENG-1423" issue id, tabular |
| Metadata | Inter | 13pt | 400 | 1.35 | 0pt | Assignee name, "2d", "updated 3h ago" |
| Label Pill | Inter | 12pt | 500 | 1.0 | 0pt | "bug", "feature" pill text |
| Command Row | Inter | 14pt | 500 | 1.3 | -0.1pt | A row in the Cmd+K menu |
| Command Shortcut | SF Mono | 12pt | 500 | 1.0 | 0pt | "⌘K", "G then I" trailing hint |
| Meta / Count | Inter | 12pt | 400 | 1.3 | 0pt | Sub-issue counts, "12 issues" |
| Button (Primary) | Inter | 14pt | 600 | 1.0 | -0.1pt | "Create issue", "Save" |
| Button (Secondary) | Inter | 14pt | 500 | 1.0 | 0pt | Ghost / outline buttons |
| Tiny Label (UPPER) | Inter | 11pt | 600 | 1.2 | 0.4pt | "TODO", group caps labels |
| Sidebar Item | Inter | 14pt | 500 | 1.3 | -0.1pt | "Inbox", "My Issues" sidebar rows |

### Principles
- **Weights concentrated at 400 / 500 / 600**: Regular for body, medium for titles and rows, semibold for headers and primary buttons — no light, no bold/black
- **Tabular numerics everywhere counts appear**: Identifiers, issue counts, and dates use `tabular-nums` so columns never jitter
- **Mono for identity, sans for content**: Issue IDs and keyboard shortcuts are monospace; everything human-readable is Inter
- **Negative tracking on titles only**: -0.4pt at 28pt easing to 0pt at body; metadata sits at 0pt
- **Density over drama**: Line heights are tight (1.2–1.35 on UI text) to maximize visible issues per screen

## 4. Component Stylings

### Buttons

**Primary Button ("Create issue", "Save")**
- Background: `#5E6AD2`
- Text: `#FFFFFF`, Inter 14pt weight 600
- Padding: 8pt vertical, 14pt horizontal
- Corner radius: 8pt
- Height: 32pt
- Pressed: background `#4F58B8`, scale 0.98, soft haptic
- Disabled: background `#1C1D1F`, text `#5C5F6A`

**Secondary / Ghost Button**
- Background: transparent
- Text: `#F7F8F8`, Inter 14pt weight 500
- Border: 1pt solid `#23252A`
- Padding: 8pt vertical, 14pt horizontal
- Corner radius: 8pt
- Pressed: background `#1C1D1F`

**Icon Button (toolbar — filter, display, more)**
- Size: 18pt glyph, 32pt hit target
- Default: `#8A8F98`
- Hover/active: `#F7F8F8`, background `#1C1D1F`, 6pt radius
- Spacing: 4pt between adjacent toolbar icons

**Quick-Add Button (the "+" / new issue)**
- 28pt, `#8A8F98` glyph, transparent; on press background `#1C1D1F`, glyph `#F7F8F8`
- In the toolbar this is the entry point to the issue composer sheet

### Cards & Containers

**Issue Row (the signature element)**
- Height: 44pt (compact) / 52pt (comfortable)
- Layout: 16pt status icon → 13pt mono identifier → 15pt title (1-line, ellipsized) → flex spacer → priority bars → 20pt assignee avatar
- Background: transparent (canvas `#08090A`)
- Hover/active: background `#1C1D1F`
- Selected: background `rgba(94,106,210,0.14)`, 2pt `#5E6AD2` left accent inset
- Divider: 1pt `#23252A` between rows when grouped headers are present
- Pressed: background `#232428`, no scale (speed > flourish)

**Group Header (status / assignee swimlane)**
- Height: 36pt
- Status icon + label (17pt w600) + count chip (`#8A8F98`, 12pt) + collapse chevron
- Background: `#08090A`, sticky on scroll
- Tap chevron: collapse/expand the group with a 150ms height spring

**Command Menu Sheet (Cmd+K)**
- Width: 560pt (regular) / full-width minus 24pt (compact)
- Background: `#141516`, 1pt border `#23252A`, corner radius 12pt
- Shadow: `rgba(0,0,0,0.6) 0 24px 64px`
- Top: 44pt search input, no border, leading `magnifyingglass` 16pt `#8A8F98`
- Rows: 40pt each — leading icon → label (14pt w500) → trailing shortcut (mono 12pt `#8A8F98`)
- Focused row: background `rgba(94,106,210,0.14)`, label `#F7F8F8`
- Section captions: 11pt w600 UPPER `#8A8F98`, 8pt top padding

**Detail Panel (issue open)**
- Full-screen on iPhone, slide-in from right
- Title field 22pt w600, editable inline
- Property rows: status, assignee, priority, labels, cycle, project — each a 36pt tappable row with leading icon + value
- Description below in 15pt w400 body, markdown rendered
- Activity feed at bottom: avatar + name + action + timestamp

### Navigation

**Sidebar (no bottom tab bar)**
- Slide-over from the left, 280pt wide, background `#08090A`
- Sections: workspace switcher (top), then Inbox, My Issues, then Teams (each expandable to Issues / Cycles / Projects)
- Item height: 32pt, icon 16pt + label 14pt w500
- Active item: background `rgba(94,106,210,0.14)`, label `#F7F8F8`, 16pt icon `#5E6AD2`
- Inactive: label `#8A8F98`
- Opened via hamburger or edge-swipe; dims canvas behind with `rgba(0,0,0,0.5)`

**Top Bar (view context)**
- Height: 44pt + safe area
- Leading: sidebar toggle (18pt) ; Center: view title (17pt w600) ; Trailing: filter + display + new-issue icons (18pt)
- Below: optional filter chip row (28pt pills) when filters are active
- No blur — opaque `#08090A`; speed favors no compositing cost

**Tab Switcher (within a team)**
- Segmented inline text tabs: "Issues" / "Cycles" / "Projects"
- Active: `#F7F8F8` with a 2pt `#5E6AD2` underline; inactive `#8A8F98`
- 14pt w500, 16pt horizontal gap

### Input Fields

**Command / Search Input**
- Background: transparent (it sits inside the `#141516` sheet)
- Height: 44pt
- Leading SF Symbol `magnifyingglass`, 16pt `#8A8F98`
- Placeholder: "Type a command or search…", 14pt w400 `#5C5F6A`
- Text: `#F7F8F8`; no border, blinking caret `#5E6AD2`

**Inline Text Field (issue title in composer)**
- Background: `#141516`
- Height: auto, min 40pt
- Corner radius: 8pt, 1pt border `#23252A`
- Focus: border `#5E6AD2`, no glow
- Placeholder: "Issue title", 15pt w400 `#5C5F6A`

**Status / Assignee Dropdown**
- Trigger: a 28pt pill showing current value (icon + text 13pt)
- Menu: `#1C1D1F`, 1pt border `#23252A`, 8pt radius, rows 32pt
- Selected row: leading check `#5E6AD2`; hover row background `#232428`

### Distinctive Components

**Command Menu (Cmd+K)**
- The single most important surface — invoked by ⌘K (hardware keyboard) or a toolbar entry
- Centered sheet, fades + scales in from 0.96 in 120ms
- Fuzzy-filters every action: create issue, assign, change status, navigate to team, switch workspace
- Each row shows its keyboard shortcut on the trailing edge in mono
- Arrow keys move the purple focus wash; Enter executes; Esc dismisses instantly

**Cycle Progress Bar**
- A horizontal track at the top of a cycle view, height 6pt, radius 3pt
- Track `#1C1D1F`; fill `#5E6AD2` proportional to completed scope
- Scope-change markers as 1pt ticks; completed segment is solid, in-progress segment is 60% opacity
- Caption below: "14 of 23 completed · 4 days left" in 12pt `#8A8F98`

**Status Icon Set**
- 16pt vector glyphs drawn, not emoji: dashed circle (backlog), thin ring (todo), half-pie (in progress), purple ring (in review), check-fill (done), cross-fill (canceled)
- Tapping opens the status dropdown inline; the icon animates between states with a 150ms morph

**Priority Bars**
- A 14pt-wide glyph of three ascending bars
- Urgent renders as a filled amber/red square with `!`; High = 3 filled, Medium = 2, Low = 1, None = 3 dimmed
- Always right-aligned in the issue row before the assignee

**Label Pills**
- 6pt colored dot + label text (12pt w500) in a `#1C1D1F` rounded container, radius 6pt
- Multiple labels stack horizontally with 4pt gaps; overflow collapses to "+2"
- The dot color is workspace data — never use it for UI chrome elsewhere

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 6, 8, 12, 16, 20, 24, 32, 40, 56
- Standard margin: 16pt horizontal on lists; 8pt internal row padding
- Issue rows touch (0pt between) — separation is via hover state and group headers, not gaps

### Grid & Container
- Content width: full device width, 16pt horizontal insets
- Issue list: single column, full-bleed rows
- Command menu: centered, max 560pt, never full-bleed on regular width
- Detail panel: full-screen on iPhone; 2-pane (list + detail) only on iPad landscape

### Whitespace Philosophy
- **Density is the product**: 44pt rows pack the maximum number of issues into a viewport — Linear is a triage tool
- **Whitespace is reserved for the detail view**: When you open an issue, the description gets generous 1.5 line-height and 16pt margins — reading mode, not list mode
- **No decorative empty space**: There are no hero areas, no oversized headers, no breathing room for its own sake

### Border Radius Scale
- Sharp (0pt): Dividers, full-bleed rows
- Subtle (6pt): Label pills, icon-button hover background, dropdown menus
- Standard (8pt): Buttons, input fields, status pills
- Comfortable (12pt): Command menu sheet, modals
- Circle (50%): Assignee avatars, workspace icon

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Issue rows, list canvas, group headers |
| Hover (Level 1) | Background shift to `#1C1D1F`, no shadow | Row hover/active — depth via color, not shadow |
| Popover (Level 2) | `rgba(0,0,0,0.4) 0 8px 24px` + 1pt `#23252A` border | Status/assignee dropdowns, label picker |
| Command Sheet (Level 3) | `rgba(0,0,0,0.6) 0 24px 64px` + 1pt border | Cmd+K menu, composer modal |
| Scrim | `rgba(0,0,0,0.5)` | Behind the sidebar slide-over and modals |

**Shadow Philosophy**: Linear avoids shadows almost entirely — elevation is communicated by a one-step background lift (`#08090A` → `#1C1D1F`) and a precise 1pt border, not by blur. The only true shadows are on floating surfaces (command menu, dropdowns) where a large, very dark, soft shadow separates them from the near-black canvas. The aesthetic is flat, sharp, and fast — shadows cost compositing time and Linear optimizes for instant.

### Motion
- **Command menu open**: opacity 0→1 + scale 0.96→1.0 over 120ms ease-out — fast enough to feel instant
- **Row selection**: background color cross-fade 90ms, no movement
- **Group collapse/expand**: height spring 150ms (response 0.3, damping 0.85)
- **Status change**: 150ms glyph morph between status icons
- **Sidebar slide-over**: translateX spring 220ms (response 0.35, damping 0.9) with scrim fade
- **Detail panel push**: slide-in-from-right 240ms ease-out; no parallax — speed over spectacle
- **Haptic**: `.impactOccurred(.light)` on primary action commit, status change, and command execute

## 7. Do's and Don'ts

### Do
- Use `#08090A` as the canvas — near-OLED black, darker than `#121212`
- Reserve Linear Purple (`#5E6AD2`) for primary action, focus, and selection — it is the verb
- Keep issue rows at ~44pt — density is the value proposition
- Use the iconographic status system (drawn glyphs) instead of colored fills or text badges
- Make the command menu (Cmd+K) reachable from anywhere and lightning fast
- Use Inter at 400/500/600 with tabular numerics on counts and identifiers
- Communicate elevation with a one-step background lift + 1pt border, not big shadows
- Render keyboard shortcuts in monospace on the trailing edge of command rows
- Animate fast (90–240ms) — every transition should feel near-instant

### Don't
- Don't use `#121212` — Linear is darker; the near-OLED black is part of the identity
- Don't introduce a second accent color — purple alone carries action and focus
- Don't add a bottom tab bar — navigation is the slide-over sidebar
- Don't pad issue rows for "breathing room" — that destroys the density that makes Linear fast
- Don't use heavy weights — Inter stops at 600 here; no bold titles
- Don't use colored status badges with text — the drawn icon system *is* the language
- Don't add large drop shadows on rows or cards — flat + 1pt border is the look
- Don't animate slowly — anything over ~250ms feels broken in Linear
- Don't use the label dot color anywhere except labels — it is data, not chrome

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Compact 44pt rows, identifier may truncate before title |
| iPhone 13/14/15 | 390pt | Standard 44pt rows, full identifier + title |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the 44pt top bar |
| iPhone 15/16 Pro Max | 430pt | Comfortable 52pt rows option, wider assignee column |
| iPad | 768pt+ | 2-pane: persistent sidebar + list + detail; command menu stays 560pt centered |

### Dynamic Type
- Issue titles, body, metadata: scale with Dynamic Type
- Issue identifiers: tabular mono, scales but never wraps (truncates with ellipsis)
- Status / priority glyphs: fixed 16pt / 14pt (layout-critical)
- Command shortcuts: fixed mono 12pt (never scales)

### Orientation
- Issue list / detail: **portrait and landscape**
- iPad: landscape unlocks the 2-pane master-detail layout
- Command menu: always centered, re-centers on rotation

### Touch Targets
- Issue row: full 44pt height tappable
- Status / priority glyphs: 16pt visual, 32pt hit area
- Toolbar icons: 18pt visual, 32pt hit area
- Command rows: full 40pt height

### Safe Area Handling
- Top: top bar respects safe area / Dynamic Island
- Bottom: composer sheet and list respect the home indicator
- Sides: 16pt content insets; sidebar slide-over starts at the screen edge

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#08090A`
- Surface 1 (sheet/rows): `#141516`
- Surface 2 (modal/hover): `#1C1D1F`
- Surface 3 (pressed/input): `#232428`
- Divider: `#23252A`
- Primary text: `#F7F8F8`
- Secondary text: `#8A8F98`
- Tertiary text: `#5C5F6A`
- Linear Purple (action/focus): `#5E6AD2`
- Purple pressed: `#4F58B8`
- In-progress amber: `#F2C94C`
- Error red: `#EB5757`

### Example Component Prompts
- "Create a SwiftUI Linear issue row: 44pt height on canvas #08090A. Leading 16pt status glyph (a half-filled circle for In Progress in #F2C94C), then 'ENG-1423' in SF Mono 13pt weight 500 #8A8F98, then title 'Fix flaky auth redirect' in Inter 15pt weight 500 #F7F8F8 (1-line ellipsized), a flexible spacer, a 14pt priority-bars glyph, and a 20pt circular assignee avatar. Hover/active background #1C1D1F; selected background rgba(94,106,210,0.14) with a 2pt #5E6AD2 left inset."
- "Build the Linear command menu: a 560pt-wide centered sheet, background #141516, 1pt #23252A border, 12pt corner radius, shadow rgba(0,0,0,0.6) 0 24px 64px. Top is a 44pt search input with a leading 16pt magnifyingglass #8A8F98 and placeholder 'Type a command or search…' in 14pt #5C5F6A. Below, 40pt rows: leading icon, label in Inter 14pt weight 500, trailing keyboard shortcut in SF Mono 12pt #8A8F98. The focused row has background rgba(94,106,210,0.14). Opens with opacity 0→1 + scale 0.96→1 over 120ms."
- "Design the Linear cycle progress bar: a 6pt-tall track, radius 3pt, background #1C1D1F, with a #5E6AD2 fill at 60% width. Below it, caption '14 of 23 completed · 4 days left' in Inter 12pt #8A8F98."
- "Create the Linear primary button: background #5E6AD2, text 'Create issue' in Inter 14pt weight 600 #FFFFFF, 8pt vertical / 14pt horizontal padding, 8pt corner radius, 32pt tall. Pressed: background #4F58B8, scale 0.98, light haptic."
- "Build a Linear status dropdown: a 28pt trigger pill showing a status glyph + 'In Progress' in 13pt. The menu is #1C1D1F with a 1pt #23252A border, 8pt radius, 32pt rows; the selected row has a leading #5E6AD2 check and hover rows go to #232428."

### Iteration Guide
1. Canvas is `#08090A` — near-OLED black, NOT `#121212`
2. Linear Purple is the only accent — primary action, focus, and selection; never a second color
3. Issue rows are dense (~44pt) — resist any urge to pad them
4. Status is a drawn icon language (backlog/started/done), not colored text badges
5. The command menu (Cmd+K) is the soul of the app — fast, fuzzy, keyboard-first
6. Typography is Inter 400/500/600 with tabular numerics on counts and IDs; mono for identifiers and shortcuts
7. Elevation = one-step background lift + 1pt border; real shadows only on floating sheets
8. Motion is near-instant (90–240ms) — speed is the aesthetic, never animate slowly
