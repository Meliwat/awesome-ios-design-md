# Design System Inspiration of Apple Notes (iOS)

## 1. Visual Theme & Atmosphere

Apple Notes is Apple's most quietly opinionated app — a 16-year exercise in restraint. Where every other Apple app debates whether to be material or flat, glass or solid, Notes commits to one idea: *it's paper*. The canvas is a warm cream yellow (`#FFFBED` in light, `#1A1A1A` in dark), the chrome is suppressed to nearly nothing, the only "color" in the entire app is the yellow folder glyph and the brand orange highlights. The aesthetic is *legal pad* — and the legal-pad reference is intentional. Notes is Apple's tribute to the yellow Steno pad and the manila folder, rendered as software.

The canvas is **Notes Cream** (`#FFFBED`) — a warmer-than-white off-cream that gives the screen the texture of light, slightly-aged paper. On dark mode, the canvas inverts to **Dark Paper** (`#1A1A1A`), a true dark gray that preserves the paper feel. The accent is **Notes Orange** (`#F09A38`) — Apple's brand orange that appears on the folder glyph, on note-creation FABs, on selection states, and on the "+" New Note button. The yellow of the folder itself — `#F5D773` — is iconic: every folder in the app is rendered as a slightly-3D yellow tab-folder, exactly the same shade as the macOS Finder folder. Everything else is the system gray ramp.

Typography is **SF Pro** at every size, including in the note body — Apple does not use a custom face here, because they want notes to feel like the iOS system. The note body uses SF Pro Text at 17pt 400 with a generous 1.5 line height, which is the most calm body text in any iOS app. Title casing is editorial — large nav titles at 34pt Heavy, note titles at 20pt Semibold, body at 17pt Regular. There is no use of SF Pro Display in the note body itself — only Text — because Notes is for reading, not glanceable headlines.

**Key Characteristics:**
- Notes Cream canvas (`#FFFBED`) in light mode, Dark Paper (`#1A1A1A`) in dark — both deliberately warmer than typical iOS apps
- Notes Orange (`#F09A38`) as the primary action color — New Note FAB, folder glyph stroke, selection states
- The Folder Yellow (`#F5D773`) — used only on the actual folder glyphs, which appear as small 3D tab-folder icons in lists
- Note list as the home screen — rows show note title (bold), preview text (1-2 lines), and date in a tight three-line stack
- Pinned section at the top of the list with a small pin glyph in Notes Orange
- Tags row above pinned — orange-tinted hash chips for #recipes, #books, #ideas
- Search bar pinned at the top — pull-to-reveal in older iOS, persistent in iOS 17+
- Bottom toolbar in the editor: 4 action buttons (formatting, checklist, table, photo) + a New Note quick action
- SF Pro at every size with Dynamic Type supported everywhere
- Subtle elevation only — Notes is paper-flat, shadows are nearly imperceptible (max 4% opacity)

## 2. Color Palette & Roles

### Brand
- **Notes Orange** (`#F09A38`): The primary brand action color — folder glyph stroke, New Note FAB, selection accent, link color, locked-note password field accent.
- **Notes Orange Pressed** (`#D87E1F`): Pressed state on Notes Orange CTAs.
- **Notes Orange Tint** (`#FFF1DD`): Background tint for selected rows, focused folder highlight.
- **Folder Yellow** (`#F5D773`): The iconic yellow used on the actual folder glyph fills throughout the app (and Finder on macOS).

### Canvas, Surfaces & Dividers (Light)
- **Notes Cream** (`#FFFBED`): Primary canvas — list view, note editor, sheets. Warmer than white by ~3%.
- **Cream Surface 1** (`#FAF6E3`): Search field fill, section background on focus, slightly warmer than the canvas.
- **Cream Surface 2** (`#F2EDD6`): Pressed-state row background, chip fills.
- **Divider Light** (`#EDEAD8`): Hairline dividers between rows, between sections; warm-toned to match the cream canvas.

