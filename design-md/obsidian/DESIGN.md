# Design System Inspiration of Obsidian (iOS)

## 1. Visual Theme & Atmosphere

Obsidian's iOS app is a dark workshop for thought. It treats your notes as a personal knowledge graph stored as plain markdown files, and the interface is built to make that graph feel tangible: a deep charcoal canvas (`#1E1E1E`), monospace-friendly editor lines, file-tree rows, and the signature graph view where every note is a node and every link is an edge you can watch settle into place. The philosophy is "tools for thinking, not a product to consume" — the UI is utilitarian, dense where it needs to be, and gets out of the way of the text. Surfaces lift in small charcoal steps (`#262626`, `#2D2D2D`) and a low-contrast divider (`#363636`) separates panes; there is no glossy chrome, no marketing gradient. It feels like a developer tool that happens to be about ideas.

The accent is Obsidian Purple — `#7C3AED` as the structural action color, lightened to `#A78BFA` for links, active nodes, and selection so it reads on the dark canvas. Purple is the brand and the connective tissue: it marks the active file, internal `[[wikilinks]]`, the selected graph node, tag pills, and the primary action. It is used with restraint so that in a sea of gray text, the purple is always "this connects to something." There is no second brand accent; structure and state are carried by gray, white-ish text, and the purple.

Typography is dual by design: an interface sans (Inter) for chrome, file rows, and the reading view, and a monospace (JetBrains Mono) for the markdown source editor, code blocks, and the command palette — used at 400 / 600 / 700 across a 12–26pt range. This split *is* the identity: the app is simultaneously a calm reading surface and a raw text editor, and the type reflects both. The most expressive typographic moment is the editor itself, where markdown syntax characters (`#`, `**`, `[[`) sit in dimmed purple-gray monospace beside the rendered-feel content — you are always aware you are editing text, not a document.

**Key Characteristics:**
- Deep charcoal canvas (`#1E1E1E`) — a dark workshop, not a polished consumer surface
- Obsidian Purple (`#7C3AED` action / `#A78BFA` links & nodes) as the single connective accent
- Graph view — notes as draggable nodes, links as edges, with gentle physics drift
- Backlinks pane — "Linked mentions" listing every note that references the current one
- Markdown source editor — monospace lines with dimmed syntax characters always visible
- File-tree rows — an indented vault explorer (folders → notes), the primary navigation
- Tag pills — `#tag` chips in tinted purple; tags are first-class graph connections
- Command palette — a monospace-flavored fuzzy action sheet (the keyboard-first control)

## 2. Color Palette & Roles

### Primary
- **Obsidian Purple** (`#7C3AED`): Primary action, the active file marker, command-palette focus, tag-pill base.
- **Purple Link** (`#A78BFA`): Internal `[[wikilinks]]`, the selected/active graph node, link hover, selection text.
- **Purple Pressed** (`#6D28D9`): Active/pressed state for purple buttons and the focused command row.
- **Purple Tint** (`rgba(124,58,237,0.14)`): Selected-row wash, active-node halo, tag-pill background.

### Canvas & Surface (Dark — primary)
- **Canvas** (`#1E1E1E`): Primary charcoal canvas — the editor and graph background.
- **Surface 1** (`#262626`): Side panes (file tree, backlinks), the command-palette body, cards.
- **Surface 2** (`#2D2D2D`): Higher elevation — modals, popovers, the active/hover row, code-block background.
- **Surface 3** (`#363636`): Pressed state on surfaces, input field fill.
- **Divider** (`#363636`): Hairline dividers between panes and file-tree groups.
- **Text Primary** (`#DCDDDE`): Body text, note titles, primary UI text — soft off-white, not pure white.
- **Text Secondary** (`#999999`): Metadata, folder labels, backlink context, timestamps.
- **Text Tertiary** (`#6B6B6B`): Disabled, placeholder, dimmed markdown syntax characters.

