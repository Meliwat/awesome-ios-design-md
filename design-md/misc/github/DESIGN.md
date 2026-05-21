# Design System Inspiration of GitHub (iOS)

## 1. Visual Theme & Atmosphere

GitHub's iOS app is the **Primer design system** rendered for mobile — a developer's tool that is dense, information-first, and engineered to make code, diffs, and project state legible at a glance. The default experience is **dark mode** (Primer "dark default"): a deep blue-tinted near-black canvas (`#0D1117`) with surfaces stepping up in measured increments — `#161B22` (subtle/raised), `#21262D` (buttons, inset rows) — each separated by a precise `#30363D` border. Light mode (`#FFFFFF` canvas, `#F6F8FA` subtle) is a first-class peer. The atmosphere is utilitarian and trustworthy: no flourish, no gratuitous motion, every pixel devoted to repository content.

The signature screens — the ones that must be reproduced faithfully — are the **repository home** and the **code browser**. The repo home leads with `owner / name` (both segments in accent blue `#2F81F7`, the name bold), a one-line description, language dot + star + fork stats, a horizontally-scrolling tab strip (**Code · Issues · Pull requests · Actions**) with an orange (`#F78166`) active underline, a branch pill, a latest-commit strip, the file tree, and then the **README rendered inline** in a bordered card. The code browser is a monospace file viewer with a sticky path breadcrumb. This "repo header → file tree → inline README" composition is GitHub's home and the heart of the product.

Color in GitHub is **semantic and disciplined**. There is one link/accent blue (`#2F81F7`), and one famous green — `#238636` — that is *strictly reserved* for the "**Code**" download button and the "**Merge pull request**" button (it literally signals "go / merge"). State is color-coded by meaning, not decoration: an **open** issue/PR is green (`#3FB950`), a **merged** PR is purple (`#A371F7`), a **closed** issue/PR is red (`#F85149`), a **draft** is grey. The most recognizable GitHub visual of all is the **contributions heatmap** — a grid of squares ramping `#161B22 → #0E4429 → #006D32 → #26A641 → #39D353` ("Less → More"). These colors are not interchangeable; each carries a fixed meaning a developer reads instantly.

Typography is **Mona Sans** (GitHub's own variable grotesque, OFL) for UI, with a monospace (`SF Mono` / GitHub's "Monaspace" / `JetBrains Mono` as fallbacks) for **all code, SHAs, file names in trees, and diffs**. The monospace is load-bearing: a commit SHA, a branch name, a file path, and a code blob are always monospace because alignment and character distinction matter. Body UI text is 14pt regular; the type system is conventional and reading-optimized — the personality is in the semantic color and the dense, structured layouts, not the letterforms.

**Key Characteristics:**
- Primer dark default: canvas `#0D1117`, surfaces `#161B22` / `#21262D`, borders `#30363D`; light mode a first-class peer
- One accent blue (`#2F81F7`) for links/active; one reserved green (`#238636`) for **Code** / **Merge** buttons only
- Repo home: `owner / name` + description + language/stars/forks + tab strip + branch pill + file tree + inline README
- Code browser: monospace file viewer with sticky path breadcrumb and a latest-commit strip
- Semantic state colors: open = green `#3FB950`, merged = purple `#A371F7`, closed = red `#F85149`, draft = grey
- Contributions heatmap: 5-step ramp `#161B22 → #0E4429 → #006D32 → #26A641 → #39D353`
- Issue/PR labels as bordered pills tinted by the label's own color (bug red, enhancement purple, etc.)
- Mona Sans for UI; monospace for ALL code, SHAs, file names, branch names, diffs
- Diff rendering: additions on a green wash, deletions on a red wash, line numbers in a muted gutter
- Octocat mark + the "Code" `<>` button as fixed brand anchors
- Bottom tab bar (Home / Notifications / Explore / Profile) with an orange-coral active underline on segmented strips

## 2. Color Palette & Roles