### Canvas, Surfaces & Dividers (Dark)
- **Dark Paper** (`#1A1A1A`): Primary dark canvas — list view, editor, sheets.
- **Dark Surface 1** (`#262626`): Search field, section background.
- **Dark Surface 2** (`#333333`): Pressed states, chip fills.
- **Dark Divider** (`#2A2A2A`): Hairlines.

### Text (Light)
- **Ink** (`#1C1C1E`): Primary text — note titles, body copy, folder names.
- **Slate** (`#8E8E93`): Secondary text — note preview, date metadata, byline.
- **Mute** (`#C7C7CC`): Tertiary — placeholder, disabled.

### Text (Dark)
- **Soft White** (`#F2F2F2`): Primary text on dark.
- **Slate Dark** (`#8E8E93`): Secondary, identical to light mode (Apple's gray ramp doesn't shift much).
- **Mute Dark** (`#48484A`): Tertiary.

### Semantic
- **Success Green** (`#34C759`): Save confirmation, checklist completed glyph fill.
- **Warning Amber** (`#FFCC00`): Warning toasts (rare in Notes).
- **Error Red** (`#FF3B30`): Delete confirmation, recently-deleted folder accent.
- **Info Blue** (`#0A84FF`): Rare — external sharing toasts.
- **Lock Yellow** (`#FFCC00`): Locked-note state.

### Tag / Highlight Palette (used inside note body)
Notes supports text highlighting in five system colors that match Apple's brand:
- **Yellow** (`#FFEB78`): Default highlight color
- **Green** (`#B3E47B`)
- **Blue** (`#A0D8FF`)
- **Pink** (`#FFB3DB`)
- **Purple** (`#D4B3FF`)

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro` — Display variant kicks in at 20pt+, Text at body sizes
- **Mono**: `SF Mono` — used in monospaced font formatting option inside notes
- **Body face on note canvas**: SF Pro Text at 17pt 400 with 1.5 line-height — the calmest body in any iOS app
- **Fallback stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Helvetica Neue', sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Nav Title | SF Pro Display | 34pt | 800 (Heavy) | 1.1 | -0.4pt | "Notes", "Folders" — Apple's biggest nav title |
| Folder Section Title | SF Pro Text | 22pt | 700 | 1.2 | -0.3pt | "On My iPhone", "iCloud" |
| Note Title (in list) | SF Pro Text | 16pt | 600 (Semibold) | 1.3 | -0.1pt | The first line of the note in bold |
| Note Preview | SF Pro Text | 14pt | 400 | 1.35 | 0pt | Lines 2-3 of the note in slate |
| Note Date | SF Pro Text | 12pt | 400 | 1.3 | 0pt | "Yesterday", "May 12, 2026" — right-aligned |
| Folder Row Name | SF Pro Text | 17pt | 400 | 1.3 | 0pt | "Notes", "Recipes", "Travel" — sidebar |
| Note Body Title | SF Pro Text | 20pt | 600 | 1.3 | -0.2pt | First line of a note inside the editor |
| Note Body Heading | SF Pro Text | 17pt | 600 | 1.4 | 0pt | Headings inside the note |
| Note Body | SF Pro Text | 17pt | 400 | 1.5 | 0pt | The note paragraph itself |
| Note Body Mono | SF Mono | 15pt | 400 | 1.5 | 0pt | Code-formatted text inside notes |
| Tag Chip | SF Pro Text | 14pt | 500 | 1.0 | 0pt | "#recipes" |
| Toolbar Label | SF Pro Text | 11pt | 500 | 1.0 | 0.1pt | Below toolbar glyph icons |
| Button (Primary) | SF Pro Text | 17pt | 600 | 1.0 | 0pt | "Save", "Done" |
| Search Placeholder | SF Pro Text | 17pt | 400 | 1.0 | 0pt | "Search" |

### Principles
- **Body type is the centerpiece** — SF Pro Text 17pt 400 with 1.5 line-height is the most generous body in any iOS app
- **Heavy on the nav title (Weight 800)** — Apple uses the rare Heavy weight on the large "Notes" title, which gives Notes its distinct identity
- **Editorial three-row note list** — title (Semibold) + preview (Regular slate) + date (Regular slate) is the canonical row layout
- **No custom typography** — SF Pro everywhere, even the "Notes" wordmark on the App Store uses SF Pro Display Heavy
- **Slate is the calm of the app** — `#8E8E93` for previews, dates, metadata; the cream canvas + slate gray gives the entire app its calm tone
- **Dynamic Type respected everywhere** — body, titles, list rows, folder names; this is essential since Notes is heavily used by older iPhone users
- **Headings inside notes use Apple's "Title", "Heading", "Subheading" presets** — the body picker UI exposes these as a vertical stack of 5 type ramps