### Editor Semantic
- **Heading Text** (`#DCDDDE`): Rendered heading content (size carries hierarchy, not color).
- **Syntax Marker** (`#6B6B6B`): The `#`, `**`, `>`, `[[` `]]` characters — dimmed but present.
- **Wikilink** (`#A78BFA`): `[[Note Name]]` internal links, underlined on press.
- **External Link** (`#7C9CBF`): `https://` external links — a cooler blue to distinguish from wikilinks.
- **Code / Mono** (`#DCDDDE` on `#2D2D2D`): Inline code and fenced code blocks.
- **Highlight** (`rgba(255,213,79,0.25)`): `==highlighted==` text background.
- **Blockquote Bar** (`#7C3AED`): The 3pt left bar on `>` blockquotes.

### Semantic
- **Tag Pill** (`#A78BFA` text on `rgba(124,58,237,0.14)`): `#tag` chips.
- **Unresolved Link** (`#6B6B6B`): A `[[link]]` to a note that doesn't exist yet (dimmed).
- **Success Green** (`#4ADE80`): Sync complete, save confirmation.
- **Error Red** (`#F87171`): Sync conflict, failed operation.
- **Modified Dot** (`#A78BFA`): Unsaved-changes indicator on a file-tree row.

### Light Mode
Obsidian ships a light theme; the default identity is the dark workshop.
- **Light Canvas** (`#FFFFFF`)
- **Light Surface** (`#F5F5F5`)
- **Light Text** (`#1E1E1E`)
- **Light Secondary** (`#6B6B6B`)
- Purple stays the accent; `#7C3AED` reads on both.

## 3. Typography Rules

### Font Family
- **Interface**: `Inter` (chrome, file rows, reading view) — 400 / 600 / 700
- **Monospace**: `JetBrains Mono` (source editor, code blocks, command palette) — 400 / 600
- **Fallback Stack (sans)**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Fallback Stack (mono)**: `'SF Mono', 'JetBrains Mono', Menlo, Consolas, monospace`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Note Title (Large) | Inter | 26pt | 700 | 1.25 | -0.3pt | The note's H1 / title at the top of the editor |
| Editor H1 | Inter | 24pt | 700 | 1.3 | -0.2pt | `# Heading` rendered in the editor |
| Editor H2 | Inter | 20pt | 700 | 1.3 | -0.2pt | `## Heading` |
| Editor H3 | Inter | 17pt | 600 | 1.3 | -0.1pt | `### Heading` |
| Reading Body | Inter | 16pt | 400 | 1.6 | 0pt | Rendered prose in reading view |
| Source Body | JetBrains Mono | 15pt | 400 | 1.55 | 0pt | The raw markdown in the source editor |
| Syntax Marker | JetBrains Mono | 15pt | 400 | 1.55 | 0pt | `#`, `**`, `[[` — color tertiary, same metrics |
| File-Tree Row | Inter | 15pt | 400 | 1.3 | -0.1pt | A note/folder row in the explorer |
| Pane Title (UPPER) | Inter | 11pt | 700 | 1.2 | 0.6pt | "LINKED MENTIONS", "FILES" pane headers |
| Backlink Context | Inter | 13pt | 400 | 1.45 | 0pt | The surrounding-text snippet in a backlink |
| Metadata | Inter | 13pt | 400 | 1.3 | 0pt | "modified 2h ago", word count, node label |
| Tag Pill | Inter | 13pt | 600 | 1.0 | 0pt | `#tag` chip text |
| Command Row | JetBrains Mono | 14pt | 400 | 1.3 | 0pt | A row in the command palette |
| Command Shortcut | JetBrains Mono | 12pt | 600 | 1.0 | 0pt | "⌘P" trailing hint in the palette |
| Button (Primary) | Inter | 15pt | 600 | 1.0 | -0.1pt | "Create note", confirm actions |
| Graph Node Label | Inter | 11pt | 600 | 1.0 | 0pt | The text under a graph node |

### Principles
- **Two type systems on purpose**: Inter for chrome and reading, JetBrains Mono for source and commands — the split is the identity
- **Size carries heading hierarchy, not color**: H1–H3 differ by size/weight; color stays primary text
- **Syntax is dimmed, not hidden**: `#`, `**`, `[[` render in tertiary mono — you always know you're editing text
- **Weights at 400 / 600 / 700 (sans), 400 / 600 (mono)**: no light, no black; monospace stays readable at 15pt
- **Generous editor line-height**: 1.55–1.6 so long-form writing and source both stay comfortable

