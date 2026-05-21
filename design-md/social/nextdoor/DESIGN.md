# Design System Inspiration of Nextdoor (iOS)

## 1. Visual Theme & Atmosphere

Nextdoor's iOS app feels like a friendly community bulletin board, not a tech product. The defining choice is the canvas: a warm cream (`#FAF9F6`) rather than the cold pure-white of most social apps. That subtle off-white warmth signals neighborliness — it reads as paper, as a notice pinned to a fence, as something local and human. Cards sit on top as crisp white (`#FFFFFF`) surfaces, so the cream shows through as the gutter between them, giving the feed a soft, approachable rhythm. Text is a near-black warm ink (`#221E1F`) with a muted brown-gray secondary (`#6B6864`) — even the grays lean warm to match the cream.

The accent system is anchored by Nextdoor Green (`#00B246`) — a confident, civic green used for the primary action, the center "Post" FAB, follow/join buttons, and verified-neighbor accents. Its pressed state deepens to `#007A30`, and links use a slightly darker forest green (`#006B3C`) for legibility on cream. Green here reads as "community," "trusted," "go" — it is the color of the local park and the recycling bin, not a startup's brand gradient. The single most distinctive screen is the hyperlocal map: a real neighborhood map with category pins (recommendations, for-sale, alerts, events) dropped at actual addresses — the product's core promise that everything is *near you* made visual.

Typography is Lato (a warm, rounded humanist sans) mapped to a system fallback. Lato's slightly soft terminals reinforce the friendly, civic tone — it is not a sharp grotesque. Display names are bold with a verified-neighbor badge and a location line ("0.3 mi · Maple Heights"); the location is a first-class metadata element because *where* a neighbor is matters as much as *who* they are. Headlines are warm and bold; body text is comfortable for reading multi-paragraph neighborhood posts. The whole system says: this is your street, in print, made simple.

**Key Characteristics:**
- Warm cream canvas (`#FAF9F6`) with crisp white (`#FFFFFF`) cards — paper, not screen
- Nextdoor Green (`#00B246`) for primary actions, the center Post FAB, follow/join, verified
- Hyperlocal map with category pins at real addresses — the signature screen
- Neighbor cards with name + verified badge + distance/neighborhood location line
- Lato typeface (warm humanist sans) mapped to a system fallback
- Group/topic chips for filtering the feed by community
- Subtle, gentle motion — fades and a soft FAB press, nothing flashy
- Bottom tabs (Home / Map / Post-center FAB / Notifications / Inbox)
- Light mode only — the warm cream is the identity

## 2. Color Palette & Roles

### Primary
- **Nextdoor Green** (`#00B246`): Primary CTA, center Post FAB, Follow/Join buttons, verified-neighbor accent, active tab.
- **Green Pressed** (`#007A30`): Active/pressed state for green CTAs and the FAB.
- **Link Forest** (`#006B3C`): Hyperlinks, tappable place/post references — a darker green for contrast on cream.

### Canvas & Surfaces (Light only)
- **Cream Canvas** (`#FAF9F6`): The signature warm background — paper, not pure white.
- **Surface 1** (`#FFFFFF`): Cards, the map sheet, composer — crisp white on cream.
- **Surface 2** (`#F2F0EB`): Pressed rows, chip backgrounds, grouped settings.
- **Divider** (`#E4E2DD`): Hairline separators, card borders — a warm light tan.
- **Text Primary** (`#221E1F`): Post body, names, headlines — a warm near-black ink.
- **Text Secondary** (`#6B6864`): Location lines, timestamps, metadata — a warm brown-gray.
- **Text Tertiary** (`#9C9893`): Disabled labels, low-emphasis hints.

### Semantic
- **Verified Green** (`#00B246`): Verified-neighbor checkmark badge (same as primary green).
- **Alert Amber** (`#E58A00`): Local alerts, urgent neighborhood notices banner.
- **Error Red** (`#D93A2B`): Destructive confirms, validation, report flow.
- **Event Blue** (`#1B6FB3`): Event category pins and event RSVP accents on the map.
- **For-Sale Purple** (`#6E4FA3`): For-sale/free category pins on the map.

