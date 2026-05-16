# Design System Inspiration of Grindr (iOS)

## 1. Visual Theme & Atmosphere

Grindr's iOS app is an unapologetically dark, photo-first grid. The canvas is pure black (`#000000`) — not the softened "not-quite-black" of media apps, but true black, because the entire screen is wallpapered with profile photography and black is the frame that makes skin tones, lighting, and faces pop. The signature surface is the **cascade**: a dense, edge-to-edge 3-column grid of square profile thumbnails sorted by physical proximity, the closest person top-left. There is almost no chrome between you and the grid — Grindr's product thesis is immediacy, and the UI is built to disappear behind the photos.

The accent system is a single high-voltage color: Grindr Yellow (`#FFDE00`), the exact hue of the brand mask logomark. It is rationed hard. Yellow appears on the primary action (the "Tap" / send-interest button, the active send arrow), on the active bottom-tab item, on unread badges, and on the brand mask itself. Online presence uses a separate semantic green dot (`#4CD964`) overlaid on the thumbnail corner. Everything else is white, gray, or near-black surface. The restraint is what makes the yellow read as "act now."

Typography is a clean grotesque — Grindr's product type, rendered here with Inter as the closest free substitute, at weights 400 / 700. The hierarchy is compact and overlay-driven: most text lives *on top of* photos as small, high-contrast labels (a display name at 15pt bold, a distance tag at 12pt bold), protected by a subtle bottom gradient scrim so it stays legible over any image. There are no large editorial headlines — the photos are the headline. The most expressive type moment is the profile sheet, where the display name jumps to 24pt bold over the hero photo.

**Key Characteristics:**
- True-black canvas (`#000000`) — the photo grid *is* the interface
- Grindr Yellow (`#FFDE00`) as the only accent — Tap action, active tab, badges, mask logo
- Dense cascade: edge-to-edge 3-column square thumbnail grid sorted by proximity
- Online green dot (`#4CD964`) — presence indicator overlaid on the thumbnail corner
- Distance label overlaid bottom-left on each tile ("220 ft", "0.4 mi") with a gradient scrim
- Profile sheet — full-bleed hero photo, slides up from the grid
- Mask logomark — flat yellow, the single brand mark
- Lazy-load fade — thumbnails decode and fade in as you scroll the cascade
- Tap action — the signature interest gesture, a yellow flame/tap button

## 2. Color Palette & Roles

### Primary
- **Grindr Yellow** (`#FFDE00`): Primary CTA, "Tap" button, send arrow active, active bottom-tab item, unread badge, mask logomark.
- **Yellow Pressed** (`#E6C700`): Active/pressed state for yellow CTAs.
- **Yellow Tint** (`#FFF2A8`): Subtle yellow highlight on selected chips or focused fields (used sparingly).

### Canvas & Surfaces (Dark-only)
- **Canvas Black** (`#000000`): Primary canvas — the cascade grid background, behind all photos.
- **Surface 1** (`#1A1A1A`): Card backgrounds, list rows, the tab bar fill.
- **Surface 2** (`#222222`): Higher elevation — sheets, modals, message bubbles (incoming).
- **Surface 3** (`#2C2C2C`): Pressed state on dark surfaces, hairline-adjacent fills.
- **Divider** (`#2C2C2C`): Hairline between list rows and message groups.
- **Text Primary** (`#FFFFFF`): Display names, headlines, primary UI text.
- **Text Secondary** (`#999999`): Distance, metadata, timestamps, placeholders, inactive tabs.
- **Text Tertiary** (`#5C5C5C`): Disabled labels, very low-emphasis captions.

### Photo Overlay
- **Scrim Gradient**: Bottom-anchored vertical gradient on each thumbnail from `rgba(0,0,0,0)` (top) to `rgba(0,0,0,0.65)` (bottom ~40%) so overlaid name/distance text stays legible over any photo.
- **Overlay Text** (`#FFFFFF`): Name and distance labels rendered on the scrim.

### Semantic
- **Online Green** (`#4CD964`): Active "online now" presence dot on the thumbnail corner and profile.
- **Fresh Yellow** (`#FFDE00`): "New here" / fresh-profile ribbon (reuses the brand yellow).
- **Error Red** (`#FF3B30`): Connection lost, blocked-user states, failed send.
- **Info Blue** (`#3D8BFF`): Links in settings and policy text.

### Light Mode (Limited Use)
Grindr's iOS app is effectively dark-only — the photo grid demands a black frame. A light variant exists only for static / embed contexts.
- **Light Canvas** (`#FFFFFF`)
- **Light Text** (`#000000`)