## 4. Component Stylings

### Buttons

**Primary Button ("Create note", confirm)**
- Background: `#7C3AED`
- Text: `#FFFFFF`, Inter 15pt weight 600
- Padding: 9pt vertical, 16pt horizontal
- Corner radius: 6pt
- Height: 34pt
- Pressed: background `#6D28D9`, scale 0.98, light haptic
- Disabled: background `#2D2D2D`, text `#6B6B6B`

**Ghost / Toolbar Button**
- Background: transparent
- Icon: 18pt, `#999999`; on active/press `#A78BFA`, background `#2D2D2D`, 6pt radius
- Hit target: 36pt; used for the ribbon (left edge) and the note toolbar

**Ribbon Icon (left vertical action strip)**
- 20pt glyph in a 40pt cell, `#999999` default, `#A78BFA` active
- Stacks vertically: open graph, command palette, search, new note, settings

**Tag Pill**
- Background: `rgba(124,58,237,0.14)`
- Text: `#A78BFA`, Inter 13pt weight 600, with a leading `#`
- Padding: 3pt vertical, 9pt horizontal; corner radius 500pt
- Pressed: background `rgba(124,58,237,0.24)` — tapping filters the graph/search to that tag

### Cards & Containers

**File-Tree Row (the navigation unit)**
- Height: 32pt
- Layout: indent (16pt per depth) → disclosure chevron (folders) → 14pt icon → name (15pt w400) → optional modified dot
- Background: transparent (`#1E1E1E`)
- Active file: background `rgba(124,58,237,0.14)`, name `#DCDDDE`, a 2pt `#7C3AED` left bar
- Hover/press: background `#2D2D2D`
- Folder toggle: chevron rotates 90°, children animate height 180ms

**Markdown Editor Surface**
- Background: `#1E1E1E`, full-bleed, comfortable 16pt side padding
- Source mode: JetBrains Mono 15pt, syntax markers (`#`, `**`, `[[`) in `#6B6B6B`
- Wikilinks `[[…]]` in `#A78BFA`; external links in `#7C9CBF`
- Code blocks: `#2D2D2D` background, 6pt radius, mono 15pt, 12pt padding
- Blockquotes: a 3pt `#7C3AED` left bar + indented text in `#999999`
- Highlights: `==text==` on a `rgba(255,213,79,0.25)` background

**Backlinks Pane ("Linked mentions")**
- A `#262626` pane (bottom of the note or a side panel)
- Header: "LINKED MENTIONS" 11pt w700 UPPER `#999999` + a count
- Each backlink: source note title (15pt w600 `#DCDDDE`) + a 13pt `#999999` context snippet with the matched `[[link]]` highlighted in `#A78BFA`
- Tap a backlink to open that note at the reference

**Graph Node + Edge**
- Node: a filled circle, radius scales with link count (4–14pt), color `#7C3AED`; the active note's node is `#A78BFA` with a `rgba(124,58,237,0.14)` halo
- Edge: a 1pt `#363636` line; edges to the active node brighten to `#7C3AED`
- Label: Inter 11pt w600 `#999999`, shown on zoom-in or near the focused node
- Drag a node to reposition; the force simulation re-settles neighbors

**Command Palette**
- Width: 560pt (regular) / full-width minus 24pt (compact)
- Background: `#262626`, 1pt border `#363636`, corner radius 10pt
- Shadow: `rgba(0,0,0,0.6) 0 20px 56px`
- Top: a 44pt monospace input, leading `>` prompt glyph in `#6B6B6B`
- Rows: 38pt — JetBrains Mono 14pt label + trailing shortcut (mono 12pt `#999999`)
- Focused row: background `rgba(124,58,237,0.14)`, label `#DCDDDE`

### Navigation

**Sidebar / Panes (no bottom tab bar)**
- Left ribbon (40pt vertical strip) + a slide-over file-tree pane (280pt) on `#262626`
- The vault explorer (folders → notes) is the primary navigation; tap a note to open it in the editor
- A right pane can host backlinks / outline / graph (on iPad shown side-by-side)
- Opened via the ribbon or edge-swipe; canvas dims `rgba(0,0,0,0.5)` behind on iPhone