### Dark Mode (Not Used)
Nextdoor's iOS app is light-only by design — the warm cream canvas is the brand's identity and does not invert.

## 3. Typography Rules

### Font Family
- **Primary**: `Lato` (warm humanist sans — friendly civic tone)
- **Fallback Stack**: `'Lato', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **System fallback**: SF Pro on iOS when Lato is unavailable — keep weights aligned

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Lato | 28pt | 700 | 1.2 | -0.3pt | "Neighborhood", "Notifications" nav title |
| Section Header | Lato | 22pt | 700 | 1.2 | -0.2pt | "For Sale & Free", "Recommendations" |
| Post Title | Lato | 18pt | 700 | 1.3 | -0.1pt | A post's headline (when present) |
| Neighbor Name | Lato | 16pt | 700 | 1.25 | 0pt | Poster's name on a feed card |
| Body | Lato | 16pt | 400 | 1.45 | 0pt | Post text — tuned for reading neighborhood updates |
| Body (Settings) | Lato | 15pt | 400 | 1.4 | 0pt | Settings descriptions |
| Location / Meta | Lato | 13pt | 400 | 1.3 | 0pt | "0.3 mi · Maple Heights · 2h" |
| Group Chip | Lato | 14pt | 700 | 1.0 | 0.1pt | Feed filter chips ("Crime & Safety") |
| Action Label | Lato | 14pt | 700 | 1.0 | 0pt | "React", "Reply", "Share" under a card |
| Button (Primary) | Lato | 16pt | 700 | 1.0 | 0pt | "Post", "Join", "Follow" |
| Tab Label | Lato | 10pt | 700 | 1.0 | 0.1pt | Bottom tab bar labels |
| Map Pin Label | Lato | 12pt | 700 | 1.0 | 0pt | Price/category label inside a map pin |

### Principles
- **Warm humanist face**: Lato's soft terminals carry the neighborly, civic tone — not a sharp grotesque
- **Location is structural**: the 13pt distance/neighborhood line is always present, never optional
- **Weight at 400 / 700**: Lato regular for body, bold for names/headlines/buttons — a simple two-weight system
- **System fallback aligned**: if Lato is missing, fall back to SF Pro at the same sizes/weights
- **Green text only for links** — body text never uses the accent; names are ink, not green

## 4. Component Stylings

### Buttons

**Primary Button ("Post" / "Join" / "Follow")**
- Background: `#00B246`
- Text: `#FFFFFF`
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: 24pt (soft rounded — friendly, not a hard rectangle, not a full pill on wide buttons)
- Font: Lato 16pt weight 700
- Pressed: `#007A30`, scale 0.98
- Disabled: `#00B246` at 40% opacity

**Secondary / Following Button**
- Background: transparent
- Text: `#00B246`
- Border: 1.5pt solid `#00B246`
- Padding: 10pt vertical, 20pt horizontal
- Corner radius: 24pt
- Pressed: background `#F2F0EB`

**Center Post FAB (Bottom Tab Center)**
- Shape: Circle, 56pt diameter, raised in the center notch of the tab bar
- Background: `#00B246`
- Icon: SF Symbol `plus` 26pt in `#FFFFFF`
- Shadow: `rgba(0,178,70,0.32) 0 6px 18px`
- Pressed: scale 0.94, `#007A30`, soft haptic + gentle scale-up of the compose sheet

**Card Action Buttons (React / Reply / Share)**
- Layout: horizontal row under the card body, icon + label
- Size: 20pt glyph, 14pt w700 label, 44pt hit area
- Default: `#6B6864`
- Active (reacted): `#00B246`
- Spacing: evenly distributed across the card footer

### Cards & Containers

**Neighborhood Feed Card (THE core unit)**
- Background: `#FFFFFF` on the `#FAF9F6` cream canvas (cream is the gutter)
- Border: 1pt `#E4E2DD`, corner radius 16pt
- Margin: 12pt between cards (cream shows through), 16pt horizontal page inset
- Padding: 16pt
- Header: 44pt circular avatar → neighbor name (16pt w700) + green verified badge → location line (13pt `#6B6864`: "0.3 mi · Maple Heights · 2h") → trailing ⋯
- Body: 16pt w400, 1.45 line height; links/place refs in `#006B3C`
- Optional photo: full-width, 12pt radius
- Footer: React · Reply · Share row, evenly spaced; a thin `#E4E2DD` divider above it
- Reaction summary: small inline count ("12 neighbors thanked this")