## 4. Component Stylings

### Buttons

**Primary CTA ("Save", "Done", "Continue")**
- Background: `#F09A38` (Notes Orange)
- Text: `#FFFFFF`, SF Pro 17pt 600
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: 12pt
- Height: 44pt minimum
- Pressed: background `#D87E1F`, scale 0.98
- Haptic: `.impactOccurred(.light)` on tap

**New Note FAB (bottom-right of note list)**
- 56pt circle, bottom-right inset 16pt
- Background: `#F09A38` Notes Orange
- Icon: custom "square.and.pencil" SF Symbol, 24pt, white
- Shadow: `rgba(240,154,56,0.30) 0 4px 12px` — orange-tinted shadow
- Haptic: `.impactOccurred(.medium)` on tap — opens new note with cursor placed at top

**Secondary Text Button ("Edit", "Cancel")**
- Text: `#F09A38` Notes Orange, SF Pro 17pt 400
- No background, 44pt tap slop
- Used in nav bars and inline lists

**Inline Action Pill (toolbar)**
- 36pt circle, `#FAF6E3` background (cream surface 1)
- Glyph: 18pt SF Symbol, `#1C1C1E`
- Pressed: background `#F2EDD6`

### Cards & Containers

**Note Row (the centerpiece of the list view)**
- Height: 84pt
- Background: `#FFFBED` Notes Cream (light) / `#1A1A1A` Dark Paper (dark)
- Layout (top to bottom):
  - 16pt top inset → title (Semibold 16pt Ink, 1-line truncate)
  - 4pt gap → preview line 1 (14pt 400 Slate, 1-line truncate)
  - 2pt gap → date (12pt 400 Slate, e.g., "Yesterday", "May 12")
- 16pt leading/trailing inset
- 0.5pt `#EDEAD8` divider at the bottom of each row
- Tap haptic: `.selectionChanged()`, scale 0.99 briefly
- Swipe-left: reveals 3 actions — Pin (orange), Add to Folder (gray), Delete (red)

**Pinned Section**
- Header: "Pinned" 13pt 600 uppercase Slate with 1.6pt letter-spacing
- Pinned notes get a small `pin.fill` 11pt Notes Orange glyph inline before the title
- A subtle warm-cream background tint `#FAF6E3` distinguishes pinned rows from regular rows

**Folder Row (folder list view)**
- Height: 44pt
- Background: `#FFFBED`
- Leading: 24pt yellow folder glyph in `#F5D773` with a 1pt `#F09A38` stroke (the iconic folder)
- Folder name: 17pt 400 Ink
- Trailing: note count "12" in 17pt 400 Slate + 14pt chevron in Slate
- Press: background fades to `#FAF6E3`

**Yellow Folder Glyph (the iconic mark)**
- A small 3D tab-folder shape, 24pt at folder-row size, 80pt at the "All iCloud" hero header
- Fill: `#F5D773`
- Stroke: 1pt `#F09A38` (Notes Orange)
- Inner highlight: a 0.5pt lighter inner-stroke `#FAE8A0` along the top edge for depth
- Slight 2-degree forward tilt — feels like a real physical folder

**Search Bar (pinned top of list)**
- Background: `#FAF6E3` (Cream Surface 1)
- Height: 36pt
- Corner radius: 10pt
- Leading 14pt `magnifyingglass` Slate
- Placeholder: "Search" SF Pro 17pt 400 Slate
- Trailing: 14pt `mic.fill` Slate (voice search)
- Focused: border 2pt `#F09A38`