**Tab Strip (open notes)**
- A thin row of note tabs above the editor (optional, like a code editor)
- Active tab: `#1E1E1E` with a 2pt `#7C3AED` top accent; inactive `#262626` text `#999999`
- A modified note shows an `#A78BFA` dot instead of the close ✕ until saved

**Note Toolbar**
- Height: 40pt over the editor
- Leading: pane toggle ; Center: note title (15pt w600, tappable to rename) ; Trailing: reading/source toggle, more (18pt `#999999`)
- No blur — opaque `#1E1E1E`

### Input Fields

**Command / Search Input**
- Background: transparent inside the `#262626` palette
- Height: 44pt; leading `>` (commands) or `magnifyingglass` (search) 16pt `#6B6B6B`
- Placeholder: "Type a command…" / "Search vault…", mono/sans 14–15pt `#6B6B6B`
- Text: `#DCDDDE`; caret `#A78BFA`; no border

**Inline Title Field (note rename)**
- Background: `#262626`, 1pt border `#363636`, 6pt radius, 36pt height
- Text: Inter 15pt `#DCDDDE`; focus border `#7C3AED`

**Note Property / Frontmatter Field**
- A `#2D2D2D` keyed row (`key: value`), mono 14pt; values editable inline
- Tag values render as purple tag pills

### Distinctive Components

**Graph View**
- The defining surface — your vault as a physics-driven node-link diagram
- Nodes sized by connection count; the active note glows `#A78BFA`; tags can appear as their own nodes
- Pinch to zoom, drag to pan, drag a node to reposition; the force simulation gently drifts and re-settles
- Filters (by folder, tag, link depth) restyle the graph live; orphan notes float at the edges

**Backlinks / Linked Mentions**
- The second pillar of the knowledge system — every note that links here, with context
- "Linked mentions" (explicit `[[links]]`) and an optional "Unlinked mentions" (plain-text name matches)
- This is how the graph is *read*, where the graph view is how it's *seen*

**Markdown Source Editor with Live Syntax**
- Monospace lines where syntax characters stay visible but dimmed (`#6B6B6B`)
- `[[wikilinks]]` are tappable purple; `#tags` are inline pills; `==highlights==` get a yellow wash
- A reading-view toggle renders the same note as clean Inter prose with no syntax characters

**Command Palette**
- Keyboard-first control surface — `⌘P` (hardware keyboard) or a ribbon icon
- Monospace rows, fuzzy filtering, trailing shortcuts; arrow keys move the purple focus, Enter runs, Esc dismisses
- Mirrors the editor's "this is text" ethos — the palette feels like a terminal

**Tag Pills as Connections**
- `#tags` are first-class graph edges, not metadata decoration
- Rendered as tinted-purple pills in the note, the tag pane, and as optional graph nodes
- Tapping a tag pill filters the graph and opens a tag search

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 6, 8, 12, 16, 20, 24, 32, 40, 56
- Standard margin: 16pt editor side padding; panes inset 12pt
- File-tree rows touch (0pt between); depth communicated by 16pt indentation steps

### Grid & Container
- Editor: single full-bleed column with 16pt side padding (a comfortable reading measure on phone)
- iPhone: editor + slide-over panes (file tree / backlinks / graph)
- iPad: multi-pane workspace — ribbon + file tree + editor + (backlinks/graph) side-by-side
- Command palette / graph filters: centered, max 560pt

### Whitespace Philosophy
- **Dense where it's a tool, roomy where it's reading**: the file tree and command palette are compact; the reading view and editor get generous 1.6 line height
- **The graph needs room to breathe**: graph view is full-bleed with no chrome so nodes have space to settle
- **Panes are utilitarian**: backlinks and the tree pack information efficiently — this is a workshop, not a magazine