**Map Pin & Cluster**
- Pin: a rounded teardrop/circle in the category color (green recommendations, blue events, purple for-sale, amber alerts) with a 12pt w700 white label (price or glyph)
- Selected pin: scales 1.15 and gains a `#00B246` ring + white border
- Cluster: a circle with a count ("8") when pins overlap at a zoom level
- Tapping a pin slides up a peek card (the underlying post) from the bottom

**Hyperlocal Map Sheet**
- Full-screen map (real neighborhood tiles) with category pins at actual addresses
- Top: category filter chips (All · Recommendations · For Sale · Events · Alerts)
- Bottom: a draggable peek sheet showing the selected post or a list of nearby posts
- "Recenter to my neighborhood" circular button (44pt) bottom-right

**Group / Topic Chip Row**
- Horizontally scrollable chips above the feed: "Crime & Safety", "Recommendations", "For Sale & Free", "Events", "General"
- Default: `#F2F0EB` background, `#221E1F` text
- Selected: `#00B246` background, `#FFFFFF` text
- Corner radius: 500pt (full pill), 14pt w700 label

**Profile / Neighbor Header**
- Cover strip (light), 110pt
- Avatar 80pt circular, overlapping by 32pt, 4pt cream-colored ring
- Name 22pt w700 + verified badge, location line 15pt `#6B6864`
- "Follow" green primary + message + ⋯
- "Neighbor since 2019 · 142 helpful reactions" stat line

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` (opaque, sits on cream) with a 0.5pt `#E4E2DD` top hairline
- Tabs: Home, Map, **Post (center raised FAB)**, Notifications, Inbox
- Icon size: 26pt
- Active: `#00B246`; Inactive: `#6B6864`
- Labels: Lato 10pt w700
- Center Post button is a raised 56pt green circle breaking the top edge of the bar
- Notifications/Inbox show a green unread dot badge

**Top Nav (Home)**
- Height: 48pt + safe area
- Left: neighborhood name (18pt w700) with a small chevron (tap to switch neighborhood)
- Right: search (22pt) + filter (22pt)
- Sits on cream; subtle hairline on scroll

### Input Fields

**Search Field**
- Background: `#FFFFFF` with 1pt `#E4E2DD` border
- Height: 42pt, corner radius 500pt (pill)
- Leading `magnifyingglass` 16pt `#6B6864`
- Placeholder: "Search your neighborhood", 15pt w400 `#6B6864`
- Focus: 1.5pt `#00B246` ring

**Post Composer**
- White sheet on cream backdrop
- Audience selector at top: "Neighborhood" / "Nearby neighborhoods" / a specific Group (chevron)
- Category picker chips: General / Recommendation / For Sale / Event / Crime & Safety
- Plain text area 18pt w400 for comfortable writing
- Photo, poll, and location-tag affordances in a toolbar

### Distinctive Components

**Hyperlocal Map View**
- The signature: a real neighborhood map with category-colored pins at actual addresses, a category filter chip row, and a draggable peek sheet — the entire "everything is near you" promise rendered as the product's hero screen

**Verified-Neighbor Badge**
- A small green `checkmark.seal.fill` next to a name, signaling an address-verified resident — trust is a first-class visual in a local network

**Location Line**
- The "0.3 mi · Maple Heights · 2h" metadata under every name — distance and neighborhood are inseparable from identity here

**Group Chips**
- The community filter row — tapping a chip reframes the feed to one topic/group, making the feed locally scoped and browsable

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56
- Standard margin: 16pt horizontal page inset
- Card rhythm: 12pt gap between cards (cream canvas is the gutter)

### Grid & Container
- Content width: full device width, 16pt page insets, cards full-width within
- Map: full-bleed edge-to-edge; the peek sheet is full-width with a 16pt-radius top
- For-sale grid (in category view): 2-column card grid, 12pt gap

