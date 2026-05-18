# Design System Inspiration of Evernote (iOS)

## 1. Visual Theme & Atmosphere

Evernote's iOS app is a **calm, long-form reading and capture surface**. It is the original "second brain" — a place where notes accumulate over years — so the design system optimizes for *legibility, reassuring structure, and a single confident brand color*. The atmosphere is "organized notebook": a clean near-white reading canvas (or a deep near-black in dark mode), generous line-height for body text, hairline list dividers, and exactly one saturated brand color — Evernote Green `#00A82D` — used for the elephant logo, the floating new-note button, checkboxes, links, and primary actions. Nothing competes with the user's content.

The signature surface is the **note editor** paired with the **note list**. The note editor is an almost chrome-free document: a back affordance and overflow menu at top, then the note title in 24pt bold, a thin metadata line ("Updated May 16 · Work notebook"), a row of green tag pills, and the body — paragraphs, headings, checklists (green-filled checkbox when done, strikethrough text), tables, attachments — at a comfortable 16pt with 1.6 line-height. A **format toolbar** docks above the keyboard with Bold/Italic/List/Checklist/Attach/Link controls. Tapping back returns to the **note list**: vertical rows of note cards, each a title (15pt semibold), a 1-2 line content snippet (14pt secondary), a date + notebook line, and an optional 52pt thumbnail. Editor ⇄ list is the loop the app orbits.