### Border Radius Scale
- Sharp (0pt): Dividers, full-bleed editor
- Subtle (6pt): Buttons, inputs, code blocks, tab accents, ghost-button hover
- Standard (10pt): Command palette, modals, popovers
- Pill (500pt): Tag pills, filter chips
- Circle (50%): Graph nodes, avatar, ribbon active indicator

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Editor, file-tree rows, graph canvas |
| Pane (Level 1) | Surface step `#1E1E1E` → `#262626` + 1pt `#363636` divider | Side panes, backlinks |
| Popover (Level 2) | `rgba(0,0,0,0.4) 0 8px 24px` + 1pt border | Context menus, tag suggester |
| Palette (Level 3) | `rgba(0,0,0,0.6) 0 20px 56px` + 1pt border | Command palette, modals |
| Active Node | `rgba(124,58,237,0.14)` halo + brighter `#A78BFA` fill | The focused note in graph view |
| Scrim | `rgba(0,0,0,0.5)` | Behind slide-over panes and modals |

**Shadow Philosophy**: Obsidian is essentially flat — a developer-tool aesthetic. Elevation between panes is a one-step charcoal lift (`#1E1E1E` → `#262626`) plus a 1pt `#363636` divider, not a shadow. Real shadows appear only on truly floating surfaces (command palette, context menus) where a large, very dark, soft shadow separates them from the charcoal canvas. The most expressive "depth" is not a shadow at all — it's the purple halo and brighter fill on the active graph node, which makes the focused idea pop out of the web of gray edges.

### Motion
- **Graph physics**: a continuous gentle force simulation — nodes drift, repel, and re-settle over ~600ms when one is dragged or a filter changes
- **Node focus**: opening a note brightens its node to `#A78BFA` and fades a `rgba(124,58,237,0.14)` halo in over 250ms; neighbors ease toward it
- **Command palette open**: opacity 0→1 + scale 0.97→1.0 over 130ms ease-out
- **File-tree folder toggle**: chevron 0°→90° + children height ease 180ms
- **Pane slide-over**: translateX spring 220ms (response 0.35, damping 0.9) with scrim fade
- **Reading/source toggle**: 200ms cross-fade between the mono source and the Inter rendered view
- **Haptic**: `.impactOccurred(.light)` on note open, command run, and tag-pill tap

## 7. Do's and Don'ts

### Do
- Use the deep charcoal canvas (`#1E1E1E`) — a workshop, not a polished consumer surface
- Reserve Obsidian Purple (`#7C3AED` / `#A78BFA`) for connections: active file, wikilinks, nodes, primary action
- Keep markdown syntax characters visible but dimmed (`#6B6B6B`) in the source editor
- Use two type systems on purpose — Inter for chrome/reading, JetBrains Mono for source/commands
- Make the graph view full-bleed with gentle physics drift and a purple-glowing active node
- Show backlinks as "Linked mentions" with a context snippet and the matched link highlighted
- Render `#tags` as tinted-purple pills that filter the graph when tapped
- Communicate pane elevation with a charcoal step + 1pt divider, not shadows
- Keep the command palette monospace and keyboard-first

### Don't
- Don't use a glossy or gradient surface — Obsidian's restraint is the developer-tool identity
- Don't introduce a second brand accent — purple is the single connective color
- Don't hide markdown syntax in the source editor — the dimmed markers are the point
- Don't use one font for everything — collapsing the sans/mono split erases the identity
- Don't add a bottom tab bar — navigation is the ribbon + file-tree panes
- Don't put heavy shadows on panes or rows — flat + 1pt divider is the look
- Don't make the graph snap or jump — node motion is a gentle continuous drift
- Don't color-code headings — size and weight carry hierarchy; text stays primary color
- Don't use light font weights — Inter 400/600/700, JetBrains Mono 400/600 only

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Editor full-bleed; panes slide over fully; graph zoomed-out by default |
| iPhone 13/14/15 | 390pt | Standard editor measure, slide-over file tree |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the 40pt note toolbar |
| iPhone 15/16 Pro Max | 430pt | Wider editor measure, more backlink context shown |
| iPad | 768pt+ | Multi-pane: ribbon + file tree + editor + backlinks/graph side-by-side |

### Dynamic Type
- Reading body, note titles, file-tree rows: scale with Dynamic Type
- Source editor (mono): scales but preserves the monospace grid; min 13pt
- Graph node labels: fixed 11pt (layout-critical at zoom)
- Command shortcuts: fixed mono 12pt (never scales)