## 3. Typography Rules

### Font Family
- **Primary**: Grindr product grotesque (custom; brand-aligned)
- **Substitute / Fallback**: `Inter` — closest free grotesque match
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Helvetica Neue', Helvetica, Arial, sans-serif`
- **Overlay legibility**: All on-photo text is weight 700 with a scrim behind it — never thin over imagery

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Profile Name (Hero) | Inter | 24pt | 700 | 1.15 | -0.3pt | Display name on the profile sheet hero |
| Screen Title | Inter | 22pt | 700 | 1.2 | -0.2pt | "Messages", "Taps", nav titles |
| Section Header | Inter | 18pt | 700 | 1.25 | -0.1pt | "Online now", "New near you" |
| Tile Name (Overlay) | Inter | 15pt | 700 | 1.2 | 0pt | Display name overlaid on a grid tile |
| List Row Title | Inter | 16pt | 700 | 1.3 | -0.1pt | Name in a message/tap list row |
| Body | Inter | 15pt | 400 | 1.45 | 0pt | Profile "About", message text |
| Message Text | Inter | 16pt | 400 | 1.35 | 0pt | Chat bubble copy |
| Metadata | Inter | 13pt | 400 | 1.3 | 0pt | "Active 5 min ago", row subtitle |
| Distance Tag (Overlay) | Inter | 12pt | 700 | 1.0 | 0.2pt | "220 ft" / "0.4 mi" on the tile scrim |
| Caption / Count | Inter | 12pt | 400 | 1.3 | 0pt | "12 new", small counts |
| Label (UPPER) | Inter | 11pt | 700 | 1.2 | 0.6pt | "ONLINE NOW" section labels |
| Button (Primary) | Inter | 16pt | 700 | 1.0 | 0.2pt | Yellow "Tap" / "Send" buttons |
| Tab Label | Inter | 10pt | 700 | 1.0 | 0.2pt | Bottom tab bar labels |

### Principles
- **Weights at 400 / 700 only**: Regular for body and metadata, bold for names, titles, and all overlay text — no semibold, no thin
- **Overlay text is always bold + scrimmed**: never place light or unprotected text over a photo
- **Negative tracking on large titles**: -0.3pt at 24pt; body sits at 0pt; overlay tags get +0.2pt for legibility
- **Dynamic Type respected on lists and chat**: grid overlay labels are pinned (layout-sensitive over photos)
- **Yellow text is reserved for the active tab label**; elsewhere yellow is a fill, not a text color

## 4. Component Stylings

### Buttons

**Primary "Tap" Button (The Yellow Interest Action)**
- Shape: Circle or pill, 56pt diameter (circular on profile) / full-pill (in action rows)
- Background: `#FFDE00`
- Icon/Text: `#000000` (intentionally black for max contrast on yellow)
- Icon: flame / tap glyph 24pt, or "Tap" label in 16pt w700
- Pressed: scale 0.92, background `#E6C700`, with haptic
- Sent state: brief scale bounce + glyph swap to a filled check, then fades to a "Tapped" outline state

**Primary Pill (Send / Chat / Unlock)**
- Background: `#FFDE00`
- Text: `#000000`
- Padding: 12pt vertical, 28pt horizontal
- Corner radius: 500pt (full pill)
- Font: Inter, 16pt, weight 700, +0.2pt tracking
- Pressed: `#E6C700`, scale 0.97

**Outline Pill ("Tapped" / "Blocked")**
- Background: transparent
- Text: `#FFFFFF`
- Border: 1pt solid `#999999`
- Padding: 12pt vertical, 24pt horizontal, corner radius 500pt
- Font: Inter, 15pt, weight 700
- Pressed: border `#FFFFFF`

**Icon Action (Tap, Favorite, Block, More, Photo)**
- Size: 24pt glyph, 44pt hit area
- Default: `#FFFFFF` (on photo) / `#999999` (on dark surface)
- Active: `#FFDE00` (tapped, favorited)
- Spacing: 20pt between icons in the profile action bar

**Text Button ("See all", "Edit profile")**
- Font: Inter, 14pt, weight 700
- Color: `#999999`
- No background, no underline, 44pt hit area

### Cards & Containers

**Cascade Grid Tile (The Signature)**
- Square, 3-column edge-to-edge grid, 2pt gutter
- Corner radius: 0pt (tiles touch — a wall of presence)
- Content: full-bleed profile photo, cover-cropped
- Overlay (bottom): a `rgba(0,0,0,0.65)` scrim, name (15pt w700 white, 1-line) + distance (12pt w700 white, +0.2pt)
- Top-right: online green dot (`#4CD964`), 10pt, 2pt black ring
- Top-left (optional): "NEW" yellow ribbon for fresh profiles
- Pressed: scale 0.97 + subtle dim, opens the profile sheet