The organizational model is **notebooks and stacks**. Notes live in notebooks; notebooks group into stacks. These appear as a dedicated tab — notebook rows with a small colored notebook-spine icon, a name, and a note count; stacks expand to reveal their notebooks. **Tags** are the cross-cutting dimension: lightweight green-tinted pill chips attached to notes and browsable as their own list. The bottom navigation is Home (a customizable dashboard of recent notes, pinned notes, and shortcuts), Notes (the all-notes list), a center **green circular FAB** for new note / capture, Notebooks, and Search (Evernote's powerful full-text + OCR search is a first-class destination).

Typography is a clean humanist sans — Evernote's product UI uses a system-leaning stack; on iOS it renders SF Pro, with **Inter** the closest free analog and a faithful match for the calm, readable hierarchy. Sizing is editorial-leaning: screen titles at 28-32pt bold, note title at 24pt bold, note headings at 17-20pt bold, body at 16pt regular with roomy 1.6 leading, list titles at 15pt semibold, snippets and metadata at 12-14pt. Reading comfort drives every type decision.

**Key Characteristics:**
- Single brand color — Evernote Green (`#00A82D`) for logo, FAB, checkboxes, links, primary actions
- Note editor + note list as the atomic surface — near chrome-free document ⇄ scannable list
- Green circular floating action button (FAB) centered in the bottom bar — new note / capture
- Tags as green-tinted pill chips — lightweight, cross-cutting, browsable
- Notebooks + stacks organizational model — colored notebook-spine icons with note counts
- Light mode is near-white (`#FFFFFF` / `#FAFAFA`); dark mode is true near-black (`#1C1C1E`) for reading comfort
- Warm Evernote ink `#1C2B33` for primary text on light — not pure black
- Roomy 1.6 body line-height — built for long-form reading
- Checklists with green-filled checkboxes + strikethrough completed text
- Format toolbar docked above the keyboard (Bold / Italic / List / Checklist / Attach / Link)
- Home dashboard — pinned notes, recents, shortcuts widgets
- First-class Search — full-text, OCR, handwriting, tag, notebook filters

## 2. Color Palette & Roles

### Primary (Interactive)
- **Evernote Green** (`#00A82D`): Primary brand & action color — logo, FAB, primary CTA, checkbox fill, links (light mode).
- **Evernote Green Bright** (`#2DBE60`): Brighter interactive green for dark surfaces — links, active tab, checkbox fill on dark.
- **Evernote Green Pressed** (`#007A20`): Pressed state of primary buttons & FAB.
- **Evernote Green Deep** (`#108A00`): Legacy/secondary green accent (older brand green, used on some marketing surfaces).

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas — note editor.
- **Surface Off-White** (`#FAFAFA`): List background, app chrome behind note rows.
- **Surface Raised** (`#FFFFFF`): Note list rows, cards (white with 1px divider).
- **Surface Pressed** (`#F1F2F2`): Pressed/selected rows, hovered notebook rows.
- **Divider** (`#E4E5E5`): Hairline dividers between note rows and sections.
- **Border** (`#DCDEDE`): Input borders, card edges.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#1C1C1E`): Primary dark canvas — true near-black reading surface.
- **Dark Surface 1** (`#242426`): Note rows, raised surfaces.
- **Dark Surface 2** (`#2E2E30`): Toolbars, input fields, notebook rows.
- **Dark Surface 3** (`#3A3A3C`): Pressed states, thumbnail placeholder.
- **Dark Divider** (`#38383A`): Hairline dividers.

### Text
- **Text Primary Light** (`#1C2B33`): Primary body & title text — Evernote warm ink, NOT pure black.
- **Text Secondary Light** (`#5C6970`): Snippets, secondary metadata.
- **Text Tertiary Light** (`#8C969C`): Dates, placeholder, low-emphasis.
- **Text Disabled Light** (`#B8C0C4`): Disabled labels.
- **Text Primary Dark** (`#E4E4E6`): Primary body on dark (not pure white — reading comfort).
- **Text Secondary Dark** (`#9A9A9E`): Snippets/secondary on dark.
- **Text Tertiary Dark** (`#6E6E72`): Dates/placeholder on dark.

### Tag Chips
- **Tag Light** (`#E6F4EA` bg / `#1F7A33` text): Green-tinted pill on light.
- **Tag Dark** (`#1C3A24` bg / `#5FD68A` text): Green-tinted pill on dark.

### Notebook Spine Accents
Notebook icons rotate through a calm accent set: Green `#00A82D`, Blue `#2F80ED`, Yellow `#F6C544`, Red `#E5484D`, Purple `#7A5AF8`, Teal `#1AAE9F`, Gray `#8C969C`.

### Semantic
- **Info / Link Blue** (`#2F80ED`): Inline hyperlinks within note body, info banners.
- **Success Green** (`#00A82D`): Save-success toast, sync-complete check.
- **Warning Amber** (`#F6A609`): Sync-conflict / quota warnings.
- **Error Red** (`#E5484D`): Delete confirmation, sync error, destructive actions.
- **Highlight Yellow** (`#FFF3B0` light / `#5A4A12` dark): Text highlight in note body.

## 3. Typography Rules

### Font Family
- **Primary**: System humanist sans — `SF Pro Text` / `SF Pro Display` on iOS (Evernote uses the platform system stack for body/UI legibility).
- **Closest Free Analog**: `Inter` (by Rasmus Andersson, SIL OFL) — calm humanist sans, excellent at long-form reading sizes; faithful to Evernote's UI rhythm.
- **Mono**: `SF Mono` / `Roboto Mono` — used in code blocks within notes.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Inter', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | SF Pro / Inter | 28-32pt | 700-800 | 1.2 | -0.4pt | "Notes", "Notebooks" — large nav title |
| Note Title | SF Pro / Inter | 24pt | 700 | 1.25 | -0.3pt | Title field in the note editor |
| Note Heading 1 | SF Pro / Inter | 20pt | 700 | 1.3 | -0.2pt | In-note H1 |
| Note Heading 2 | SF Pro / Inter | 17pt | 700 | 1.35 | -0.1pt | In-note H2 |
| Body | SF Pro / Inter | 16pt | 400 | 1.6 | 0pt | Note paragraph — roomy reading leading |
| List Title | SF Pro / Inter | 15pt | 600 | 1.3 | 0pt | Note row title in the list |
| Checklist Item | SF Pro / Inter | 15pt | 400 | 1.45 | 0pt | Strikethrough + tertiary when done |
| Snippet / Secondary | SF Pro / Inter | 14pt | 400 | 1.4 | 0pt | Note row content preview |
| Tag / Caption | SF Pro / Inter | 12pt | 600 | 1.2 | 0.1pt | Tag pill text, captions |
| Meta | SF Pro / Inter | 12pt | 400 | 1.3 | 0pt | "Updated · Notebook", dates |
| Button | SF Pro / Inter | 15pt | 600 | 1.0 | 0pt | Primary/secondary buttons |
| Tab Label | SF Pro / Inter | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Code | SF Mono | 14pt | 400 | 1.5 | 0pt | Code block in note body |

### Principles
- **Reading comfort first**: body text is 16pt at 1.6 line-height — never tighten note body leading below 1.5.
- **Editorial hierarchy**: note title → H1 → H2 → body mirrors document structure, not product-chrome structure.
- **Weight ladder 400 / 500 / 600 / 700**: regular body, medium tabs, semibold list titles & buttons, bold titles & headings. No thin, no black.
- **Completed checklist items recede**: strikethrough + tertiary color, never deleted from view.
- **Dynamic Type on content, fixed on chrome**: note title/headings/body/snippets scale; tab labels, tag pills stay fixed.
- **Snippet clamp**: note-list content preview clamps to 2 lines with ellipsis.

## 4. Component Stylings

### Buttons

**Primary Button (New Note / Save / primary CTA)**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#00A82D` light & dark (brand green holds; on dark the surrounding contrast suffices)
- Text: `#FFFFFF`, 15pt weight 600
- Padding: 13pt vertical, 26pt horizontal
- Pressed: background `#007A20` + 0.98 scale
- Disabled: background `#B8C0C4` / `#3A3A3C`, text 60% opacity

**Floating Action Button (FAB — new note / capture)**
- Shape: Circle, 52pt diameter, centered in bottom bar (overlaps tab strip by ~8pt)
- Background: `#00A82D`; icon: white `plus` 24pt weight 600
- Shadow: `0 6px 16px rgba(0,168,45,0.40)`
- Tap: opens capture sheet (Note / Camera / Audio / Reminder); long-press: quick-action radial
- Pressed: `#007A20` + 0.96 scale

**Outline / Secondary Button (Upgrade / promo)**
- Background: transparent
- Border: 1.5pt `#00A82D`
- Text: `#1F7A33` light / `#2DBE60` dark, weight 600, 14pt, 8pt radius
- Pressed: faint `#E6F4EA` / `#1C3A24` fill

**Subtle Button (Move to notebook / secondary action)**
- Background: `#F1F2F2` light / `#2E2E30` dark
- Text: `#1C2B33` / `#E4E4E6`, weight 500, 14pt, 8pt radius
- Pressed: one surface step darker

**Text / Link Button (Share note)**
- No background/border
- Text: `#1F7A33` light / `#2DBE60` dark, weight 600, 14pt
- Pressed: underline

### Core Atoms

**Tag Pill**
- Shape: full pill (500pt radius)
- Padding: 3pt vertical, 9pt horizontal
- Text: 12pt weight 600
- Color: `#E6F4EA`/`#1F7A33` light, `#1C3A24`/`#5FD68A` dark
- Tappable → filters note list by tag; "×" on tag in edit mode

**Checkbox (checklist)**
- Unchecked: 19pt rounded square (5pt radius), 2pt `#8C969C` border, transparent fill
- Checked: filled `#00A82D` (`#2DBE60` dark), white check glyph, item text strikethrough + tertiary color
- 44pt hit area; tap toggles with spring + light haptic

**Notebook Spine Icon**
- 22pt glyph: a rounded rectangle "book" with a darker spine stripe in the notebook's accent color
- Stack icon: layered/folder glyph in accent color

**Note Thumbnail**
- 52pt rounded square (8pt radius); shows first image/attachment or a typed-content placeholder (`#F1F2F2` / `#3A3A3C`)

**Avatar (shared notes / people)**
- Circle, 28pt, initials or photo, calm accent background

### Note Card / List Row

- Surface: `#FFFFFF` light / `#242426` dark on a `#FAFAFA`/`#1C1C1E` background
- Divider: 1px `#E4E5E5` / `#38383A` between rows (rows, not boxed cards)
- Padding: 14pt vertical, 4-16pt horizontal
- Structure: left text column — title (15pt/600, 1-line), snippet (14pt secondary, 2-line clamp), date + notebook (12pt tertiary); right — optional 52pt thumbnail
- Pinned variant: leading green pin glyph
- Swipe actions: leading = pin / move; trailing = delete (red) / tag
- Selected (multi-select): leading green check, row tint `#E6F4EA`/`#1C3A24`

### Navigation

**Bottom Tab Bar (with center FAB)**
- Height: 56pt + safe area
- Background: `#FFFFFF` / `#1C1C1E` with 0.5pt top border `#E4E5E5` / `#38383A`
- 5 slots: Home, Notes, [FAB], Notebooks, Search
- Tab icon: 22-24pt; active fill, inactive stroke
- Active color: `#00A82D` light / `#2DBE60` dark; inactive `#8C969C` / `#6E6E72`
- Labels: 10pt weight 500, always shown
- Center FAB: 52pt green circle, raised above the strip

**Note Editor Top Bar**
- Height: 44pt + safe area
- Leading: back chevron + "Notes" label (green)
- Trailing: info/details icon + overflow ⋯ (share, move, tags, delete, reminder)
- Title and body scroll under the bar; bar stays minimal

**Note List Top Bar**
- Large title ("Notes"), trailing sort/filter + view toggle (list/snippet/grid)
- Search field appears on pull-down

**Format Toolbar (above keyboard)**
- Docked accessory bar, `#FFFFFF`/`#2E2E30`, 0.5pt top divider, ~48pt tall
- Controls: Bold, Italic, Lists, Checklist, Heading, Attach, Photo, Table, Link, Highlight
- Active control tinted green `#2DBE60`

### Input Fields

**Search Field**
- Height: 40pt
- Background: `#F1F2F2` light / `#2E2E30` dark (filled, not bordered)
- Corner radius: 10pt
- Leading icon `magnifyingglass` 16pt tertiary
- Placeholder: 15pt `#8C969C` / `#6E6E72` ("Search notes")
- Focus: 1.5pt `#00A82D`/`#2DBE60` border ring

**Note Title Field**
- Borderless, 24pt weight 700, placeholder "Title" in tertiary

**Inline Text Body**
- Borderless rich-text region, 16pt/1.6, placeholder "Start writing…" in tertiary
- Selection highlight: `#E6F4EA` / `#1C3A24`

**Sheet Field (move/notebook picker rows)**
- 48pt rows, leading accent icon, title 16pt, trailing chevron or selected check (green)

### Distinctive Components

**Note Editor**
- Chrome-free document: title → meta line → tag pills → rich body (paragraphs, H1/H2, checklists, tables, dividers, attachments, code, highlight)
- Format toolbar docks above keyboard while editing
- Tap meta line to change notebook; tap tag area to add tags

**Home Dashboard**
- Customizable widget stack: "Recently viewed" horizontal note cards, "Pinned Notes", "Notebooks" shortcuts, "Scratch pad", "Tasks"
- Each widget a 14pt-radius card on the canvas with a header + edit affordance

**Notebooks & Stacks Screen**
- List of notebooks (spine icon + name + count) and stacks (expandable group header with chevron + aggregate count)
- "New notebook" / "New stack" primary actions

**Tag Browser**
- Alphabetical / frequency list of tag pills with note counts; tap to see filtered notes

**Search Surface**
- Prominent search field; recent searches; scopes — All, Title, Tag, Notebook, Attachments
- Results: note rows with matched-term highlight; OCR/handwriting matches indicated

**Capture Sheet (from FAB)**
- Bottom sheet: Text Note, Camera (document scan), Photo, Audio, Reminder, Templates
- 20pt-radius sheet, large tappable rows with leading green icons

**Reminder / Task Row**
- Note with a due date: green clock glyph + relative date; overdue turns red `#E5484D`

**Sync Status Pill**
- Transient toast: spinner → green check "All notes synced" / amber "Conflict" / red "Sync error"

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48
- Note row vertical padding: 14pt
- Body paragraph spacing: 12-16pt; heading top margin: 20pt
- Screen horizontal inset: 16pt; note editor body inset: 22pt (extra reading margin)
- Section gap: 24pt

### Grid & Container
- iPhone: 16pt list insets; 22pt editor body insets for reading line-length comfort
- iPad: 3-pane — Notebooks/sidebar + note list + note editor at ≥768pt
- Note editor max readable width: ~680pt centered on large screens
- Home dashboard: single-column widget stack on phone, 2-column on iPad

### Whitespace Philosophy
- **Content is sacred**: chrome recedes so the note reads like clean paper (or a clean dark page)
- **Roomy reading rhythm**: 1.6 body leading + 22pt editor margins — long notes never feel cramped
- **Hairlines over boxes**: note rows separated by 1px dividers, not nested cards
- **One color rationed**: neutral canvas + a single green means green always reads as "Evernote / action / done"

### Border Radius Scale
- Square (0pt): full-bleed dividers, note body region
- Subtle (5pt): checkboxes, small inline chips
- Standard (8pt): buttons, thumbnails, list-item containers
- Comfortable (10pt): search field, input fields
- Card (14pt): home dashboard widgets, modals
- Sheet (20pt): bottom sheets (capture, move, share)
- Pill (500pt): tag chips, segmented toggles
- Circle (50%): FAB, avatars

## 6. Depth & Elevation

Evernote is mostly flat — elevation marks the FAB and transient/floating surfaces only.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (0) | No shadow; 1px divider only | Note rows, list, note body, notebook rows |
| Raised (1) | `0 1px 3px rgba(28,43,51,0.10)` | Home dashboard widget cards, hovered rows |
| FAB (2) | `0 6px 16px rgba(0,168,45,0.40)` | Floating action button (green-tinted) |
| Menu (3) | `0 8px 20px rgba(28,43,51,0.16)` | Overflow menus, dropdowns, popovers |
| Sheet/Modal (4) | `0 -8px 28px rgba(28,43,51,0.18)` + `rgba(28,43,51,0.45)` scrim | Capture sheet, move/share sheets |

**Shadow Philosophy**: Shadows are tinted with Evernote ink (`rgba(28,43,51,…)`) on light to stay calm and cool. The FAB is the one exception that gets a green-tinted glow so it reads as the primary affordance. On dark mode shadows nearly vanish, so elevation is signaled by the surface step (`#242426` → `#2E2E30` → `#3A3A3C`) plus a 1px border on menus and sheets.

### Motion
- **Note open (list → editor)**: iOS native push, 300ms horizontal slide; title cross-fades from row to editor
- **Checkbox toggle**: box fills with a 150ms spring scale-up; text strikethrough draws over 120ms; light haptic
- **FAB tap → capture sheet**: sheet slides up 300ms ease-out; FAB scales 0.96 then back; backdrop scrim fades in
- **FAB long-press**: quick-action radial expands over 180ms with soft haptic
- **Swipe actions**: 1:1 finger tracking; action buttons reveal; full-swipe triggers with medium haptic
- **Pull-to-search**: list rubber-bands; search field slides down 200ms
- **Sync toast**: fades + 8pt slide-up in; auto-dismiss after 2s with fade-out
- **Section/stack expand**: chevron rotates 90° 150ms; rows expand height 0↔auto 200ms ease-out
- **Tag add**: pill scales in from 0.8 over 150ms ease-out
- **Haptics**: light on checkbox toggle & tab change; medium on swipe-action commit & note delete; soft on FAB long-press
- **Reduce Motion**: replace slides/scale with crossfades; keep instant state changes; checkbox toggles without spring

## 7. Do's and Don'ts

### Do
- Use Evernote Green (`#00A82D`) for logo, FAB, checkboxes, links, and primary actions only
- Brighten interactive green to `#2DBE60` on dark surfaces for AA contrast
- Keep the canvas neutral (near-white light / true near-black dark) so green always means "Evernote/action/done"
- Render the new-note affordance as a centered green circular FAB raised above the tab bar
- Use roomy 1.6 line-height for note body and 22pt editor side margins
- Show tags as green-tinted pill chips
- Use the notebook-spine icon with accent color + note count for notebook rows
- Strike through + dim completed checklist items (never delete from view)
- Use Evernote warm ink `#1C2B33` for primary text on light — not pure black
- Use true near-black `#1C1C1E` for dark mode reading comfort — body text `#E4E4E6`, not pure white
- Separate note rows with 1px hairline dividers, not nested cards
- Dock the format toolbar above the keyboard while editing

### Don't
- Don't use green as a large background fill — it must read as "action/brand", not decoration
- Don't introduce competing accent colors — one green + neutral grays + minimal semantic set
- Don't tighten note-body line-height below 1.5 — reading comfort is the brand
- Don't replace the FAB with a plain toolbar "+" — the raised green circle is signature
- Don't use pure black text (`#000000`) on light — `#1C2B33` warm ink is correct
- Don't use pure white body text on dark — `#E4E4E6` for long-form comfort
- Don't pure-white the dark canvas to gray-charcoal — Evernote dark is a true near-black `#1C1C1E`
- Don't box every note row in its own card — hairline dividers only
- Don't hide completed checklist items — strike + dim, keep visible
- Don't animate aggressively — motion is calm 150-300ms ease-out / soft spring
- Don't drop pure-black shadows on light — tint with `rgba(28,43,51,…)` ink (FAB excepted: green glow)
- Don't bury Search — it is a first-class bottom-tab destination

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Single-column list; editor margins reduce to 18pt |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top bar |
| iPhone 15/16 Pro Max | 430pt | Wider editor margins (24pt); larger thumbnails |
| iPad (portrait) | 768pt | 2-pane: note list + editor; notebooks as slide-over |
| iPad (landscape) | 1024pt+ | 3-pane: notebooks sidebar + note list + editor; editor capped ~680pt |

### Dynamic Type
- Scales: screen titles, note title, headings, body, snippets, list titles, checklist text
- Fixed: tab labels, tag pills, sync-toast text (chrome/layout-sensitive)
- Code blocks scale to XL but stay monospace

### Orientation
- iPhone: portrait-primary; landscape widens reading column
- iPad: split-view controller — sidebar + list + editor; all panes rotate; editor keeps max readable width

### Touch Targets
- Note row: full-row tap (≥64pt), swipe for actions
- Checkbox: 19pt glyph, 44pt hit
- FAB: 52pt circle, full hit
- Tab icons: 22-24pt glyph, 44pt hit
- Format toolbar controls: 24pt glyph, 44pt hit

### Safe Area Handling
- Top: editor/list bars respect safe area and Dynamic Island
- Bottom: tab bar + FAB + home indicator respected; FAB sits above safe-area inset
- Keyboard: format toolbar docks directly above keyboard; body scrolls above it
- Sides: 16pt list inset, 22pt editor body inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Primary / Evernote Green: `#00A82D` · interactive on dark `#2DBE60` · pressed `#007A20`
- Link blue: `#2F80ED`
- Canvas: `#FFFFFF` light · `#1C1C1E` dark (true near-black)
- Surface (off-white list bg): `#FAFAFA` light · note surface `#242426` dark
- Surface raised (row): `#FFFFFF` light · `#242426` dark
- Divider: `#E4E5E5` light · `#38383A` dark
- Text primary: `#1C2B33` (Evernote ink) light · `#E4E4E6` dark
- Text secondary: `#5C6970` light · `#9A9A9E` dark
- Text tertiary: `#8C969C` light · `#6E6E72` dark
- Tag pill: `#E6F4EA`/`#1F7A33` light · `#1C3A24`/`#5FD68A` dark
- FAB shadow: `0 6px 16px rgba(0,168,45,0.40)`
- Highlight: `#FFF3B0` light · `#5A4A12` dark

### Example Component Prompts
- "Build the Evernote note editor in SwiftUI: chrome-free document. Top bar 44pt — leading back chevron + 'Notes' in Evernote Green `#2DBE60`, trailing info + overflow ⋯. Body inset 22pt: note title TextField in 24pt weight 700 `#1C2B33` (`#E4E4E6` dark), then a 12pt tertiary meta line ('Updated May 16 · Work'), then a wrapping row of green tag pills (`#E6F4EA`/`#1F7A33`, full pill, 12pt/600). Rich body at 16pt weight 400 line-height 1.6. Dock a format toolbar above the keyboard (`#FFFFFF`/`#2E2E30`, 0.5pt top divider) with Bold/Italic/List/Checklist/Heading/Attach/Link, active control tinted `#2DBE60`."

- "Create the Evernote checklist item: 19pt rounded-square checkbox (5pt radius). Unchecked = 2pt `#8C969C` border, transparent. Checked = filled `#00A82D` (`#2DBE60` dark) with a white check, and the item text (15pt/400) gets strikethrough + tertiary color. 44pt hit area; on tap, the box fills with a 150ms spring scale-up, the strikethrough draws over 120ms, and a light haptic fires. Completed items stay visible — never removed."

- "Render the Evernote note list row: white `#FFFFFF` (`#242426` dark) on a `#FAFAFA`/`#1C1C1E` background, 1px `#E4E5E5`/`#38383A` bottom divider, 14pt vertical padding. Left column: title 15pt weight 600 `#1C2B33` (1 line), content snippet 14pt `#5C6970` (2-line clamp), date+notebook 12pt `#8C969C`. Right: optional 52pt rounded-square (8pt) thumbnail. Leading swipe = pin/move, trailing swipe = delete (red `#E5484D`)/tag, full-swipe with medium haptic."

- "Build the Evernote bottom tab bar with center FAB: 56pt + safe area, `#FFFFFF`/`#1C1C1E`, 0.5pt top divider. Five slots — Home, Notes, [FAB], Notebooks, Search. Tab icons 22-24pt (active filled, inactive stroked), labels 10pt weight 500. Active tint `#00A82D` (light) / `#2DBE60` (dark), inactive `#8C969C`/`#6E6E72`. Center FAB: 52pt green `#00A82D` circle raised ~8pt above the strip, white `plus` 24pt, shadow `0 6px 16px rgba(0,168,45,0.40)`; tap opens a 20pt-radius capture sheet (Text / Camera / Photo / Audio / Reminder)."

- "Create the Evernote tag pill: full pill (500pt radius), 3pt vertical / 9pt horizontal padding, text 12pt weight 600. Light = `#E6F4EA` bg / `#1F7A33` text; dark = `#1C3A24` bg / `#5FD68A` text. Tappable to filter the note list by that tag; in edit mode shows a trailing '×' to remove. New tag animates in scaling from 0.8 over 150ms ease-out."

- "Render the Evernote notebooks screen: list of notebook rows — leading 22pt notebook-spine icon (rounded book + darker spine stripe in the notebook's accent color from {green `#00A82D`, blue `#2F80ED`, yellow `#F6C544`, red `#E5484D`}), name 16pt weight 600, trailing note count 12pt tertiary. Stacks render as expandable group headers with a leading chevron (rotates 90° on expand, 150ms) and an aggregate count; tapping expands the contained notebooks with a 200ms height animation."

### Iteration Guide
1. Canvas is neutral — near-white `#FFFFFF`/`#FAFAFA` light, true near-black `#1C1C1E` dark (reading comfort, NOT charcoal-gray)
2. Text primary is Evernote warm ink `#1C2B33` light / `#E4E4E6` dark — never pure black/white
3. Evernote Green `#00A82D` is brand+action only; brighten to `#2DBE60` for interactive elements on dark
4. The new-note affordance is a centered green circular FAB raised above the tab bar — not a toolbar "+"
5. Body line-height is 1.6 with 22pt editor side margins — reading comfort is the brand
6. Tags are green-tinted pill chips; checkboxes fill green with strikethrough+dim on complete (kept visible)
7. Note rows use 1px hairline dividers — never nested cards
8. Notebooks use a colored spine icon + note count; stacks are expandable groups
9. Format toolbar docks above the keyboard while editing, active control tinted green
10. Motion is calm Evernote ease-out 150-300ms / soft spring; shadows ink-tinted on light (FAB = green glow), surface-step on dark
