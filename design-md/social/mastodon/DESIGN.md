# Design System Inspiration of Mastodon (iOS)

## 1. Visual Theme & Atmosphere

Mastodon's iOS app is a calm, readable, content-first timeline that feels closer to a well-set magazine than to an algorithmic firehose. The default surface is pure white (`#FFFFFF`) with near-black text (`#000000`), and the dark variant inverts to a deep blue-gray (`#191B22`) rather than a true black — the subtle blue cast is part of Mastodon's identity, echoing its purple wordmark. The interface trusts the reader: generous line height, comfortable left-aligned text, and toot cards separated by hairline dividers rather than heavy boxes. There is no infinite-scroll dopamine engineering here; the chronological timeline and the federated-timeline switcher are front and center.

The accent system is anchored by Mastodon Purple (`#6364FF`) — a friendly indigo used for links, the active tab, the compose FAB, and primary buttons. Its pressed state deepens to `#563ACC`. The one other chromatic moment is the boost: when you reblog a toot, the boost icon turns a confident green (`#2DCE89`) and performs a satisfying 360° spin. Favourites are a warm star, replies and shares stay neutral. This restrained two-color logic (purple for navigation/identity, green for the boost) keeps the timeline legible while giving the most social action — amplification — a distinct, joyful signature.

Typography is the system font (San Francisco) used at comfortable reading sizes, mapped onto a fallback of Inter / Roboto for cross-platform parity. Toot bodies sit at 16pt with 1.45 line height for sustained reading; display names are 16pt semibold; the federated `@user@instance.social` handle is 14pt in muted gray, always visible because the instance *is* the identity in a federated network. The most distinctive type-adjacent component is the content-warning spoiler: a collapsed card with a one-line warning and a "SHOW MORE" button that expands the hidden body inline — a structural commitment to consent-first reading.

**Key Characteristics:**
- White canvas (`#FFFFFF`) / blue-tinted dark (`#191B22`) — never true black
- Mastodon Purple (`#6364FF`) for links, active tab, compose FAB, primary actions
- Boost green (`#2DCE89`) with a 360° spin — the signature amplification gesture
- Federated `@user@instance` handle always shown — identity is the instance
- Content-warning spoiler cards — collapsed by default, "SHOW MORE" expands inline
- Federated timeline switcher: Home / Local / Federated segmented at the top
- Toot card action row: Reply · Boost · Favourite · Share, evenly spaced
- System font at reading sizes (16pt body, 1.45 line height), Inter/Roboto fallback
- Bottom tabs (Home / Search / Notifications / Profile) + a floating compose FAB

## 2. Color Palette & Roles

### Primary
- **Mastodon Purple** (`#6364FF`): Links, active tab tint, compose FAB, primary button fill, mention/hashtag text.
- **Purple Pressed** (`#563ACC`): Active/pressed state for purple CTAs and the FAB.
- **Boost Green** (`#2DCE89`): Boost icon active state, "boosted by" attribution, the spin gesture.

### Light Mode (Default)
- **Canvas** (`#FFFFFF`): Primary background, timeline surface.
- **Surface 1** (`#FFFFFF`): Toot cards (flat on canvas, separated by dividers).
- **Surface 2** (`#F2F3F8`): Grouped settings rows, search field, pressed list rows.
- **Divider** (`#C0CDD9`): Hairline separators between toots and list rows.
- **Text Primary** (`#000000`): Toot body, display names, headlines.
- **Text Secondary** (`#606984`): `@user@instance` handles, timestamps, metadata, counts.
- **Text Tertiary** (`#8B95A8`): Disabled labels, very low-emphasis hints.

### Dark Mode
- **Dark Canvas** (`#191B22`): Primary background — deep blue-gray, NOT pure black.
- **Dark Surface 1** (`#282C37`): Toot cards, elevated rows.
- **Dark Surface 2** (`#313543`): Search field, sheets, pressed rows.
- **Dark Divider** (`#393F4F`): Hairline separators in dark mode.
- **Dark Text Primary** (`#FFFFFF`): Toot body, names.
- **Dark Text Secondary** (`#9BAEC8`): Handles, timestamps, metadata.