**Profile Sheet**
- Full-bleed hero photo (device width, ~480pt tall), swipeable photo carousel with dot indicators
- Slides up from the cascade with a shared-element photo transition
- Below the fold: name (24pt w700) + age, distance row, "About" body (15pt w400), stats grid
- Bottom action bar pinned: Favorite, large yellow "Tap" / "Chat" button, Block/More
- Background below the photo: `#000000`

**Message List Row**
- Height: 72pt
- Layout: 52pt circular avatar (with online dot) → name (16pt w700) + last message preview (13pt w400 `#999999`, 1-line) → trailing timestamp (12pt) + unread yellow dot
- Background: `#000000`; Pressed: `#1A1A1A`
- Unread: name stays white, preview brightens, a `#FFDE00` 8pt dot trails

**Chat Bubble**
- Incoming: background `#222222`, text `#FFFFFF`, corner radius 18pt (4pt on the tail corner)
- Outgoing: background `#FFDE00`, text `#000000`, corner radius 18pt (4pt on the tail corner)
- Max width 76% of screen, 10pt vertical / 14pt horizontal padding
- Timestamp: 11pt `#999999` below the last bubble in a group

**Filter / Tag Chip**
- Background: `#1A1A1A` default / `#FFDE00` selected
- Text: `#FFFFFF` default / `#000000` selected
- Padding: 8pt vertical, 16pt horizontal, corner radius 500pt
- Font: Inter, 13pt, weight 700

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(0,0,0,0.94)` with `.regularMaterial` blur, 0.5pt top divider `#2C2C2C`
- Tabs: Browse, Taps, Messages, Profile
- Icon size: 26pt
- Active color: `#FFDE00` (icon + label)
- Inactive: `#999999`
- Labels: Inter 10pt w700, always shown
- Badge: yellow `#FFDE00` pill with black count text on Taps / Messages

**Top Bar (in-grid)**
- Height: 44pt + safe area
- Left: yellow mask logomark (24pt)
- Center: location / cascade title (18pt w700) or filter chip row
- Right: filter icon (22pt) + settings (22pt), `#FFFFFF`
- Translucent black blur on scroll

### Input Fields

**Message Composer**
- Background: `#1A1A1A`, corner radius 22pt, min height 44pt
- Placeholder: "Send a message", 16pt w400 `#999999`
- Leading photo/camera icon (22pt `#999999`), trailing send arrow
- Send arrow: `#999999` when empty → `#FFDE00` filled circle when text present
- Focused: 1pt `#FFDE00` ring (subtle)

**Search Field**
- Background: `#1A1A1A`, height 44pt, corner radius 12pt
- Leading `magnifyingglass` 18pt `#999999`
- Placeholder: "Search", 16pt w400 `#999999`
- Focused: background `#222222`, 1pt `#FFDE00` ring

### Distinctive Components

**Proximity Thumbnail Cascade**
- The defining surface: an edge-to-edge wall of square photos, sorted nearest-first, 3 columns, 2pt gutter, 0pt radius
- Infinite scroll, virtualized; new tiles decode and fade in (180ms opacity) as you reach them
- Each tile fuses a photo + a presence dot + a scrimmed name/distance overlay into one tap target

**Online Presence Dot**
- 10pt circle, `#4CD964`, 2pt `#000000` ring, top-right of every tile and avatar
- Solid = online now; absent = offline (no gray dot — absence is the offline state)

**Distance Scrim Label**
- The bottom ~40% of each tile carries a `rgba(0,0,0,0.65)` gradient so the 12pt bold distance and 15pt bold name read over any photo without a solid bar

**Tap Send Animation**
- Pressing "Tap" triggers: button scale 0.92 → 1.0 spring, a brief yellow flame pulse radiating from the button, success haptic, then the button settles into the "Tapped" outline state

**Lazy-Load Fade**
- As the cascade scrolls, each newly visible thumbnail transitions opacity 0 → 1 over 180ms with a subtle 1.02 → 1.0 scale settle — the only ambient motion on the grid

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 2, 4, 8, 12, 16, 20, 24, 32, 44, 56
- Grid gutter: 2pt (tiles nearly touch — density is the point)
- List margin: 16pt horizontal; chat margin: 12pt horizontal
- Section spacing: 20pt between shelves in the Taps / Favorites views