**Tag Chip (above pinned)**
- A horizontal scroll of orange-tinted hash chips
- Each chip: 32pt height, `#FFF1DD` background, 16pt corner radius, padding 6pt vertical / 12pt horizontal
- Label: "#recipes" SF Pro 14pt 500 `#F09A38`
- 8pt gap between chips
- Selected state: `#F09A38` background with white text

**Note Body Canvas (editor)**
- Background: `#FFFBED` Notes Cream — no border, no card, just the canvas
- Padding: 20pt horizontal, 16pt top, 16pt bottom
- Content: title (20pt 600 Ink) → body (17pt 400 Ink, line-height 1.5)
- The cursor blinks Notes Orange `#F09A38`
- Selection highlight: `#FFEB78` (the same Yellow used as default highlight color)

**Format Picker (bottom sheet)**
- Slides up from the bottom when "Aa" toolbar button is tapped
- Background: `#FFFBED` with `.regularMaterial` blur if scrolled over content
- Top: 5 type ramps (Title, Heading, Subheading, Body, Monospaced) as horizontally-scrolled chips
- Below: format toggles (B, I, U, S, bulleted list, numbered list, checklist) as 44pt-tall icon buttons in a grid
- Bottom: text color and highlight color swatches (5 each from the Highlight palette)

**Bottom Toolbar (in editor)**
- Sticky bar at the bottom of the editor, 56pt tall + safe area
- Background: `#FFFBED` with `.regularMaterial` blur and a 0.5pt `#EDEAD8` top divider
- Layout: 5 icon buttons evenly spaced
  - Aa (formatting picker)
  - Checklist (`checklist`)
  - Table (`tablecells`)
  - Photo (`camera`)
  - Done (text button, right-aligned)
- Icon size: 22pt, `#1C1C1E`
- Tap haptic: `.selectionChanged()`

### Navigation

**Large-Title Nav Bar**
- Height: variable — 96pt with large title, collapses to 44pt inline
- Background: `#FFFBED` (light) / `#1A1A1A` (dark); `.regularMaterial` blur when content scrolls under
- Title: SF Pro Display 34pt 800 (Heavy) `#1C1C1E`, left-aligned, 16pt leading inset
- Trailing: "Edit" text button in Notes Orange + 24pt `ellipsis.circle` more menu

**Note Editor Nav Bar**
- Height: 44pt
- Leading: back chevron + folder name in Notes Orange ("< All Notes" / "< Recipes")
- Trailing: 24pt `square.and.pencil` (new note), 24pt `square.and.arrow.up` (share), 24pt `ellipsis.circle` (more)
- All icon buttons are Notes Orange

**Folder Sidebar (iPad / iPhone deep navigation)**
- Hierarchical list — iCloud folder header → individual folders → custom folders → smart folders
- Each row: 44pt tall, leading folder glyph + name + trailing count
- Section headers in 13pt 600 uppercase Slate

### Input Fields

**Note Body Editor**
- Inline rich text editor with no chrome — the entire canvas is editable
- Cursor: 2pt vertical bar in Notes Orange, blinks every 600ms
- Selection: yellow highlight `#FFEB78` with rounded ends
- Long-press a word: pops the system text-selection magnifier + suggested actions (Cut/Copy/Paste/Look Up/Translate)

**Checklist Item**
- 24pt × 24pt empty circle with 1.5pt Slate stroke
- Tap to fill: animates to Notes Orange `#F09A38` fill with a white `checkmark` glyph + a subtle "tick" haptic
- The text strikes through and fades to 50% opacity over 300ms

### Distinctive Components