### Semantic
- **Favourite Gold** (`#CA8F04`): Active favourite (star) state.
- **Error Red** (`#DF405A`): Failed posts, destructive confirms, validation.
- **Success Green** (`#2DCE89`): Same as boost green — posted/confirmed states.
- **Verified Purple** (`#6364FF`): Verified-link checkmark on profiles.

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro Text` / `SF Pro Display` (system) on iOS
- **Cross-platform parity**: `Inter` (web/Android reference), then `Roboto`
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'Inter', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Monospace (code toots)**: `ui-monospace, 'SF Mono', Menlo, monospace`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | SF Pro Display | 28pt | 700 | 1.2 | -0.4pt | "Notifications", "Explore" large nav title |
| Section Header | SF Pro Display | 22pt | 700 | 1.2 | -0.3pt | "Trending now", profile section heads |
| Display Name | SF Pro Text | 16pt | 600 | 1.25 | -0.2pt | Poster's display name on a toot card |
| Toot Body | SF Pro Text | 16pt | 400 | 1.45 | 0pt | The status text — tuned for sustained reading |
| Body (Settings) | SF Pro Text | 15pt | 400 | 1.4 | 0pt | Settings descriptions, about text |
| Handle / Meta | SF Pro Text | 14pt | 400 | 1.3 | 0pt | `@user@instance.social`, "2h", "via Web" |
| CW Warning | SF Pro Text | 15pt | 600 | 1.35 | 0pt | The content-warning line on a spoiler card |
| Action Count | SF Pro Text | 13pt | 500 | 1.2 | 0pt | Reply/boost/favourite numeric counts |
| Button (Primary) | SF Pro Text | 16pt | 600 | 1.0 | -0.2pt | "Publish", "Follow" |
| Button (Show More) | SF Pro Text | 14pt | 700 | 1.0 | 0.2pt | "SHOW MORE" on CW cards |
| Tab Label | SF Pro Text | 10pt | 600 | 1.0 | 0.1pt | Bottom tab bar labels |
| Timestamp Inline | SF Pro Text | 13pt | 400 | 1.2 | 0pt | Trailing time on a toot header |

### Principles
- **Reading-first body**: 16pt at 1.45 line height — Mastodon optimizes for paragraphs, not glances
- **Handle is always visible**: the `@user@instance` is a 14pt structural element, never truncated away
- **Weight at 400 / 500 / 600 / 700**: regular body, medium counts, semibold names, bold "SHOW MORE"
- **System font, Inter fallback**: keeps iOS native feel while matching the web client's Inter rendering
- **Purple text only for links/mentions/hashtags** — body text never uses the accent

## 4. Component Stylings

### Buttons

**Primary Button ("Publish" / "Follow")**
- Background: `#6364FF`
- Text: `#FFFFFF`
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: 8pt (Mastodon uses soft rectangles, not full pills, for primary buttons)
- Font: SF Pro Text 16pt weight 600
- Pressed: `#563ACC`, scale 0.98
- Disabled: `#6364FF` at 40% opacity

**Secondary / Following Button**
- Background: transparent
- Text: `#6364FF` (light) / `#9BAEC8` (dark)
- Border: 1pt solid `#C0CDD9` (light) / `#393F4F` (dark)
- Padding: 10pt vertical, 20pt horizontal
- Corner radius: 8pt
- Pressed: background `#F2F3F8`

**Compose FAB**
- Shape: Circle, 56pt diameter, bottom-right, 16pt from edges (floats above tab bar)
- Background: `#6364FF`
- Icon: SF Symbol `square.and.pencil` 24pt in `#FFFFFF`
- Shadow: `rgba(99,100,255,0.35) 0 6px 20px`
- Pressed: scale 0.94, `#563ACC`, soft haptic

**Toot Action Icons (Reply / Boost / Favourite / Share)**
- Size: 18pt glyph, 44pt hit area
- Default: `#606984` (light) / `#9BAEC8` (dark)
- Boost active: `#2DCE89` (with 360° spin on activation)
- Favourite active: `#CA8F04` (gold, fill + scale bounce)
- Spacing: evenly distributed across the card's full content width
- Count: 13pt w500 trailing each icon when > 0

### Cards & Containers