### Grid & Container
- Cascade: 3-column edge-to-edge, 2pt gutter, no outer margin, square tiles
- Lists (Messages / Taps): full width, 16pt horizontal content inset
- Chat: bubbles max 76% width, aligned left (incoming) / right (outgoing)
- Profile carousel: full-bleed device width

### Whitespace Philosophy
- **Maximum density on the grid**: 2pt gutters and 0pt radius pack the most faces per screen — proximity browsing is the core loop
- **Breathing room in chat and profile**: bubbles and the profile body get generous 12–16pt padding so reading is comfortable
- **Photos own the screen**: chrome is thin and translucent; the imagery is never boxed or letterboxed

### Border Radius Scale
- Square (0pt): Cascade grid tiles (intentional wall-of-photos density)
- Standard (12pt): Search field, settings cards
- Comfortable (18pt): Chat bubbles
- Composer (22pt): Message input field
- Sheet (16pt): Bottom sheets / modals (top corners)
- Full Pill (500pt): Primary CTAs, filter chips
- Circle (50%): Avatars, presence dot, Tap button, send arrow

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Cascade tiles, list rows, canvas |
| Tile Press (Level 1) | Inner dim `rgba(0,0,0,0.25)` + scale 0.97 | Pressed grid tile |
| Sheet (Level 2) | `rgba(0,0,0,0.7) 0 -16px 40px` | Profile sheet, action sheets |
| Composer Float (Level 3) | `rgba(0,0,0,0.5) 0 -8px 24px` | Chat composer above the keyboard |
| Tab Bar Material | `.regularMaterial` over `rgba(0,0,0,0.94)` | Bottom tab bar over the scrolling grid |
| Scrim | `rgba(0,0,0,0.65)` gradient (per-tile) / `rgba(0,0,0,0.6)` flat (modal) | Tile text legibility / modal dim |

**Shadow Philosophy**: On a true-black canvas, traditional drop shadows are invisible, so depth is communicated through *scrims and motion* rather than shadow. The per-tile bottom gradient is the most important "elevation" device — it lifts text off the photo. Sheets use a large, very dark top shadow only to separate them from the grid behind. Everything on the grid itself is flat; the photos carry all the visual weight.

### Motion
- **Tap button**: scale 0.92 → 1.0 spring (damping 0.7), yellow flame pulse, `.notificationOccurred(.success)` haptic
- **Cascade lazy-load**: per-tile opacity 0 → 1 + scale 1.02 → 1.0 over 180ms as tiles enter the viewport
- **Profile sheet present**: shared-element photo expands from tile → hero, 0.3s spring, canvas fades to black
- **Photo carousel**: 60fps horizontal paging with dot indicator, subtle parallax on the hero
- **Message send**: outgoing bubble scales in from the composer 0.9 → 1.0 over 200ms, send arrow flashes
- **Tab switch**: instant; active glyph fills + recolors to `#FFDE00`
- **Pull-to-refresh**: minimal yellow spinner, 0.4s, refreshes the cascade order

## 7. Do's and Don'ts

### Do
- Use pure black `#000000` as the canvas — the photo grid needs a true-black frame
- Reserve Grindr Yellow (`#FFDE00`) for the Tap action, active tab, badges, and the mask logo
- Use black text/icons (`#000000`) on yellow — it is intentional for maximum contrast
- Build the cascade as a 3-column edge-to-edge square grid with a 2pt gutter and 0pt tile radius
- Overlay name + distance on a `rgba(0,0,0,0.65)` bottom scrim so text reads over any photo
- Use the green dot (`#4CD964`) for online presence; absence of the dot means offline
- Make overlay text weight 700 — never thin or unprotected over imagery
- Fade thumbnails in (180ms) as they decode while scrolling the cascade
- Use Inter (or the Grindr grotesque) at weights 400/700 only

### Don't
- Don't use a softened off-black canvas — Grindr is true `#000000`, the photos demand it
- Don't add accent colors beyond yellow + the green presence dot — restraint makes yellow urgent
- Don't round the cascade tiles — the square, near-touching grid is the brand
- Don't place light or unscrimmed text over a profile photo — always scrim + bold
- Don't show a gray "offline" dot — offline is simply the absence of the green dot
- Don't use semibold or thin weights — the ramp is 400/700 only
- Don't box or letterbox profile photos — they go full-bleed
- Don't use yellow as a text color outside the active tab label — yellow is a fill
- Don't animate the grid aggressively — the only ambient motion is the lazy-load fade

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 3-col cascade (~124pt tiles); profile hero ~420pt |
| iPhone 13/14/15 | 390pt | Standard 3-col cascade (~129pt tiles) |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in the top bar |
| iPhone 15/16 Pro Max | 430pt | Optional 4-col cascade toggle; hero ~520pt |
| iPad | 768pt+ | 5–6 col cascade; profile opens as a side panel, not a full sheet |