### Orientation
- Editor / reading / file tree: **portrait and landscape**
- Graph view: **portrait and landscape** (more nodes visible in landscape)
- iPad: landscape unlocks the full multi-pane workspace

### Touch Targets
- File-tree row: full 32pt height tappable (≥44pt effective with hit slop)
- Ribbon icons: 20pt glyph, 40pt cell
- Toolbar icons: 18pt glyph, 36pt hit area
- Graph node: the node + a generous radius around it is the drag/tap target
- Command rows: full 38pt height

### Safe Area Handling
- Top: note toolbar respects Dynamic Island / notch
- Bottom: editor and backlinks pane clear the home indicator; the keyboard accessory bar sits above it
- Sides: 16pt editor insets; slide-over panes start at the screen edge

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#1E1E1E`
- Surface 1 (panes): `#262626`
- Surface 2 (modal/code): `#2D2D2D`
- Surface 3 (pressed/input): `#363636`
- Divider: `#363636`
- Text primary: `#DCDDDE`
- Text secondary: `#999999`
- Text tertiary / syntax: `#6B6B6B`
- Obsidian Purple (action): `#7C3AED`
- Purple link / node: `#A78BFA`
- External link: `#7C9CBF`

### Example Component Prompts
- "Create a SwiftUI Obsidian file-tree row: 32pt tall on canvas #1E1E1E, indented 16pt per depth, a disclosure chevron for folders, a 14pt document icon, then the note name 'Daily Note' in Inter 15pt weight 400 #DCDDDE, with a trailing #A78BFA modified dot when unsaved. The active file has a rgba(124,58,237,0.14) background and a 2pt #7C3AED left bar; hover/press goes to #2D2D2D."
- "Build the Obsidian markdown source editor line: JetBrains Mono 15pt, line-height 1.55, on #1E1E1E. Render `## ` markers and `**` in #6B6B6B (dimmed but visible), `[[Note Name]]` wikilinks in #A78BFA, external https links in #7C9CBF, and a `==highlight==` on a rgba(255,213,79,0.25) background. Blockquotes get a 3pt #7C3AED left bar with #999999 text."
- "Design the Obsidian graph view: a full-bleed #1E1E1E canvas of circular nodes (radius 4–14pt scaling with link count) in #7C3AED, connected by 1pt #363636 edges. The active note's node is #A78BFA with a rgba(124,58,237,0.14) halo and brightened edges; labels are Inter 11pt weight 600 #999999. Nodes drift with a gentle continuous force simulation and re-settle over ~600ms when dragged."
- "Create the Obsidian command palette: a 560pt centered sheet, background #262626, 1pt #363636 border, 10pt radius, shadow rgba(0,0,0,0.6) 0 20px 56px. A 44pt monospace input with a leading `>` prompt in #6B6B6B and placeholder 'Type a command…'. Below, 38pt rows in JetBrains Mono 14pt with a trailing shortcut in mono 12pt #999999; the focused row has a rgba(124,58,237,0.14) background. Opens with opacity 0→1 + scale 0.97→1 over 130ms."
- "Build the Obsidian backlinks pane: a #262626 panel with a 'LINKED MENTIONS' header in Inter 11pt weight 700 uppercase #999999 plus a count. Each backlink: source note title in Inter 15pt weight 600 #DCDDDE and a 13pt #999999 context snippet with the matched [[link]] highlighted in #A78BFA."

### Iteration Guide
1. Canvas is deep charcoal `#1E1E1E` — a developer-tool workshop, not a glossy consumer app
2. Obsidian Purple is the single connective accent — `#7C3AED` action, `#A78BFA` links/nodes/selection
3. Two type systems on purpose — Inter for chrome/reading, JetBrains Mono for source/commands; don't collapse them
4. Keep markdown syntax characters dimmed-but-visible (`#6B6B6B`) — the source editor must feel like text
5. The graph view (gentle physics, purple-glowing active node) and backlinks pane are the twin pillars
6. `#tags` are connections rendered as tinted-purple pills, not decorative metadata
7. Elevation is a charcoal step + 1pt `#363636` divider; real shadows only on the command palette / menus
8. Navigation is the ribbon + file-tree panes — no bottom tab bar; the app is flat and utilitarian