### Whitespace Philosophy
- **Warm and breathing**: the cream gutter between white cards gives the feed a soft, unhurried rhythm
- **Reading comfort**: 16pt body at 1.45 line height for multi-paragraph neighborhood posts
- **Cards are objects**: each post is a discrete bordered white card (unlike divider-only timelines) — like notices pinned to a board

### Border Radius Scale
- Square (0pt): Rare — full-bleed map only
- Soft (12pt): Photos, map peek-sheet inner media
- Card (16pt): Feed cards, sheets' top corners
- Friendly (24pt): Primary/secondary buttons (soft, not pill, not sharp)
- Full Pill (500pt): Group chips, search field
- Circle (50%): Avatars, the center Post FAB, map recenter button, verified badge

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Cream canvas, chip row |
| Card (Level 1) | `rgba(34,30,31,0.06) 0 1px 3px` + 1pt `#E4E2DD` border | Feed cards lift gently off the cream |
| Map Pin (Level 2) | `rgba(34,30,31,0.25) 0 2px 6px` | Pins float above the map tiles |
| FAB (Level 2) | `rgba(0,178,70,0.32) 0 6px 18px` | Center Post button |
| Peek Sheet (Level 3) | `rgba(34,30,31,0.15) 0 -12px 36px` | Map bottom sheet, menus |
| Tab Bar | `rgba(34,30,31,0.06) 0 -1px 0` hairline | Opaque white bar over cream |

**Shadow Philosophy**: Shadows are soft, warm-tinted (based on the ink `#221E1F`, not pure black), and gentle — Nextdoor is calm, not dramatic. Feed cards get only the faintest lift (0.06 opacity) plus a warm tan border, so they read as paper notices rather than floating glass. The two real elevations are map pins (which must clearly sit above the map) and the green center FAB. Nothing in the app uses heavy or cold drop shadows.

### Motion
- **FAB press**: scale 0.94, `#007A30`, soft haptic; compose sheet rises with a gentle 0.3s ease-out
- **Reaction tap**: icon fades to `#00B246` with a subtle 1.0 → 1.12 → 1.0 scale over 250ms, light haptic
- **Card appear**: new feed cards fade + 8pt rise over 0.25s ease-out (no bounce)
- **Map pin select**: pin scales 1.0 → 1.15 with a green ring; peek sheet slides up 0.3s ease-out
- **Chip switch**: 0.2s cross-fade of the feed when a group chip is selected
- **Pull-to-refresh**: standard system spinner, no custom flourish — calm by default

## 7. Do's and Don'ts

### Do
- Use the warm cream `#FAF9F6` canvas with crisp white `#FFFFFF` cards — paper, not screen
- Reserve Nextdoor Green (`#00B246`) for primary actions, the center FAB, follow/join, and verified
- Always show the location line (distance · neighborhood · time) under every name
- Make the hyperlocal map a first-class screen with category-colored pins at real addresses
- Render feed posts as discrete bordered white cards with a 12pt cream gutter
- Use Lato (or an aligned system fallback) — the warm humanist tone matters
- Keep the verified-neighbor badge in green next to names
- Keep motion gentle — fades and soft scales, never bouncy or flashy

### Don't
- Don't use cold pure-white as the canvas — the warm cream is the identity
- Don't add a dark mode — Nextdoor is light-only by design
- Don't drop the location/distance line — local context is the product
- Don't use cold black shadows — shadows are warm-tinted and faint
- Don't make buttons hard rectangles or full pills — the 24pt friendly radius is the tone
- Don't bury the map — it's the hero, not a tab afterthought
- Don't add accent colors beyond green and the category-pin palette
- Don't over-animate — calm, civic, and unhurried is the brand

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Avatar 40pt; for-sale grid stays 2-col |
| iPhone 13/14/15 | 390pt | Standard 44pt avatar, 16pt insets |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the neighborhood title |
| iPhone 15/16 Pro Max | 430pt | Body measure widens; FAB stays 56pt |
| iPad | 768pt+ | Two-column feed + persistent map split, max 640pt reading column |

### Dynamic Type
- Post body, names, headlines: full scale (reading app — honor it)
- Location line, timestamps: scale modestly
- Tab labels: fixed 10pt
- Map pin labels: fixed 12pt (layout-critical inside pins)

### Orientation
- Feed / Profile: portrait and landscape (iPad split)
- Map: portrait and landscape (more tiles in landscape)
- Composer: portrait-locked on iPhone