### Dynamic Type
- Chat text, message-list names, profile body: full scale
- Grid overlay name + distance: pinned (layout-sensitive over photos)
- Tab labels: fixed 10pt
- Profile hero name: scales up to 28pt before clamping

### Orientation
- Cascade / Messages / Profile: **portrait-locked** (the grid is tuned for portrait density)
- Full-screen photo viewer: **rotates** to fit landscape images
- iPad: **all orientations**, cascade recolumns fluidly

### Touch Targets
- Cascade tile: full tile tappable (~129pt) — the entire photo is the target
- Tap button: 56pt circle — large and central on the profile
- List rows: 72pt height, full-row tappable
- Tab bar icons: 26pt glyph, 44pt effective hit
- Send arrow / icon actions: 24pt glyph, 44pt hit area

### Safe Area Handling
- Top: top bar + filter chips respect safe area and Dynamic Island
- Bottom: tab bar respects the home indicator; chat composer floats above the keyboard
- Cascade: goes edge-to-edge horizontally (intentional), respects top/bottom safe area
- Profile hero: extends under the status bar with a top scrim for control legibility

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#000000`
- Surface 1 (cards/tabs): `#1A1A1A`
- Surface 2 (sheets/incoming bubble): `#222222`
- Divider: `#2C2C2C`
- Text primary: `#FFFFFF`
- Text secondary: `#999999`
- Text tertiary: `#5C5C5C`
- Grindr Yellow (action): `#FFDE00`
- Yellow pressed: `#E6C700`
- Online green (presence): `#4CD964`
- Error red: `#FF3B30`

### Example Component Prompts
- "Create a SwiftUI Grindr cascade tile: square, full-bleed cover-cropped profile photo, 0pt corner radius. Bottom 40% has a linear gradient scrim from clear to `rgba(0,0,0,0.65)`. Overlaid bottom-left: name 'Alex' in Inter 15pt weight 700 `#FFFFFF` and distance '220 ft' in 12pt weight 700 `#FFFFFF` +0.2pt tracking. Top-right: a 10pt `#4CD964` online dot with a 2pt `#000000` ring. Pressed: scale 0.97 with a subtle dim."
- "Build the yellow Tap button: 56pt circular button, `#FFDE00` background, a flame/tap glyph 24pt in `#000000` (intentionally black on yellow). Pressed: scale 0.92 with a success haptic, background `#E6C700`, then a brief yellow flame pulse and settle into a transparent 'Tapped' outline state with a 1pt `#999999` border."
- "Design the cascade grid: 3-column edge-to-edge `LazyVGrid`, 2pt gutter, no outer margin, square tiles. Each tile fades in over 180ms (opacity 0→1, scale 1.02→1.0) as it enters the viewport. Background `#000000`."
- "Create a Grindr chat screen: incoming bubbles `#222222` text `#FFFFFF`, outgoing bubbles `#FFDE00` text `#000000`, both 18pt corner radius (4pt on the tail corner), max width 76%. Composer: `#1A1A1A` rounded 22pt field, placeholder 'Send a message' 16pt `#999999`, trailing send arrow that is `#999999` when empty and a filled `#FFDE00` circle with a black arrow when text is present."
- "Build a Grindr message list row: 72pt height, 52pt circular avatar with a top-right `#4CD964` online dot, name 'Jordan' 16pt weight 700 `#FFFFFF`, last-message preview 13pt weight 400 `#999999` (1-line), trailing timestamp 12pt `#999999` and an 8pt `#FFDE00` unread dot. Pressed background `#1A1A1A`."

### Iteration Guide
1. Canvas is true black `#000000` — the photo grid requires it, do not soften it
2. Grindr Yellow `#FFDE00` only for the Tap action, active tab, badges, and mask logo
3. Black text/icons on yellow — intentional max-contrast choice (`#000000` on `#FFDE00`)
4. The cascade is a 3-column edge-to-edge square grid, 2pt gutter, 0pt radius — keep it dense
5. Always scrim overlay text (`rgba(0,0,0,0.65)` bottom gradient) and set it weight 700
6. Online presence is a `#4CD964` dot; offline is the absence of the dot — never a gray dot
7. Typography: Inter (or the Grindr grotesque), weights 400/700 only — no semibold, no thin
8. Depth on black comes from scrims and motion, not drop shadows — lazy-load fade is the only ambient motion