**Toot Card (THE core unit)**
- Background: `#FFFFFF` (light) / `#282C37` (dark) — flat, no shadow
- Separator: 1pt `#C0CDD9` divider below each card (cards touch)
- Padding: 16pt all sides
- Header: 46pt rounded-square avatar (8pt radius) → display name (16pt w600) + `@user@instance` (14pt `#606984`) → trailing timestamp (13pt) + ⋯
- Body: 16pt w400, 1.45 line height, links/mentions/hashtags in `#6364FF`
- Media: rounded 12pt corner image grid (1–4 attachments)
- Action row: Reply · Boost · Favourite · Share evenly spaced, 12pt above bottom padding
- "Boosted by Name" attribution row above the header in 13pt `#606984` with a small green boost glyph when applicable

**Content-Warning Spoiler Card**
- Collapsed: warning text (15pt w600) + a "SHOW MORE" button (14pt w700 uppercase) on a `#F2F3F8` (light) / `#313543` (dark) inset block, 8pt radius
- Expanded: body reveals inline below the warning with a 0.25s height/opacity transition; button label flips to "SHOW LESS"
- The warning row keeps a small `eye.slash` glyph leading the text

**Profile Header**
- Banner image full-width, 150pt tall
- Avatar 86pt rounded-square (12pt radius), overlapping the banner by 40pt, 4pt canvas-colored ring
- Display name 22pt w700, `@user@instance` 15pt `#606984`
- Bio with clickable mentions/hashtags in purple
- Fields table (verified links show a green/purple check)
- "Follow" primary button + bell + ⋯

**Notification Row**
- 46pt avatar + bold actor name + action verb ("boosted your post", "followed you")
- A small tinted glyph indicates the type: green boost arrows, gold favourite star, purple follow person
- Tap opens the related toot or profile

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(255,255,255,0.94)` (light) / `rgba(25,27,34,0.94)` (dark) with `.regularMaterial` blur
- Tabs: Home, Search, Notifications, Profile
- Icon size: 26pt
- Active: `#6364FF`; Inactive: `#606984` (light) / `#9BAEC8` (dark)
- Labels: SF Pro Text 10pt w600
- Notifications tab shows a purple unread dot badge

**Federated Timeline Switcher**
- A segmented control pinned under the nav on the Home tab: **Home · Local · Federated**
- Selected segment: `#6364FF` text with a 2pt purple underline indicator
- Unselected: `#606984`
- Switching slides the timeline with a 0.25s cross-fade + horizontal nudge

**Top Nav**
- Height: 44pt + safe area
- Large title (28pt w700) collapsing to inline on scroll
- Trailing settings/filter icon (22pt)
- Blur-on-scroll background

### Input Fields

**Search Field**
- Background: `#F2F3F8` (light) / `#313543` (dark)
- Height: 40pt, corner radius 10pt
- Leading `magnifyingglass` 16pt `#606984`
- Placeholder: "Search Mastodon", 15pt w400 `#606984`
- Focus: 1.5pt `#6364FF` ring

**Compose Editor**
- Full-screen sheet, plain text area at 18pt w400 for comfortable writing
- Character ring counter (500 default) top-right that turns `#DF405A` past the limit
- CW toggle (`eye.slash`), media, poll, visibility, language chips in a toolbar above the keyboard
- Visibility selector: Public / Unlisted / Followers / Mentioned (globe/lock glyphs)

### Distinctive Components

**Content-Warning Spoiler Reveal**
- The signature consent-first pattern: hidden body, one-line warning, "SHOW MORE"
- Expansion animates height + opacity over 0.25s ease-out; collapse is symmetric
- Persists per-toot in the session (re-collapses on relaunch)

**Boost-with-Spin**
- Tapping boost flips the arrows to `#2DCE89` and spins the icon 360° over 0.4s
- Un-boosting spins back -360° and returns to neutral
- Paired with a success haptic; the count increments with a tiny vertical roll

**Federated Timeline Switch**
- The Home/Local/Federated control reframes the entire feed — a core Mastodon mental model surfaced as a first-class switcher, not buried in settings

**Reply-Controls Chip**
- A small chip on the composer indicating who can reply (Everyone / Followers / Mentioned), echoing the visibility model — purple when restricted

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56
- Standard margin: 16pt horizontal on toot cards
- Toot vertical rhythm: cards touch; a 1pt divider provides separation