### Primary (Interactive)
- **Accent FG** (`#2F81F7` dark / `#0969DA` light): Hyperlinks, `owner/name`, active text, selected states, focus rings.
- **Accent Emphasis** (`#1F6FEB` dark / `#0969DA` light): Pressed link, primary-info button fill.
- **Button Primary** (`#238636` dark / `#1F883D` light): RESERVED — the "Code" download button and "Merge pull request" button only.
- **Button Primary Hover** (`#2EA043` dark / `#1A7F37` light): Pressed/hover of the green button.

### Canvas & Surfaces (Dark — default)
- **Canvas Default** (`#0D1117`): Primary canvas — blue-tinted near-black.
- **Canvas Subtle** (`#161B22`): Raised surfaces, README/file-list card headers, contributions level-0.
- **Canvas Inset / Surface** (`#21262D`): Default button background, code-block background, inset rows, count pills.
- **Surface Pressed** (`#30363D`): Pressed rows, hovered list items.
- **Border Default** (`#30363D`): Primary 1pt borders (cards, file list, buttons).
- **Border Muted** (`#21262D`): Hairline row dividers inside lists.

### Canvas & Surfaces (Light)
- **Canvas Default Light** (`#FFFFFF`): Primary canvas.
- **Canvas Subtle Light** (`#F6F8FA`): Raised surfaces, code blocks, card headers.
- **Border Default Light** (`#D0D7DE`): Primary borders.
- **Border Muted Light** (`#D8DEE4`): Hairline dividers.

### Text (Foreground)
- **FG Default** (`#E6EDF3` dark / `#1F2328` light): Primary text, file names, headings.
- **FG Muted** (`#7D8590` dark / `#656D76` light): Descriptions, metadata, secondary text.
- **FG Subtle** (`#6E7681` dark / `#6E7781` light): Timestamps, very low-emphasis, placeholders.
- **FG on Emphasis** (`#FFFFFF`): Text on green/blue/red emphasis fills.

### Semantic State
| Role | Dark | Light | Meaning |
|------|------|-------|---------|
| Open (success) | `#3FB950` | `#1A7F37` | Open issue/PR, passing check, success label |
| Merged (done) | `#A371F7` | `#8250DF` | Merged PR (purple is "done") |
| Closed (danger) | `#F85149` | `#CF222E` | Closed issue/PR, failing check, delete |
| Draft / Neutral | `#6E7681` | `#6E7781` | Draft PR, neutral state |
| Attention | `#D29922` | `#9A6700` | Pending check, warning, "needs attention" |
| Sponsor (pink) | `#DB61A2` | `#BF3989` | Sponsor heart, GitHub Sponsors |

### Contributions Heatmap (the iconic ramp)
| Level | Dark | Light |
|-------|------|-------|
| 0 (none) | `#161B22` | `#EBEDF0` |
| 1 | `#0E4429` | `#9BE9A8` |
| 2 | `#006D32` | `#40C463` |
| 3 | `#26A641` | `#30A14E` |
| 4 (most) | `#39D353` | `#216E39` |

### Diff Colors
- **Addition line bg** (`rgba(63,185,80,0.15)` dark) · marker `#3FB950`
- **Deletion line bg** (`rgba(248,81,73,0.15)` dark) · marker `#F85149`
- **Diff gutter** (`#6E7681` line numbers on `#161B22`)

## 3. Typography Rules

### Font Family
- **UI**: **Mona Sans** (by GitHub, SIL OFL) — a variable grotesque. Fallback `-apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif`.
- **Monospace**: **Monaspace** / `SF Mono` on iOS; `'JetBrains Mono', ui-monospace, SFMono-Regular, Menlo, monospace` as the open fallback. Used for **all** code, SHAs, file/branch names, diffs, inline `code`.
- **Heading variant**: Mona Sans (GitHub uses one family for display + body; weight + size establish hierarchy).

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Mona Sans | 32pt | 800 | 1.15 | -0.4pt | Marketing/empty-state hero |
| Repo Title | Mona Sans | 24pt | 700 | 1.2 | -0.3pt | `owner / name` heading |
| Section | Mona Sans | 20pt | 600 | 1.3 | -0.2pt | "Pull requests", profile sections |
| Subtitle | Mona Sans | 16pt | 600 | 1.4 | 0pt | "Latest commit", card titles |
| Body | Mona Sans | 14pt | 400 | 1.5 | 0pt | Descriptions, issue/PR body |
| Body Strong | Mona Sans | 14pt | 600 | 1.5 | 0pt | File names, emphasized inline |
| Code / Blob | Monospace | 13pt | 400 | 1.45 | 0pt | Code viewer, diffs, code blocks |
| Code Inline | Monospace | 13pt | 400 | 1.4 | 0pt | `inline code` on `#21262D` |
| SHA / Branch | Monospace | 12pt | 600 | 1.0 | 0pt | Commit SHA, branch chips |
| Metadata | Mona Sans | 12pt | 400 | 1.4 | 0pt | "opened 3 days ago by …" |
| Label | Mona Sans | 12pt | 500 | 1.0 | 0pt | Issue labels, state pills |
| Count Pill | Mona Sans | 11pt | 600 | 1.0 | 0pt | Tab counts ("2.3k") |
| Tab Label | Mona Sans | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Caption | Mona Sans | 11pt | 400 | 1.3 | 0pt | Heatmap legend, fine print |