**The Yellow Folder (Apple's iconic mark)**
- The 80pt hero version appears at the top of the All Folders view as a centerpiece graphic
- The 24pt list version appears in every folder row
- Apple has used this exact yellow folder design in macOS Finder, iCloud Drive, and Files — it's a brand asset that crosses apps

**Pinned Note Strip**
- A horizontally-scrolling strip of pinned notes appears at the top of the note list (some users see this as a grid)
- Each pinned card: 160pt wide, 96pt tall, cream surface 1 background, 10pt corner radius
- Inside: title (15pt 600 Ink, 1 line) → preview (12pt 400 Slate, 3 lines)
- Pin glyph (Notes Orange `pin.fill` 11pt) in the top-right corner of each card

**Locked Note**
- When a note is locked, the row shows a 24pt yellow lock glyph `lock.fill` instead of the preview text
- Preview is replaced with "This note is locked." in 14pt 400 Slate italic
- Tap opens a Face ID / passcode prompt overlay

**Smart Folders**
- A small spark glyph `sparkles` 14pt in Notes Orange precedes smart folder names
- Smart folders appear in their own section "Smart Folders" with subtle italic in the header

**Recently Deleted Folder**
- A red-tinted variant of the standard folder glyph (fill `#FFB3AD`, stroke `#FF3B30`)
- 30-day countdown on each note: "Deletes in 27 days" in 12pt 400 red as the date row

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 56, 64
- Standard horizontal margin: 16pt in the list view, 20pt in the editor
- Section vertical gap: 24pt between Tags / Pinned / All Notes
- Note body line-height: 1.5 (Apple's most generous body line-height)

### Grid & Container
- List view: full-width single-column rows with 16pt outer margins
- Editor: single-column full-width content with 20pt horizontal margins on iPhone, 64pt on iPad (centered)
- Folder list on iPad: 280pt-wide sidebar pinned to the left

### Whitespace Philosophy
- **Generous body breathing** — 1.5 line-height on the note body is the calmest body in any iOS app
- **Tight list rows** — 84pt rows with title/preview/date stacked at 2-4pt internal gaps
- **No card chrome in the editor** — the canvas IS the paper; no card, no border, just the cream
- **Large title is the gravity** — 34pt 800 Heavy "Notes" anchors the screen visually

### Border Radius Scale
- Square (0pt): note list rows, editor canvas
- Small (4pt): badges
- Medium (10pt): search bar, pinned card thumbnails
- Standard (12pt): primary CTAs, format picker chips
- Comfortable (16pt): tag chips (half-height pill effect), modal sheets
- Tab-folder shape: a custom path — the iconic yellow folder glyph
- Circle (50%): FAB, checklist circles

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, editor canvas, folder rows |
| Subtle (Level 1) | `rgba(0,0,0,0.04) 0 1px 3px` | Pinned card press states |
| Folder Lift (Level 2) | A 0.5pt warm-cream inner highlight + 2pt drop | The yellow folder glyph (depth illusion) |
| FAB (Level 3) | `rgba(240,154,56,0.30) 0 4px 12px` | New Note FAB (orange-tinted shadow) |
| Sheet (Level 4) | `rgba(0,0,0,0.16) 0 -8px 24px` | Format picker, share sheet |
| Blur Material | `.regularMaterial` over canvas | Nav bar on scroll, bottom toolbar |

**Shadow Philosophy**: Notes is *paper*, not material. Shadows are nearly invisible. The only meaningful elevation is the New Note FAB, which casts an *orange-tinted* shadow to feel like the button is glowing with the brand color. The yellow folder glyph has a hand-painted depth illusion (warm-cream inner highlight + 2pt drop), which is the most "designed" part of the app.

### Motion
- **Note row tap**: scale 0.99 briefly, then the note editor slides in from the right at 350ms cubic-bezier
- **New Note FAB tap**: scale 0.95 → 1.05 → 1.0 over 250ms spring, paired with `.impactOccurred(.medium)`
- **Checklist toggle**: circle fills with Notes Orange via a 200ms scale animation (0 → 1.0), text strike-through animates over 300ms ease-out, `.notificationOccurred(.success)` haptic
- **Pin / Unpin swipe**: row slides left to reveal the pin button; tap triggers `.impactOccurred(.light)` and the row animates to/from the Pinned section over 400ms spring
- **Cursor blink**: 600ms steady cycle on the orange cursor
- **Search field expand**: on focus, the search bar expands to full width; the "Cancel" button slides in from the right at 250ms ease-out
- **Folder yellow tilt**: at the top of the All Folders view, the giant yellow folder gets a subtle 2-degree forward tilt that responds to device tilt via CoreMotion parallax (very slight, <5 degrees max)
- **Tab switch (folders → notes)**: no tab bar in Notes — navigation is push/pop hierarchical with the standard iOS slide transition

## 7. Do's and Don'ts

### Do
- Use `#FFFBED` Notes Cream as the canvas — warmer than white by ~3%, gives the app its paper feel
- Use Notes Orange `#F09A38` for the primary action — FAB, folder glyph stroke, link color, cursor color, selection accent
- Render every folder as the iconic 3D yellow tab-folder with `#F5D773` fill and `#F09A38` stroke
- Use SF Pro across the entire app — no custom typography
- Use SF Pro Display 34pt 800 Heavy for the large "Notes" nav title — this is what distinguishes Notes from other Apple apps
- Use 17pt 400 with 1.5 line-height for note body — the most generous body in any iOS app
- Use the three-line note row pattern: title (Semibold 16pt) → preview (Regular 14pt Slate) → date (Regular 12pt Slate)
- Use `#FFEB78` Yellow as the default highlight color inside note body
- Use the orange-tinted shadow `rgba(240,154,56,0.30) 0 4px 12px` on the New Note FAB — never a neutral shadow
- Use the cream surface family (`#FAF6E3`, `#F2EDD6`) for search bar, press states, pinned section background

### Don't
- Don't use pure white `#FFFFFF` for the canvas — Notes is warmer than that
- Don't use a custom font — Notes is system-typography only
- Don't add a bottom tab bar — Notes uses hierarchical navigation (folders → notes → editor)
- Don't break the three-line note row pattern — title + preview + date is the canonical layout
- Don't use Notes Orange `#F09A38` as a section background — it's an action color, not a fill
- Don't replace the yellow folder glyph with a different color or icon — the yellow folder is a cross-app Apple brand asset
- Don't compress note row height below 84pt — the breathing room is part of the calm
- Don't use card chrome in the editor — the canvas IS the paper, no border or card
- Don't use Selection Yellow `#FFEB78` outside of text-selection highlights — it's reserved for that specific use
- Don't make the FAB shadow neutral — the orange-tinted shadow is part of the brand

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Pinned section becomes a single column instead of horizontal scroll |
| iPhone 13/14/15 | 390pt | Standard 84pt rows; 16pt outer margins |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected; large nav title nudges down 8pt |
| iPhone 15/16 Pro Max | 430pt | Pinned cards can show 4 across instead of 3 |
| iPad | 768pt+ | Three-pane split view: folders sidebar (280pt) + note list (320pt) + editor (flex) |

### Dynamic Type
- All text scales — note titles, body, preview, date, folder names. Notes is one of the most Dynamic-Type-friendly apps because of its age-demographic skew
- **Fixed**: tag chip labels, toolbar labels, search placeholder — layout-sensitive

### Orientation
- iPhone: portrait-locked on the note list and editor (though the editor rotates if you really insist)
- iPad: supports both orientations; in landscape, the 3-pane split view shows all three columns simultaneously

### Touch Targets
- Note row: full 84pt tap target
- FAB: 56pt visual, 44pt minimum tap target met easily
- Folder row: 44pt tap target
- Toolbar buttons: 44pt × 44pt minimum
- Tag chips: 32pt visual, 44pt hit slop minimum

### Safe Area Handling
- Top: large nav title starts at safe-area top + 16pt
- Bottom: FAB sits 16pt from the bottom safe-area inset; toolbar honors home indicator
- Sides: 16pt content insets on phone, 20-64pt on iPad

## 9. Quick Reference Cheat Sheet

### Color Quick Reference
- Notes Cream (canvas): `#FFFBED`
- Cream Surface 1: `#FAF6E3`
- Cream Surface 2: `#F2EDD6`
- Divider Light: `#EDEAD8`
- Notes Orange: `#F09A38`
- Notes Orange Pressed: `#D87E1F`
- Notes Orange Tint (bg): `#FFF1DD`
- Folder Yellow (glyph fill): `#F5D773`
- Highlight Yellow (text selection): `#FFEB78`
- Ink (primary text): `#1C1C1E`
- Slate (secondary text): `#8E8E93`
- Dark Paper (dark canvas): `#1A1A1A`
- Dark Surface 1: `#262626`
- Success Green: `#34C759`
- Error Red: `#FF3B30`

### Example Component Prompts
- "Create a SwiftUI Apple Notes note row: 84pt tall, `#FFFBED` background. Three-row stack: title 'Grocery list' in SF Pro Text 16pt Semibold `#1C1C1E` (1-line truncate), 4pt gap, preview 'Milk, eggs, butter, sourdough bread...' in 14pt 400 `#8E8E93` (1-line truncate), 2pt gap, date 'Yesterday' in 12pt 400 `#8E8E93`. 16pt horizontal padding, 0.5pt `#EDEAD8` divider at the bottom. Swipe-left reveals Pin (Notes Orange), Add to Folder (gray), Delete (red `#FF3B30`)."
- "Build the iconic Apple Notes folder glyph: a tab-folder shape, 24pt at row size. Fill is `#F5D773` Folder Yellow, 1pt `#F09A38` Notes Orange stroke. Add a 0.5pt lighter `#FAE8A0` inner highlight along the top edge for depth, and a subtle 2-degree forward tilt. Render as a Path with the tab on the top-left rising 4pt above the body."
- "Design the New Note FAB: 56pt circle, `#F09A38` Notes Orange fill, white `square.and.pencil` SF Symbol at 24pt. Position: 16pt from bottom-right safe area. Shadow: `rgba(240,154,56,0.30) 0 4px 12px` — orange-tinted, not neutral. Tap: scale 0.95 → 1.05 → 1.0 over 250ms spring with `.impactOccurred(.medium)` haptic."
- "Create the Apple Notes editor canvas: full-width SF Pro Text 17pt 400 body on a `#FFFBED` canvas, line-height 1.5, 20pt horizontal padding. Cursor is a 2pt vertical bar in `#F09A38` Notes Orange that blinks every 600ms. Text selection highlight is `#FFEB78` Yellow with rounded ends. Inline title at top in 20pt 600 Ink, then a 4pt gap before the body."
- "Build the tag chip strip: horizontal scroll of pill-shaped chips. Each chip: 32pt tall, `#FFF1DD` background, 16pt corner radius, padding 6pt vertical / 12pt horizontal. Label 'recipes' in SF Pro Text 14pt 500 `#F09A38` with a leading `#` glyph. Selected: background flips to `#F09A38` with white text. 8pt spacing between chips."

### Iteration Guide
1. Canvas is `#FFFBED` Notes Cream — never pure white; warmer by ~3% for paper feel
2. Primary action is Notes Orange `#F09A38` — FAB, folder stroke, cursor color, link
3. Render the yellow folder glyph (`#F5D773` fill, `#F09A38` stroke) — it's a cross-app Apple brand asset
4. SF Pro everywhere — no custom typography; SF Pro Display 34pt 800 Heavy on the large nav title
5. Note body: 17pt 400 with 1.5 line-height — Apple's calmest body
6. Three-line note row: title (Semibold) + preview (Slate) + date (Slate)
7. Highlight Yellow `#FFEB78` is reserved for text selection inside note body
8. The New Note FAB shadow is orange-tinted, not neutral — `rgba(240,154,56,0.30) 0 4px 12px`
9. No bottom tab bar — Notes uses hierarchical navigation (folders → notes → editor)
10. Dark mode is Dark Paper `#1A1A1A` — true dark gray, not pure black, preserves paper feel