### Grid & Container
- Content width: full device width, 16pt internal card padding
- Media grid: 1 image full-width 12pt radius; 2–4 images in a 2-col 12pt-radius mosaic with 2pt gaps
- Profile fields: 2-column key/value table

### Whitespace Philosophy
- **Reading comfort over density**: 16pt body at 1.45 line height; cards breathe internally
- **Dividers, not boxes**: separation is a hairline, keeping the timeline visually quiet
- **Action row spaced wide**: the four actions span the full card width so each has a generous target

### Border Radius Scale
- Square (0pt): Rare — full-bleed banners only
- Soft (8pt): Buttons, avatars (rounded-square), CW inset blocks, search field-ish
- Comfortable (10–12pt): Search field, media attachments
- Large (16pt): Bottom sheets, modals
- Circle (50%): Compose FAB, notification-type glyph chips, boost/favourite hit circles
- Note: Mastodon avatars are **rounded-squares (8–12pt)**, not circles — a deliberate identity choice

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Toot cards, timeline, list rows |
| Card Hairline (Level 1) | 1pt divider `#C0CDD9` / `#393F4F` | Toot-to-toot separation |
| FAB (Level 2) | `rgba(99,100,255,0.35) 0 6px 20px` | Floating compose button |
| Sheet (Level 3) | `rgba(0,0,0,0.15) 0 -12px 40px` (light) / `rgba(0,0,0,0.5)` (dark) | Compose sheet, context menus |
| Tab Bar Material | `.regularMaterial` over 94%-opaque canvas | Bottom tab bar over scrolling content |
| Blur Overlay | scrim `rgba(0,0,0,0.4)` + backdrop-blur 30 | Image lightbox, modal dim |

**Shadow Philosophy**: Mastodon is almost entirely flat — the timeline reads like a document. The only true elevation is the purple compose FAB (a colored, brand-tinted shadow) and modal sheets. Cards never lift; separation comes from hairline dividers. In dark mode shadows deepen to 0.5 opacity to register on the blue-gray canvas, while in light mode they stay barely-there at 0.15.

### Motion
- **Boost spin**: icon rotates 360° over 0.4s ease-out, color → `#2DCE89`, success haptic
- **Favourite bounce**: star fills + scales 1.0 → 1.2 → 1.0 over 300ms, gold `#CA8F04`
- **CW expand/collapse**: height + opacity over 0.25s ease-out, symmetric
- **Timeline switch**: 0.25s cross-fade + 8pt horizontal nudge between Home/Local/Federated
- **Compose FAB**: scale 0.94 on press, soft haptic; sheet presents with a 0.35s spring
- **Pull-to-refresh**: standard system spinner, no custom flourish

## 7. Do's and Don'ts

### Do
- Use white `#FFFFFF` light / blue-gray `#191B22` dark — never true black for the dark canvas
- Reserve Mastodon Purple (`#6364FF`) for links, mentions, hashtags, the active tab, FAB, and primary buttons
- Make the boost a 360° spin to `#2DCE89` — it's the signature amplification moment
- Always show the full `@user@instance` handle — identity is federated
- Default content-warning bodies to collapsed with a "SHOW MORE" reveal
- Render avatars as rounded-squares (8–12pt), not circles
- Surface the Home/Local/Federated switcher prominently on the timeline
- Keep cards flat and separate them with hairline dividers, not shadows

### Don't
- Don't use pure black for dark mode — the blue cast (`#191B22`) is Mastodon's identity
- Don't add accent colors beyond purple and boost-green — the timeline must stay legible
- Don't truncate or hide the instance part of a handle
- Don't auto-expand content warnings — consent-first means collapsed by default
- Don't make avatars circular — Mastodon uses rounded-squares deliberately
- Don't box every toot with borders/shadows — dividers keep it quiet and readable
- Don't shrink body text below 16pt — this is a reading app
- Don't animate aggressively — the only flourish is the boost spin and favourite bounce

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Avatar 42pt; media grid still 2-col |
| iPhone 13/14/15 | 390pt | Standard 46pt avatar, 16pt card padding |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the large title |
| iPhone 15/16 Pro Max | 430pt | Body line measure widens; FAB stays 56pt |
| iPad | 768pt+ | Multi-column: timeline + detail split view, max 680pt reading column |