### Principles
- **Monospace is the contract**: code, SHAs, file names in trees, branch names, and diffs are ALWAYS monospace — alignment and glyph distinction (`0` vs `O`, `l` vs `1`) are functional, not stylistic.
- **Weight + size for hierarchy**: one family (Mona Sans); 800 display, 700 repo title, 600 sections/subtitles/strong, 400 body. No thin.
- **Reading-optimized, not expressive**: the type system is conventional; GitHub's identity is the semantic color and dense structured layouts.
- **Numbers and counts are tabular**: star/fork counts, tab counts, line numbers use tabular figures so columns align.
- **Dynamic Type**: titles, body, code scale; tab labels, count pills, SHAs stay fixed (table/tree-layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary Button (Code / Merge pull request — RESERVED green)**
- Shape: rounded rectangle, 6pt corner radius
- Background: `#238636` (dark) / `#1F883D` (light) — used ONLY for "Code" download and "Merge pull request"
- Text/icon: `#FFFFFF`
- Border: 1pt `rgba(240,246,252,0.1)` (subtle top highlight)
- Padding: 10pt vertical, 16pt horizontal
- Font: Mona Sans 14pt weight 600
- Pressed: background `#2EA043`

**Default Button (Watch / Fork / Star / secondary actions)**
- Background: `#21262D` (dark) / `#F6F8FA` (light)
- Text: `#E6EDF3` (dark) / `#24292F` (light)
- Border: 1pt `#30363D` (dark) / `#D0D7DE` (light)
- Corner radius: 6pt
- Padding: 10pt vertical, 16pt horizontal
- Font: Mona Sans 14pt weight 600
- Pressed: background `#30363D`

**Danger Button (Close issue / Delete)**
- Background: `#21262D`, text `#F85149`, border 1pt `#30363D`
- On confirm/destructive primary: background `#DA3633`, text `#FFFFFF`
- Corner radius: 6pt, same padding/font as default

**Link / Text Button (View all branches / counterfactual nav)**
- Font: Mona Sans 14pt weight 500
- Color: `#2F81F7` (accent)
- No background, no border
- Pressed: underline

**Counter Button (Star with count)**
- Two-segment: a default button + an attached count pill (`#161B22` bg, `#7D8590` text, left border)

### Repository Components (the core)

**Repo Header**
- Octocat-less; leads with a repo glyph (17pt `#7D8590`) + `owner` (`#2F81F7`) + `/` (`#7D8590`) + `name` (`#2F81F7` bold), Mona Sans 15–24pt
- Description: Mona Sans 13–14pt `#7D8590`
- Stats row: language dot (11pt circle, language color e.g. TS `#3178C6`) + label, star (`#7D8590` + count), fork (`#7D8590` + count), Mona Sans 12pt

**Tab Strip (Code / Issues / Pull requests / Actions)**
- Horizontally scrollable; each item Mona Sans 14pt
- Inactive: `#7D8590`; Active: `#E6EDF3` weight 600 + 2pt **orange-coral** underline `#F78166`
- Count badge: `#161B22` pill, `#6E7681` text, Mona Sans 11pt w600

**Branch Pill**
- `#161B22` bg, 1pt `#30363D` border, 6pt radius
- Leading branch glyph (13pt), branch name in **monospace** 12pt w600 `#E6EDF3`

**Latest-Commit Strip**
- `#161B22` bg, bottom border `#30363D`
- Avatar (18pt circle) + commit message (`#E6EDF3` truncated) + **monospace** short SHA (`#7D8590` 11pt) + relative time

**File Tree Row**
- Folder glyph `#7D8590` (filled) or file glyph `#7D8590` (outline), 15pt
- File/dir name: `#E6EDF3` 13pt (monospace for file names is acceptable; GitHub uses sans for the tree list label but mono in the path breadcrumb)
- Trailing: last-commit message (`#7D8590` 12pt, truncated) + relative time (`#6E7681` 11pt)
- Row divider: 1pt `#21262D`

**Inline README Card**
- 1pt `#30363D` border, 8pt radius
- Header: file glyph + "README.md" (`#E6EDF3` 13pt w600) on `#161B22`, bottom border `#30363D`
- Body: rendered markdown — `h1` 19pt w700 with a `#21262D` bottom rule; body 13pt `#7D8590`; `code` chips on `#21262D`; status badges as small monospace pills

### State & Labels

**State Pill (Open / Merged / Closed / Draft)**
- Pill, 999pt radius, Mona Sans 12pt w600, `#FFFFFF` text
- Open `#3FB950` (leading ● dot or issue-opened glyph), Merged `#A371F7` (⑂ merge glyph), Closed `#F85149` (● or x glyph), Draft `#6E7681` (○)
- Padding: 5pt vertical, 12pt horizontal

**Issue Label (bug / enhancement / good first issue / custom)**
- Pill, 999pt radius, 1pt border, Mona Sans 12pt w500
- Color-tinted by the label's own color: text = label color, border = label color @ 40%, fill = label color @ 10%
- Examples: bug `#F85149`, enhancement `#A371F7`, good first issue `#3FB950`, documentation `#0075CA`

### Contributions Heatmap (signature)

- Grid of squares, each `aspect-ratio: 1`, 2pt radius, 3pt gap
- Fill by activity level: L0 `#161B22` (1pt `rgba(110,118,129,0.15)` border) · L1 `#0E4429` · L2 `#006D32` · L3 `#26A641` · L4 `#39D353`
- Legend row: "Less" + 5 swatches L0→L4 + "More", Mona Sans 11pt `#7D8590`
- Tap a square → tooltip "{n} contributions on {date}"

### Diff Viewer (signature for PRs)

- Monospace blob, 13pt, `#161B22` background
- Addition line: `rgba(63,185,80,0.15)` row bg, leading `+` marker `#3FB950`
- Deletion line: `rgba(248,81,73,0.15)` row bg, leading `-` marker `#F85149`
- Line-number gutter: `#6E7681` on `#0D1117`, right-aligned, 1pt right border `#21262D`
- File header: filename (monospace `#E6EDF3`) + additions `+N` (`#3FB950`) / deletions `-N` (`#F85149`)

### Navigation

**Bottom Tab Bar**
- Height: 66pt + safe area
- Background: `rgba(13,17,23,0.94)` with `backdrop-filter: blur(20px)`, 1pt top border `#21262D`
- Tabs (4): Home, Notifications, Explore, Profile
- Icon size: 22pt
- Active: glyph + label `#E6EDF3` (thicker stroke); no tint pill
- Inactive: `#7D8590`
- Labels: Mona Sans 10pt w500, always shown
- Notifications tab shows a `#F85149` unread dot when applicable

**Top Bar (repo view)**
- Height: 44pt + safe area
- Leading: back chevron `#7D8590`; Trailing: overflow `⋯` (repo actions)
- Title appears on scroll (compact `owner/name`)

### Input Fields

**Search Bar (Explore / repo file finder)**
- Height: 40pt, `#0D1117` bg, 1pt `#30363D` border, 6pt radius
- Leading `magnifyingglass` 16pt `#7D8590`
- Placeholder: "Search or jump to…" 14pt `#6E7681`
- Focus: 1pt `#2F81F7` border + 3pt `rgba(47,129,247,0.3)` ring

**Comment Composer (issues/PRs)**
- Multi-line, `#0D1117` bg, 1pt `#30363D` border, 6pt radius
- "Write / Preview" segmented tabs on top; Markdown toolbar
- Submit = green `#238636` "Comment" button (this green is allowed for the primary submit on a comment form)

## 5. Layout Principles

### Spacing System
- Base unit: 4pt — scale: 4, 8, 12, 16, 20, 24, 32, 48
- List row vertical padding: 9–12pt (dense)
- Card internal padding: 14–16pt
- Screen horizontal inset: 16pt

### Grid & Container
- iPhone: content at 16pt horizontal insets; file list / README in full-width bordered cards
- Tab strip scrolls horizontally; never wraps
- iPad: 2-pane — repo nav/file tree (left) + content (right); diff viewer can split old/new
- Code viewer: horizontal scroll for long lines (never wrap code)

### Whitespace Philosophy
- **Dense and structured**: this is a developer tool — rows are tight, information is maximized, every card has a clear bordered boundary
- **Borders define regions**: GitHub uses 1pt `#30363D` borders (not shadows) to delineate cards, lists, and the README — structure over softness
- **Stepped surfaces**: depth is communicated by luminance steps (`#0D1117 → #161B22 → #21262D`), not drop-shadows
- **Code breathes within structure**: code blocks get internal padding and their own surface, but the chrome around them stays tight

### Border Radius Scale
- Square (0pt): full-bleed media, diff line rows
- Subtle (4pt): code chips, contribution squares, badges
- Standard (6pt): buttons, branch pill, search, inputs
- Card (8pt): file list, README card, issue cards
- Comfortable (12pt): bottom sheets, modals
- Pill (999pt): state pills, issue labels, count badges
- Circle (50%): avatars

## 6. Depth & Elevation

GitHub is structurally flat — depth is **stepped surface luminance + 1pt borders**, not shadow.

| Level | Treatment | Use |
|-------|-----------|-----|
| Canvas (Level 0) | `#0D1117`, no border | Page background |
| Subtle (Level 1) | `#161B22` + 1pt `#30363D` | Cards, README, file-list headers |
| Inset (Level 1.5) | `#21262D` + 1pt `#30363D` | Buttons, code blocks, count pills |
| Floating (Level 2) | `#161B22` + 1pt `#30363D` + `rgba(1,4,9,0.85) 0 8px 24px` | Dropdowns (branch switcher, ⋯ menus), autocomplete |
| Sheet (Level 3) | `#161B22` + 12pt top radius + `rgba(1,4,9,0.85) 0 -8px 24px` | Bottom sheets (share, label picker) |
| Scrim (Level 4) | `rgba(1,4,9,0.8)` overlay | Behind modals/sheets |

**Shadow Philosophy**: On the dark canvas, shadows are minimal — a 1pt `#30363D` border is the primary "this is a distinct region" cue, reinforced by a luminance step. Only true overlays (menus, sheets) get a shadow, and it's a deep `rgba(1,4,9,0.85)` (near-black, blue-tinted) to read over `#0D1117`.

### Motion
- **Tab strip switch**: orange underline slides to the new tab over 200ms ease-out; content crossfades
- **Branch switcher**: dropdown fades + 4pt slide-down in 150ms ease-out; filter is instant
- **Star/Watch toggle**: button label flips ("Star" ↔ "Starred"), count increments with a 120ms count-up; subtle scale 1 → 0.97 → 1; soft haptic
- **Merge button**: on tap, button shows a spinner, then morphs to purple "Merged" state over 250ms; success haptic
- **Diff expand**: "Expand N lines" injects rows with a 150ms height ease-out
- **Pull-to-refresh**: standard iOS spinner; repo/feed refetches
- **List append (infinite scroll)**: new rows fade in over 150ms as they enter
- **Navigation**: native iOS push (300ms horizontal slide) between repo → file → blob
- **Haptic feedback**: soft impact on star/watch toggle and merge; selection tick on tab change

## 7. Do's and Don'ts

### Do
- Use Primer dark default: canvas `#0D1117`, surfaces `#161B22` / `#21262D`, borders `#30363D`
- Reserve green `#238636` for the "Code" download and "Merge pull request" buttons ONLY
- Use accent blue `#2F81F7` for links, `owner/name`, and active/selected states
- Color state semantically: open = green `#3FB950`, merged = purple `#A371F7`, closed = red `#F85149`, draft = grey
- Render the contributions heatmap with the exact 5-step ramp `#161B22 → #0E4429 → #006D32 → #26A641 → #39D353`
- Use monospace for ALL code, SHAs, file/branch names, and diffs — alignment is functional
- Define regions with 1pt `#30363D` borders + luminance steps, not drop-shadows
- Render the README inline in a bordered card on the repo home
- Give the active tab an orange-coral `#F78166` 2pt underline
- Tint issue labels by the label's own color (text + 40% border + 10% fill)
- Render diffs with green/red line washes and a muted line-number gutter

### Don't
- Don't use the green `#238636` for generic CTAs — it specifically means "download code / merge"
- Don't swap state colors — merged is ALWAYS purple, closed is ALWAYS red; developers read these instantly
- Don't render code, SHAs, or file paths in the sans face — monospace is the contract
- Don't use drop-shadows to separate cards — use borders + surface steps
- Don't wrap long code lines — code scrolls horizontally
- Don't use a pure-black canvas — Primer dark is the blue-tinted `#0D1117`
- Don't tint the active bottom tab with a pill — active is `#E6EDF3` glyph+label
- Don't recolor the contributions ramp — it's GitHub's single most recognizable visual
- Don't soften the dense layout with large whitespace — this is a developer tool, density is the point
- Don't use accent blue for body text — blue is links/active only
- Don't animate aggressively — motion is quiet, functional, 150–300ms ease-out

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Tab strip scrolls; stats may wrap to 2 lines |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top bar |
| iPhone 15/16 Pro Max | 430pt | Wider file tree; more visible heatmap weeks |
| iPad (portrait) | 768pt | 2-pane: file tree (left) + blob/README (right) |
| iPad (landscape) | 1024pt+ | 3-pane possible: nav + tree + content; split diff (old/new) |

### Dynamic Type
- Repo title, section, body, code: full scale
- Tab labels, count pills, SHAs, heatmap squares: FIXED (table/tree-layout-sensitive)
- Code blocks: scale to XL (stay monospace, still horizontally scroll)

### Orientation
- All screens support rotation
- iPad split view: persistent file tree (primary) + blob/diff (secondary)
- Diff viewer switches to side-by-side (old | new) in landscape/iPad

### Touch Targets
- Tab strip item: 44pt min hit, 14pt label
- File tree row: full-row 44pt hit
- Bottom tab icons: 22pt glyph, 44pt hit
- Heatmap square: small visual, 28pt effective hit (tooltip)
- Buttons: 36–44pt tall

### Safe Area Handling
- Top: top bar respects safe area and Dynamic Island
- Bottom: bottom tab bar respects home indicator; comment composer docks above keyboard
- Keyboard: file finder / comment composer scrolls above keyboard
- Sides: 16pt content inset; code blocks scroll horizontally within insets

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#0D1117` · (light): `#FFFFFF`
- Canvas subtle: `#161B22` dark / `#F6F8FA` light
- Surface / inset: `#21262D` dark / `#F6F8FA` light
- Border default: `#30363D` dark / `#D0D7DE` light
- FG default: `#E6EDF3` dark / `#1F2328` light
- FG muted: `#7D8590` dark / `#656D76` light
- Accent (link): `#2F81F7` dark / `#0969DA` light
- Button primary (Code/Merge): `#238636` dark / `#1F883D` light
- Open (success): `#3FB950` · Merged (done): `#A371F7` · Closed (danger): `#F85149` · Draft: `#6E7681`
- Attention: `#D29922` · Sponsor: `#DB61A2`
- Heatmap: `#161B22 → #0E4429 → #006D32 → #26A641 → #39D353`
- Tab active underline: `#F78166`

### Example Component Prompts
- "Build the GitHub repo header in SwiftUI: a repo glyph (17pt `#7D8590`) + `owner` (`#2F81F7`) + `/` (`#7D8590`) + `name` (`#2F81F7` bold) in Mona Sans 15pt; below, a description in Mona Sans 13pt `#7D8590`; a stats row with a language dot (11pt circle, language color) + label, a star glyph + count, a fork glyph + count in Mona Sans 12pt `#7D8590`. Canvas `#0D1117`."

- "Create the GitHub tab strip: horizontally-scrolling Mona Sans 14pt items 'Code', 'Issues', 'Pull requests', 'Actions'. Inactive `#7D8590`; active `#E6EDF3` weight 600 with a 2pt `#F78166` orange-coral underline. Count badges as `#161B22` pills with `#6E7681` Mona Sans 11pt w600 text."

- "Render the GitHub inline README card: 1pt `#30363D` border, 8pt radius. Header row: file glyph + 'README.md' (`#E6EDF3` 13pt w600) on `#161B22` with a bottom `#30363D` border. Body: rendered markdown — h1 19pt w700 with a `#21262D` bottom rule, body 13pt `#7D8590`, inline `code` on a `#21262D` chip, a status badge as a small monospace pill (`#3FB950` bg, `#0D1117` text)."

- "Build the GitHub contributions heatmap: a grid of `aspect-ratio: 1` squares, 2pt radius, 3pt gap, filled by activity level — L0 `#161B22` (1pt `rgba(110,118,129,0.15)` border), L1 `#0E4429`, L2 `#006D32`, L3 `#26A641`, L4 `#39D353`. Below: a legend 'Less' + 5 swatches L0→L4 + 'More' in Mona Sans 11pt `#7D8590`. Tap a square → tooltip '{n} contributions on {date}'."

- "Create GitHub state pills: rounded 999pt, Mona Sans 12pt w600, white text, 5pt×12pt padding. Open = `#3FB950` (leading ● / issue-opened glyph), Merged = `#A371F7` (merge glyph), Closed = `#F85149` (x glyph), Draft = `#6E7681` (○). Issue labels: 1pt-bordered pills tinted by the label's own color — text = label color, border = 40% alpha, fill = 10% alpha (bug `#F85149`, enhancement `#A371F7`, good first issue `#3FB950`)."

- "Build the GitHub diff viewer: monospace 13pt blob on `#161B22`. Addition lines get a `rgba(63,185,80,0.15)` row background + a `+` marker `#3FB950`; deletion lines get `rgba(248,81,73,0.15)` + a `-` marker `#F85149`. Line-number gutter: `#6E7681` numbers on `#0D1117`, right-aligned, 1pt right border `#21262D`. File header: filename (monospace `#E6EDF3`) + `+N` (`#3FB950`) `-N` (`#F85149`)."

- "Build the GitHub bottom tab bar: 66pt + safe area, `rgba(13,17,23,0.94)` 20px blur, 1pt `#21262D` top border. Four tabs: Home, Notifications, Explore, Profile — 22pt glyphs, Mona Sans 10pt w500 labels. Active = `#E6EDF3` glyph+label (thicker stroke, NO pill). Inactive = `#7D8590`. A `#F85149` unread dot on Notifications when applicable."

### Iteration Guide
1. Canvas is Primer dark `#0D1117` (blue-tinted near-black) — NOT pure black; surfaces step `#161B22 → #21262D`
2. Green `#238636` is RESERVED for "Code" download + "Merge pull request" — never a generic CTA
3. Accent blue `#2F81F7` is links / `owner/name` / active states only — never body text
4. State color is semantic and fixed: open = green `#3FB950`, merged = purple `#A371F7`, closed = red `#F85149`, draft = grey
5. The contributions heatmap ramp is exact (`#161B22 → #0E4429 → #006D32 → #26A641 → #39D353`) — GitHub's signature visual
6. Monospace for ALL code, SHAs, file/branch names, diffs — alignment & glyph distinction are functional
7. Regions are defined by 1pt `#30363D` borders + luminance steps, NOT drop-shadows
8. The repo home composition is: `owner/name` header → tab strip → branch pill → latest-commit strip → file tree → inline README card
9. The active tab strip item gets a 2pt orange-coral `#F78166` underline (the tab bar itself has no pill)
10. Diffs use green/red line washes + a muted line-number gutter; code never wraps (horizontal scroll)
