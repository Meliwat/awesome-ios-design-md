# Design System Inspiration of Dropbox (iOS)

## 1. Visual Theme & Atmosphere

Dropbox's iOS app is a calm, paper-white workspace built around one idea: your files should feel as legible and stress-free as a tidy desk. The canvas is a true white (`#FFFFFF`) with a faint warm-cream surface (`#F7F5F2`) reserved for grouped sections and recents shelves. There is almost no chrome — no gradients, no glass, no decorative texture. The app gets out of the way so the content (a folder of photos, a shared PDF, a scanned receipt) is the only thing with weight. This restraint is deliberate brand strategy: Dropbox sells trust and focus, and the UI reads like a well-lit filing room rather than a feed.

The accent system is monastic. Dropbox Blue (`#0061FF`) — the exact hue of the open-box logomark — is the single brand color, and it is rationed. It appears on the primary action ("Share", "Upload", the floating create button), on the active bottom-tab item, on selected checkboxes, and on links. Everything else is ink, gray, or the warm divider line (`#E6E1DA`). File-type icons carry their own product colors (PDF red, Doc blue, image teal) but those are illustration, not UI accent. The result: when blue appears, it always means "this is the thing to tap."

Typography is Sharp Grotesk (Dropbox's brand typeface as of the 2017+ rebrand), rendered here with Inter as the closest free substitute. It is a clean, slightly condensed grotesque — confident but never loud. The hierarchy is tight and utilitarian: 11–28pt with weights at 400 / 600 / 700. There are no oversized hero headlines because a file browser does not need them; the largest type is the screen title at 28pt bold, and most of the screen is 15–17pt file names in a dense, scannable list. The most expressive type moment is the empty state ("No files yet — tap + to add one") rendered in friendly 17pt regular with a simple line illustration above it.

**Key Characteristics:**
- Paper-white canvas (`#FFFFFF`) with a warm-cream grouped surface (`#F7F5F2`)
- Dropbox Blue (`#0061FF`) as the only accent — primary actions, active tab, links, selection
- File/folder list rows with colored type icons — the signature browsing pattern
- Open-box blue logomark — flat, geometric, never gradient
- Preview thumbnail grid for the Photos tab — square tiles, tight 2pt gutters
- Floating circular blue Upload/Create FAB anchored bottom-right above the tab bar
- Recent files shelf — horizontally scrolling cards above the folder list
- Sharp Grotesk typeface (Inter fallback), weights 400/600/700, condensed grotesque
- Determinate blue upload progress bar — the only persistent motion in the app

## 2. Color Palette & Roles

### Primary
- **Dropbox Blue** (`#0061FF`): Primary CTA, "Share"/"Upload" buttons, upload FAB, active bottom-tab item, selected checkbox fill, hyperlinks.
- **Blue Pressed** (`#0050D0`): Active/pressed state for blue CTAs and the FAB.
- **Blue Tint** (`#E6F0FF`): Selected-row background, focused-input ring, subtle blue chips.

### Canvas & Surfaces (Light)
- **Canvas White** (`#FFFFFF`): Primary canvas — file list background, sheets, nav bar.
- **Surface Cream** (`#F7F5F2`): Grouped section background, recents shelf, settings groups.
- **Surface Raised** (`#FFFFFF` + shadow): Cards and modals lift via shadow, not fill.
- **Divider** (`#E6E1DA`): Warm hairline between file rows and section groups.
- **Text Primary** (`#1E1919`): File names, screen titles, primary UI text (warm near-black).
- **Text Secondary** (`#6F6A65`): File metadata (size, modified date), subtitles, placeholders.
- **Text Tertiary** (`#A39E98`): Disabled labels, very low-emphasis captions.

### Canvas & Surfaces (Dark)
- **Dark Canvas** (`#1E1919`): Primary dark canvas — warm charcoal, not pure black.
- **Dark Surface** (`#2A2424`): Grouped sections, recents shelf, raised cards.
- **Dark Divider** (`#3A3331`): Hairline between rows on dark.
- **Dark Text Primary** (`#FFFFFF`): File names and titles on dark.
- **Dark Text Secondary** (`#A39E98`): Metadata and subtitles on dark.
- **Dark Blue** (`#3D8BFF`): Blue accent lightened for AA contrast on the dark canvas.

### File-Type Icon Colors
- **PDF Red** (`#FA551E`): PDF document tiles and row icons.
- **Doc Blue** (`#0061FF`): Word / Paper document icons.
- **Sheet Green** (`#1A8754`): Spreadsheet icons.
- **Image Teal** (`#00B2A9`): Image and photo file icons.
- **Folder Slate** (`#8C97A8`): Generic folder glyph fill.

### Semantic
- **Success Green** (`#1A8754`): Upload-complete checkmark, "Synced" state.
- **Warning Amber** (`#FFAF00`): "Offline — will sync" banner.
- **Error Red** (`#D1180B`): Failed upload, storage-full, sign-in error.

## 3. Typography Rules

### Font Family
- **Primary (2017+)**: `Sharp Grotesk` (Dropbox brand typeface, by Sharp Type)
- **Substitute / Fallback**: `Inter` — closest free condensed-grotesque match
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerics**: Tabular figures for file sizes and dates so columns align

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Sharp Grotesk | 28pt | 700 | 1.15 | -0.4pt | "Home", "Files", large nav title |
| Section Header | Sharp Grotesk | 22pt | 700 | 1.2 | -0.3pt | "Recents", "Suggested", "Starred" |
| Sheet Title | Sharp Grotesk | 20pt | 700 | 1.2 | -0.2pt | Share sheet, upload sheet header |
| Folder / Card Title | Sharp Grotesk | 17pt | 600 | 1.3 | -0.1pt | Folder tile name, recent-file card |
| File Row Name | Sharp Grotesk | 16pt | 600 | 1.25 | -0.1pt | Primary text in a file list row |
| Body | Sharp Grotesk | 15pt | 400 | 1.45 | 0pt | Descriptions, empty-state copy |
| Metadata | Sharp Grotesk | 14pt | 400 | 1.3 | 0pt | "2.4 MB · Modified Apr 12" |
| Tab Label | Sharp Grotesk | 11pt | 600 | 1.0 | 0.2pt | Bottom tab bar labels |
| Caption / Count | Sharp Grotesk | 12pt | 400 | 1.3 | 0pt | "12 items", "3 people" |
| Label (UPPER) | Sharp Grotesk | 11pt | 700 | 1.2 | 0.6pt | "SHARED WITH YOU" group labels |
| Button (Primary) | Sharp Grotesk | 16pt | 600 | 1.0 | 0pt | Blue "Share" / "Upload" buttons |
| Button (Secondary) | Sharp Grotesk | 15pt | 600 | 1.0 | 0pt | Outline / text buttons |
| Link | Sharp Grotesk | 15pt | 600 | 1.4 | 0pt | Inline blue links, "Manage access" |

### Principles
- **Weights concentrated at 400 / 600 / 700**: Regular for body/metadata, semibold for names, bold for titles — no light, no black
- **Tabular numerals everywhere data aligns**: file sizes, dates, item counts use fixed-width figures
- **Negative tracking on titles only**: -0.4pt max at 28pt; body and metadata sit at 0pt
- **Dynamic Type respected**: file names, metadata, and body scale; tab labels and progress timers are pinned
- **Blue text means link or action**; never use blue for decoration or non-interactive emphasis

## 4. Component Stylings

### Buttons

**Primary Button (Share / Upload / Done)**
- Background: `#0061FF`
- Text: `#FFFFFF`
- Padding: 14pt vertical, 24pt horizontal
- Corner radius: 8pt
- Font: Sharp Grotesk, 16pt, weight 600
- Pressed: background `#0050D0`, scale 0.98, soft haptic
- Disabled: background `#0061FF` at 40% opacity, text `#FFFFFF`

**Secondary / Outline Button (Cancel / Manage)**
- Background: transparent
- Text: `#1E1919`
- Border: 1pt solid `#E6E1DA`
- Padding: 14pt vertical, 24pt horizontal
- Corner radius: 8pt
- Font: Sharp Grotesk, 15pt, weight 600
- Pressed: background `#F7F5F2`

**Upload / Create FAB (The Floating Blue Plus)**
- Shape: Circle, diameter 56pt
- Position: anchored bottom-right, 16pt inset, floating 16pt above the tab bar
- Background: `#0061FF`
- Icon: SF Symbol `plus`, 24pt, `#FFFFFF`
- Shadow: `rgba(0,97,255,0.32) 0 8px 20px`
- Pressed: scale 0.92, background `#0050D0`, with haptic; expands into an action sheet (Upload, Scan, Create Folder, Paper)

**Text Button ("Select" / "Manage access")**
- Font: Sharp Grotesk, 15pt, weight 600
- Color: `#0061FF`
- No background, no underline, 44pt hit area

**Icon Action (Star, Share, Download, More)**
- Size: 22pt glyph, 44pt hit area
- Default: `#6F6A65`
- Active: `#0061FF` (starred, shared, downloaded)
- Spacing: 20pt between icons in a row action bar

### Cards & Containers

**File / Folder Row**
- Height: 60pt
- Layout: 40pt × 40pt type icon (leading) → stacked name + metadata → trailing 20pt ellipsis (⋯)
- Background: `#FFFFFF`
- Pressed: background `#F7F5F2`
- Selected: background `#E6F0FF`, leading icon replaced by a `#0061FF` filled checkbox
- Name: Sharp Grotesk 16pt w600 `#1E1919`; Metadata: 14pt w400 `#6F6A65` ("2.4 MB · Apr 12")
- Folder rows show a `#8C97A8` folder glyph and a trailing chevron instead of ellipsis

**Recent File Card (Horizontal shelf)**
- Width: 140pt, height: 160pt
- Top: 140pt × 96pt preview thumbnail (4pt radius) or a centered type icon on `#F7F5F2`
- Below: file name (17pt w600, 2-line clamp) + metadata (12pt w400 `#6F6A65`)
- Corner radius: 12pt on the card, 1pt border `#E6E1DA`
- Gap between cards: 12pt
- Tap: scale 0.97 with subtle haptic

**Photo Grid Tile**
- Square, 3-column grid, 2pt gutter, edge-to-edge
- Corner radius: 0pt (tiles touch — a contact-sheet feel)
- Selected: 4pt `#0061FF` inset border + a top-right `#0061FF` checkmark badge
- Long-press enters multi-select with a count chip in the nav bar

**Share Sheet**
- Bottom sheet, corner radius 16pt top corners, 16pt grabber
- Background: `#FFFFFF`
- Header: file thumbnail + name + "X people have access"
- Access rows: avatar + name + a permission dropdown ("Can edit" / "Can view")
- Footer: full-width `#0061FF` "Copy link" primary button

**Section Group (Settings / Account)**
- Background: `#F7F5F2` container with `#FFFFFF` rows
- Group header label: 11pt w700 uppercase `#6F6A65`, 0.6pt tracking
- Rows: 52pt tall, 1pt `#E6E1DA` divider inset to align with text

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `rgba(255,255,255,0.94)` with `.regularMaterial` blur, 0.5pt top divider `#E6E1DA`
- Tabs: Home, Files, Photos, Offline, Account
- Icon size: 24pt
- Active color: `#0061FF` (icon + label)
- Inactive: `#6F6A65`
- Labels: Sharp Grotesk 11pt w600, always shown
- Active icon: filled SF Symbol variant; inactive: outlined

**Top Nav**
- Height: 44pt + safe area
- Large title (28pt w700 `#1E1919`) collapsing to inline 17pt on scroll
- Trailing: search icon (22pt) + select/overflow icon (22pt), both `#6F6A65`
- Translucent white blur on scroll, 0.5pt bottom divider

**Search Field (in Search screen)**
- Background: `#F7F5F2`
- Height: 44pt
- Corner radius: 10pt
- Leading SF Symbol `magnifyingglass`, 18pt, `#6F6A65`
- Placeholder: "Search files and folders", 16pt w400 `#6F6A65`
- Focused: 2pt `#0061FF` ring, background `#FFFFFF`

### Input Fields

**Text Field (Rename / Folder name)**
- Background: `#FFFFFF`
- Border: 1pt solid `#E6E1DA`, corner radius 8pt
- Height: 48pt, 14pt horizontal padding
- Label above in 14pt w600 `#1E1919`
- Focused: border `#0061FF` 2pt, subtle `#E6F0FF` glow
- Error: border `#D1180B`, helper text `#D1180B` 13pt

**Permission Dropdown**
- Pill, background `#F7F5F2`, text `#1E1919` 14pt w600
- Trailing chevron `#6F6A65`
- Tap opens a menu: "Can edit", "Can view", "Remove"

### Distinctive Components

**File-Type Row List**
- The core browsing surface: a flat vertical list where each row leads with a colored, glyph-bearing type icon (PDF red, Doc blue, Sheet green, Image teal, Folder slate)
- Icons are simple filled rectangles with a folded corner and a 1-letter or glyph label — no skeuomorphism
- Rows are 60pt, ultra-scannable, with tabular metadata so size/date columns align vertically

**Upload Progress Bar**
- A 3pt determinate bar pinned to the bottom of the active list (above the tab bar) during upload
- Track `#E6E1DA`, fill `#0061FF`, animates left→right by real byte progress (no indeterminate shimmer)
- Accompanied by a 14pt w600 label: "Uploading 3 of 8 · 4.2 MB"
- On completion: bar fills, flashes a `#1A8754` check, then fades after 1.5s

**Preview Thumbnail Grid (Photos tab)**
- A dense, edge-to-edge square grid (3 columns, 2pt gutter) that turns the camera roll into a contact sheet
- Scroll is virtualized; thumbnails fade in as they decode (200ms opacity)
- Pinch to switch between 3-col and 5-col density

**Share Link Banner**
- After "Copy link", a `#E6F0FF` banner slides down: "Link copied · Anyone with the link can view" + a "Change" link
- Auto-dismisses after 3s or on tap

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56
- Standard margin: 16pt horizontal
- Section spacing: 24pt between the recents shelf and the file list
- Row internal padding: 12pt vertical, 16pt horizontal

### Grid & Container
- Content width: full device width, 16pt horizontal margins
- Recents shelf: begins at 16pt left inset, 12pt card gaps, peeks the next card
- Photo grid: 3-column edge-to-edge, 2pt gutter, no outer margin
- Folder grid view (optional): 2-column, 12pt gap, 8pt card radius

### Whitespace Philosophy
- **Dense but breathable lists**: 60pt rows are tall enough to tap comfortably while still showing ~10 files per screen — this is a browse-and-find app
- **Generous empty states**: when a folder is empty, the illustration + copy is vertically centered with ~120pt of air above
- **Content over chrome**: thumbnails and previews get full edge-to-edge width; UI furniture stays thin

### Border Radius Scale
- Square (0pt): Photo grid tiles (intentional contact-sheet edges)
- Soft (4pt): Preview thumbnails inside cards
- Standard (8pt): Buttons, text fields, file-type icons
- Comfortable (12pt): Recent-file cards, content cards
- Sheet (16pt): Bottom sheets, modals (top corners)
- Full Pill (500pt): Permission chips, filter chips
- Circle (50%): Upload FAB, avatars, checkbox

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | File rows, list canvas, section groups |
| Card (Level 1) | `rgba(30,25,25,0.06) 0 1px 3px` | Recent-file cards, content cards |
| Raised (Level 2) | `rgba(30,25,25,0.10) 0 4px 12px` | Search bar on scroll, sticky headers |
| FAB (Level 3) | `rgba(0,97,255,0.32) 0 8px 20px` | Floating Upload/Create button |
| Sheet (Level 4) | `rgba(30,25,25,0.16) 0 -12px 32px` | Share sheet, action sheets, modals |
| Scrim | `rgba(30,25,25,0.40)` | Behind modal sheets and the FAB action menu |

**Shadow Philosophy**: Shadows are soft, low-opacity, and warm-tinted (built from the `#1E1919` ink, not pure black) so they sit naturally on the cream-white canvas. Elevation is used sparingly — only the FAB and active sheets really lift. The FAB is the single exception with a colored blue shadow that signals "primary, floating, always reachable." Everything else stays flat to keep the file list calm and scannable.

### Motion
- **FAB tap**: scale 0.92 → 1.0 spring (damping 0.7), `.impactOccurred(.soft)` haptic, then a 0.25s action-sheet rise
- **Upload progress**: determinate fill animating to real byte ratio, 60fps, no easing tricks — honesty over flourish
- **Row press**: instant `#F7F5F2` background, no scale
- **Pull-to-refresh**: minimal blue spinner, 0.4s
- **Thumbnail decode**: 200ms opacity fade-in as each tile loads
- **Sheet present**: 0.3s spring rise with a `rgba(30,25,25,0.40)` scrim fade
- **Star toggle**: subtle scale bounce 1.0 → 1.12 → 1.0 over 240ms, fill flips to `#0061FF`

## 7. Do's and Don'ts

### Do
- Use a true white canvas (`#FFFFFF`) with the warm cream surface (`#F7F5F2`) for grouped sections
- Reserve Dropbox Blue (`#0061FF`) for primary actions, the active tab, links, and selection — nothing else
- Lead every file row with a colored type icon — it is the core wayfinding signal
- Use tabular numerals for file sizes, dates, and counts so columns align
- Keep the Upload FAB circular, blue, bottom-right, floating above the tab bar
- Show a determinate (real-progress) upload bar — never a fake indeterminate spinner for uploads
- Use warm ink (`#1E1919`) for text and warm dividers (`#E6E1DA`) — not pure black or cold gray
- Render the Photos grid edge-to-edge with 0pt tile radius for the contact-sheet feel
- Use Sharp Grotesk (Inter fallback) at weights 400/600/700 only

### Don't
- Don't use pure black `#000000` for text or dividers — the system is warm; use `#1E1919` / `#E6E1DA`
- Don't add accent colors beyond blue to UI chrome — type-icon colors are illustration, not UI accent
- Don't apply gradients or glass to the logomark or the canvas — Dropbox is flat and matte
- Don't round the photo grid tiles — the square edges are intentional
- Don't use an indeterminate spinner for file uploads — uploads must show real byte progress
- Don't over-elevate — only the FAB and active sheets lift; lists stay flat
- Don't use light or black font weights — Sharp Grotesk runs 400/600/700 here
- Don't hide the upload progress bar while a transfer is active
- Don't use blue text for non-interactive emphasis — blue means link or action

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 2-col photo grid option; recents cards shrink to 128pt |
| iPhone 13/14/15 | 390pt | Standard 140pt recents cards, 3-col photo grid |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in the large title nav |
| iPhone 15/16 Pro Max | 430pt | 4-col photo grid option, 156pt recents cards |
| iPad | 768pt+ | Two-pane: folder sidebar + file list; 5–6 col photo grid |

### Dynamic Type
- File names, metadata, body, empty-state copy: full scale
- Tab labels: fixed 11pt (layout-sensitive)
- Upload progress label: scales; the bar height stays 3pt
- Large title scales up to 34pt before collapsing to inline

### Orientation
- Home / Files / Photos: **portrait-primary**, landscape supported (grid recolumns)
- File preview (PDF / image): **rotates freely** — full-screen viewer
- iPad: **all orientations**, two-pane layout persists

### Touch Targets
- File rows: 60pt height, full-row tappable
- Upload FAB: 56pt — always reachable bottom-right
- Ellipsis / overflow: 44pt hit area
- Tab bar icons: 24pt glyph, 44pt effective hit
- Photo tiles: full tile tappable; long-press to multi-select

### Safe Area Handling
- Top: large title respects safe area + Dynamic Island
- Bottom: FAB floats 16pt above the tab bar; both clear the home indicator
- Upload progress bar pins just above the tab bar, inside safe area
- Sides: 16pt content insets (photo grid goes edge-to-edge intentionally)

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface (cream): `#F7F5F2`
- Divider (warm): `#E6E1DA`
- Text primary (warm ink): `#1E1919`
- Text secondary: `#6F6A65`
- Text tertiary: `#A39E98`
- Dropbox Blue (action): `#0061FF`
- Blue pressed: `#0050D0`
- Blue tint (selection): `#E6F0FF`
- Dark canvas: `#1E1919`
- Dark surface: `#2A2424`
- Success green: `#1A8754`
- Error red: `#D1180B`

### Example Component Prompts
- "Create a SwiftUI Dropbox file row: 60pt height, leading 40×40pt colored type icon (PDF red `#FA551E` rounded-corner glyph), stacked file name 'Q3 Report.pdf' in Inter 16pt weight 600 `#1E1919` and metadata '2.4 MB · Modified Apr 12' in 14pt weight 400 `#6F6A65` with tabular figures. Trailing 20pt ellipsis `#6F6A65`. Pressed background `#F7F5F2`; selected background `#E6F0FF` with a `#0061FF` filled checkbox replacing the icon."
- "Build the Upload FAB: 56pt circular button, `#0061FF` background, SF Symbol `plus` 24pt `#FFFFFF`, shadow `rgba(0,97,255,0.32) 0 8px 20px`, anchored bottom-right 16pt inset and 16pt above the tab bar. Pressed: scale 0.92 with soft haptic, background `#0050D0`, then present an action sheet (Upload, Scan, Create Folder)."
- "Design the recents shelf: section header 'Recents' in Inter 22pt weight 700 `#1E1919`. Below: horizontal scroll of 140×160pt cards (12pt radius, 1pt `#E6E1DA` border), each with a 140×96pt preview thumbnail (4pt radius) on top, file name 17pt weight 600 (2-line clamp) and '12 KB · Today' in 12pt weight 400 `#6F6A65` below. 12pt gap, peek the next card."
- "Create the upload progress bar: 3pt determinate bar pinned above the tab bar, track `#E6E1DA`, fill `#0061FF` driven by real byte ratio (no shimmer). Above it a 14pt weight 600 `#1E1919` label 'Uploading 3 of 8 · 4.2 MB'. On completion fill to 100%, flash a `#1A8754` checkmark, fade after 1.5s."
- "Build the Photos grid: edge-to-edge 3-column square tiles, 2pt gutter, 0pt corner radius (contact-sheet feel). Tiles fade in over 200ms as they decode. Long-press enters multi-select: selected tiles get a 4pt `#0061FF` inset border and a top-right `#0061FF` checkmark badge; a count chip appears in the nav bar."

### Iteration Guide
1. Canvas is true white `#FFFFFF`; grouped sections use warm cream `#F7F5F2` — never gray
2. Dropbox Blue `#0061FF` only for primary action, active tab, links, and selection
3. Text and dividers are warm (`#1E1919`, `#E6E1DA`) — never pure black or cold gray
4. Every file row leads with a colored type icon — it is the core wayfinding device
5. Uploads show a real determinate progress bar — honesty over decorative spinners
6. Typography: Sharp Grotesk (Inter fallback), weights 400/600/700, tabular numerals for data
7. Only the FAB and active sheets elevate — the file list stays flat and scannable
8. Photo grid is edge-to-edge with 0pt tile radius — keep the contact-sheet square integrity