### Dynamic Type
- Toot body, display names, bios: full scale (this is a reading app — honor it generously)
- Action counts, timestamps: scale modestly
- Tab labels: fixed 10pt
- "SHOW MORE" button: fixed 14pt

### Orientation
- Timeline / Profile: portrait and landscape (iPad supports split view)
- Image lightbox: rotates to landscape for wide media
- Compose: portrait-locked on iPhone

### Touch Targets
- Toot action icons: 18pt glyph, 44pt hit circle
- Compose FAB: 56pt
- Tab icons: 26pt glyph, 44pt effective hit
- "SHOW MORE": full warning-block width is tappable

### Safe Area Handling
- Top: large title and timeline switcher sit below safe area / Dynamic Island
- Bottom: FAB floats 16pt above the tab bar; both respect the home indicator
- Sides: 16pt content insets, banner goes edge-to-edge

## 9. Agent Prompt Guide

### Quick Color Reference
- Light canvas: `#FFFFFF` · Dark canvas: `#191B22`
- Light surface 2: `#F2F3F8` · Dark surface 1: `#282C37`
- Divider: `#C0CDD9` (light) / `#393F4F` (dark)
- Text primary: `#000000` (light) / `#FFFFFF` (dark)
- Text secondary: `#606984` (light) / `#9BAEC8` (dark)
- Mastodon Purple (action/link): `#6364FF`
- Purple pressed: `#563ACC`
- Boost green: `#2DCE89`
- Favourite gold: `#CA8F04`
- Error red: `#DF405A`

### Example Component Prompts
- "Create a SwiftUI Mastodon toot card: 46pt rounded-square avatar (8pt radius) leading, display name 'Eugen' in SF Pro 16pt weight 600 and handle '@Gargron@mastodon.social' in 14pt #606984 below it, trailing '2h' timestamp 13pt and a ⋯ menu. Body text 16pt weight 400 at 1.45 line height with links/mentions/hashtags in #6364FF. Below: a Reply · Boost · Favourite · Share row, icons 18pt, evenly spaced across the full card width, with 13pt weight-500 counts. Flat #FFFFFF card with a 1pt #C0CDD9 divider beneath."
- "Build the Mastodon boost action: an 18pt boost-arrows icon, default #606984; on tap it turns #2DCE89 and spins 360° over 0.4s ease-out with a success haptic, and the trailing count rolls up by one. Tapping again spins -360° back to neutral."
- "Design a Mastodon content-warning spoiler card: collapsed by default showing an eye.slash glyph + warning text 15pt weight 600 and a 'SHOW MORE' button 14pt weight 700 uppercase on a #F2F3F8 inset block with 8pt radius. On tap the hidden body expands inline with a 0.25s height+opacity ease-out and the label flips to 'SHOW LESS'."
- "Create the Mastodon federated timeline switcher: a segmented control 'Home · Local · Federated' pinned under the nav; selected segment uses #6364FF text with a 2pt purple underline, unselected is #606984. Switching cross-fades the feed over 0.25s with an 8pt horizontal nudge."
- "Build the Mastodon compose FAB: a 56pt circular button, #6364FF, bottom-right 16pt from the edges, SF Symbol 'square.and.pencil' 24pt #FFFFFF, shadow rgba(99,100,255,0.35) 0 6px 20px. Pressed: scale 0.94, color #563ACC, soft haptic; opens the compose sheet with a 0.35s spring."

### Iteration Guide
1. Light canvas `#FFFFFF`; dark canvas is blue-gray `#191B22` — never pure black
2. Purple `#6364FF` is links + navigation + primary actions; boost-green `#2DCE89` is the one other accent
3. The boost is a 360° spin to green — make it the most joyful gesture in the app
4. Always render the full `@user@instance` handle — federation is the identity model
5. Content warnings collapse by default with "SHOW MORE" — consent-first reading
6. Avatars are rounded-squares (8–12pt), not circles
7. Cards are flat; separate with 1pt dividers, not shadows — the timeline is a document
8. Body text is 16pt at 1.45 line height — never compromise readability for density