### Touch Targets
- Card action buttons: 20pt glyph, 44pt hit area
- Center Post FAB: 56pt
- Tab icons: 26pt glyph, 44pt effective hit
- Map pins: minimum 36pt tappable, 44pt effective with hit-slop

### Safe Area Handling
- Top: neighborhood title and chip row sit below safe area / Dynamic Island
- Bottom: tab bar (with raised center FAB) respects the home indicator; map peek-sheet docks above it
- Sides: 16pt content insets; the map is edge-to-edge

## 9. Agent Prompt Guide

### Quick Color Reference
- Cream canvas: `#FAF9F6`
- Card surface: `#FFFFFF`
- Surface 2 (chips/pressed): `#F2F0EB`
- Divider / card border: `#E4E2DD`
- Text primary (warm ink): `#221E1F`
- Text secondary (warm gray): `#6B6864`
- Nextdoor Green (action): `#00B246`
- Green pressed: `#007A30`
- Link forest: `#006B3C`
- Alert amber: `#E58A00`
- Event blue (pin): `#1B6FB3` · For-sale purple (pin): `#6E4FA3`

### Example Component Prompts
- "Create a SwiftUI Nextdoor feed card: white `#FFFFFF` card on a `#FAF9F6` cream canvas, 1pt `#E4E2DD` border, 16pt corner radius, 12pt gap to the next card. Header: 44pt circular avatar, neighbor name 'Dana R.' in Lato 16pt weight 700 with a green `checkmark.seal.fill` verified badge, then a location line '0.3 mi · Maple Heights · 2h' in 13pt `#6B6864`, trailing ⋯. Body 16pt weight 400 at 1.45 line height with place links in `#006B3C`. Footer: React · Reply · Share row, 20pt glyphs, 14pt weight-700 labels, with a 1pt `#E4E2DD` divider above it."
- "Build the Nextdoor center Post FAB: a 56pt green `#00B246` circle raised into the center notch of the bottom tab bar, SF Symbol `plus` 26pt `#FFFFFF`, shadow rgba(0,178,70,0.32) 0 6px 18px. Pressed: scale 0.94, `#007A30`, soft haptic, and the compose sheet rises with a gentle 0.3s ease-out."
- "Design the Nextdoor hyperlocal map: a full-bleed neighborhood map with category-colored pins (green recommendations, blue `#1B6FB3` events, purple `#6E4FA3` for-sale, amber `#E58A00` alerts) at real addresses, each pin a rounded teardrop with a 12pt weight-700 white label. A category filter chip row pinned at the top (All · Recommendations · For Sale · Events · Alerts) and a draggable bottom peek sheet showing the selected post. Selected pin scales 1.15 with a `#00B246` ring."
- "Create a Nextdoor group chip row: horizontally scrollable pills above the feed — 'Crime & Safety', 'Recommendations', 'For Sale & Free', 'Events'. Default `#F2F0EB` background with `#221E1F` text; selected pill `#00B246` background with `#FFFFFF` text; 500pt radius, Lato 14pt weight 700; switching cross-fades the feed over 0.2s."
- "Build the Nextdoor primary button: `#00B246` fill, `#FFFFFF` Lato 16pt weight 700 text, 12pt vertical / 24pt horizontal padding, 24pt friendly corner radius (not a hard rectangle, not a full pill). Pressed: `#007A30`, scale 0.98."

### Iteration Guide
1. Canvas is warm cream `#FAF9F6` — never cold pure-white; cards are crisp `#FFFFFF`
2. Nextdoor Green `#00B246` is primary actions + the center FAB + verified; links use forest `#006B3C`
3. The hyperlocal map with category-colored pins at real addresses is the hero screen
4. Every name carries a location line (distance · neighborhood · time) — local context is the product
5. Posts are discrete bordered white cards with a 12pt cream gutter — paper notices, not a divider timeline
6. Typeface is Lato (warm humanist) at weights 400/700 — fall back to SF Pro aligned
7. Shadows are faint and warm-tinted (based on ink, not black); buttons use a 24pt friendly radius
8. Light mode only; motion is gentle — fades and soft scales, never bouncy
