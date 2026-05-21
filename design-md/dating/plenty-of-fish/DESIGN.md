# Design System Inspiration of Plenty of Fish (iOS)

## 1. Visual Theme & Atmosphere

Plenty of Fish (POF) is one of the original mass-market dating apps, and its iOS design system reflects that heritage: approachable, high-density, and unmistakably **blue**. Where newer dating apps lean on swipe-deck spectacle, POF is grid-first and conversation-first — it shows you *many* people at once and pushes you toward messaging quickly. The atmosphere is friendly and busy rather than minimal: rounded cards, heavy rounded type, full-color profile photography edge-to-edge, and POF Blue (`#0098DB → #00A6E2`) as the single, dominant brand color on every primary action. The default app is light (`#FFFFFF` / `#F4F5F7`); this guide also specifies a cool near-black dark mode (`#121417`).

The signature surface — and the screen this guide treats as the hero — is the **match grid**: a 2-column scrolling grid of portrait profile cards. Each card is a full-bleed photo with a bottom gradient scrim carrying the person's name + age, distance and last-active ("Sofia, 27 · 3 mi · Active now"), an "Online" pill top-left (teal dot), and a circular blue **like** button bottom-right. Above the grid sits a logo + actions bar and a segmented strip (Matches / Meet Me / Search). The grid is paired with **Meet Me** — POF's lightweight yes/maybe/no game: one large profile card at a time with three round buttons (✕ no, ? maybe, ♥ yes), the yes button in POF Blue. Together the grid and Meet Me are the heart of POF's "see lots of people, decide fast, then chat" model.

Navigation is a 5-slot bottom tab bar — Matches, Meet Me, Search, Messages (with a pink unread badge), Me — with the active tab in POF Blue. Messaging is a first-class destination (POF historically lets you message before matching), so the Messages tab and its badge are prominent. The brand typeface direction is a rounded, friendly, heavy sans; this guide uses **Nunito Sans** (free, SIL OFL) — warm, rounded, with strong 800/900 weights for names and section titles, matching POF's approachable, unintimidating personality. Color discipline: POF Blue for *brand + primary action*, teal for *online/presence*, pink for *unread/notification*, gold for *upgrade*, and full-color photography for *content*; chrome stays neutral.

**Key Characteristics:**
- Light (`#FFFFFF`) default / cool near-black (`#121417`) dark — friendly and busy, never pure black
- POF Blue (`#0098DB → #00A6E2`) is *the* brand color — logo, primary CTA, active tab, the like button
- Match grid — 2-column scrolling grid of full-bleed portrait photo cards with gradient name/meta scrims
- Meet Me — one big profile card + three round actions (✕ no / ? maybe / ♥ yes), yes in POF Blue
- "Online" presence pill — teal dot (`#00C9B7`) + "Online", top-left of cards; first-class presence
- Messaging is a primary destination — Messages tab with a pink (`#FF4F8B`) unread badge
- 5-slot bottom tab bar — Matches / Meet Me / Search / Messages / Me, active in POF Blue
- Rounded everything — 16–20pt photo cards, 999pt pill buttons, circular action buttons
- Heavy rounded type (Nunito Sans) — 800/900 weights for names and titles; approachable, unintimidating
- Distance + last-active everywhere — "3 mi · Active now" is core profile metadata, not decoration
- Full-color photography edge-to-edge — content is loud; chrome is neutral and recedes

## 2. Color Palette & Roles

### Primary (Interactive)
- **POF Blue** (`#0098DB`): The brand anchor — logo, primary CTA, active tab, the circular like button, links.
- **POF Blue Light** (`#00A6E2`): Gradient end (`#0098DB → #00A6E2`), highlights, selected states.
- **POF Blue Gradient** (`#0098DB → #00A6E2`, 135°): Primary buttons, the Meet Me "yes" button, key headers.
- **POF Blue Pressed** (`#0079B0`): Pressed state for blue buttons (apply via 0.93 brightness).

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light background, cards, sheets.
- **Background Grey** (`#F4F5F7`): App background behind cards and grid.
- **Surface Raised** (`#FFFFFF`): Cards, Meet Me card, sheets (with soft shadow).
- **Surface Pressed** (`#E9EBEF`): Pressed rows / chips.
- **Divider** (`#E4E6EA`): Hairlines, segmented-strip underline track.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#121417`): Primary dark background — cool near-black, NOT pure black.
- **Dark Surface 1** (`#1A1D21`): Cards, Meet Me card, panels.
- **Dark Surface 2** (`#23272C`): Search field, raised chips, hovered rows.
- **Dark Divider** (`#2C3036`): Hairlines and the tab-bar top border.

### Text
- **Text Primary Light** (`#16181C`): Names, headlines, body on light.
- **Text Secondary Light** (`#6B7079`): Distance, last-active, captions, placeholder.
- **Text Tertiary Light** (`#9BA1AB`): Disabled, inactive tab labels.
- **Text Primary Dark** (`#EDEFF2`): Names, headlines, body on dark.
- **Text Secondary Dark** (`#9BA1AB`): Distance/meta on dark.
- **Text Tertiary Dark** (`#6B7079`): Disabled, inactive tab labels on dark.
- **Text On Photo** (`#FFFFFF`): Name/meta over a card's bottom gradient scrim (always white).

### Accent / Status
| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Online / Presence (teal) | `#00C9B7` | `#00C9B7` |
| Unread / Notification (pink) | `#FF4F8B` | `#FF4F8B` |
| Upgrade / Premium (gold) | `#FFB23E` | `#FFB23E` |
| Like (blue) | `#0098DB` | `#0098DB` |
| Maybe (gold outline) | `#FFB23E` | `#FFB23E` |

### Semantic
- **Success Green** (`#20C997`): Match-made confirmation, profile-saved toast.
- **Error Red** (`#FF5267`): Block/report confirmation, send-failure banner.
- **Warning Gold** (`#FFB23E`): Reuses Upgrade Gold for quota/limit warnings.
- **Scrim Gradient** (`transparent → rgba(0,0,0,0.78)`): Bottom-of-card overlay for name/meta legibility.
- **Selection Tint** (`#0098DB1F`): ~12% POF Blue overlay on a selected row/chip.

## 3. Typography Rules

### Font Family
- **Primary**: POF's UI sans (a rounded, friendly, heavy grotesque). Free stand-in: **`Nunito Sans`** (by Vernon Adams / Jacques Le Bailly, SIL OFL) — warm, rounded, with strong 800/900 weights.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'SF Pro Display', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Weights used**: 400 (body), 600 (meta emphasis), 700 (card names / labels), 800 (section titles / buttons), 900 (display).

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Nunito Sans | 32pt | 900 | 1.15 | -0.6pt | Onboarding / large titles |
| Title | Nunito Sans | 26pt | 800 | 1.2 | -0.3pt | Screen titles ("Meet Me") |
| Section Title | Nunito Sans | 22pt | 800 | 1.25 | -0.2pt | "People near you" |
| Profile Name (detail) | Nunito Sans | 18pt | 800 | 1.3 | -0.1pt | Profile detail name + age |
| Body | Nunito Sans | 16pt | 400 | 1.5 | 0pt | Bio, message body, descriptions |
| Card Name | Nunito Sans | 15pt | 700 | 1.3 | 0pt | Name + age on grid card scrim |
| Button | Nunito Sans | 16pt | 800 | 1.0 | 0pt | Primary / secondary buttons |
| Meta | Nunito Sans | 14pt | 400 | 1.4 | 0pt | "Active now · 3 miles away" |
| Card Meta | Nunito Sans | 12pt | 600 | 1.35 | 0pt | Distance/last-active on card scrim |
| Chip / Status | Nunito Sans | 12pt | 700 | 1.3 | 0.1pt | "Online" pill, interest chips |
| Tab Label | Nunito Sans | 10pt | 800 | 1.0 | 0.1pt | Bottom tab labels |
| Badge | Nunito Sans | 9pt | 800 | 1.0 | 0.2pt | Unread count badge |
| Message Bubble | Nunito Sans | 15pt | 500 | 1.4 | 0pt | Chat bubble text |

### Principles
- **Friendly and unintimidating**: rounded heavy weights (800/900) make names and titles warm and confident, never corporate-thin — dating should feel inviting.
- **Weight concentrated at 400 / 600 / 700 / 800 / 900**: regular body, semibold meta, bold card names, extrabold titles/buttons, black display. No thin.
- **White on photos, always**: any text on a card photo is `#FFFFFF` over a bottom gradient scrim — never colored text on imagery.
- **Names lead**: the profile name+age is the loudest text on any card (700+) — people, not chrome, are the content.
- **Dynamic Type first-class**: display, titles, body, meta scale; card-scrim name/meta, tab labels, badges, status chips stay fixed (layout-sensitive over imagery).

## 4. Component Stylings

### Buttons

**Primary Button (Send Message / Continue)**
- Shape: pill, 999pt corner radius
- Background: linear gradient `#0098DB → #00A6E2` at 135°
- Text: `#FFFFFF`, Nunito Sans 16pt weight 800
- Padding: 14pt vertical, 28pt horizontal
- Pressed: brightness 0.93 + scale 0.98

**Secondary Solid Button (Like)**
- Background: solid `#0098DB` (POF Blue)
- Text: `#FFFFFF`, 15pt weight 800
- Padding: 12pt vertical, 22pt horizontal
- Corner radius: 999pt (pill)
- Pressed: background `#0079B0`

**Outline Button (View Profile)**
- Background: transparent
- Text: `#0098DB`, 15pt weight 700
- Border: 1.5pt `#0098DB`
- Corner radius: 999pt (pill)
- Padding: 11pt vertical, 20pt horizontal
- Pressed: background `#0098DB14` (8% blue)

**Upgrade Button (Upgrade / Get Premium)**
- Background: `#FFB23E` (Upgrade Gold)
- Text: `#2A1B05`, 14pt weight 800
- Corner radius: 999pt (pill)
- Padding: 10pt vertical, 18pt horizontal
- Often paired with a small crown/diamond glyph

**Text Button (Skip / Cancel)**
- Font: Nunito Sans 15pt weight 700
- Color: `#0098DB`
- No background, no underline
- Pressed: opacity 0.6

**Circular Action Buttons (Meet Me / card like)**
- Like (♥, primary): 56pt circle, `#0098DB` fill, white heart, shadow `rgba(0,152,219,0.5) 0 6px 16px`
- Maybe (?): 56pt circle, surface fill, 1.5pt `#FFB23E` border, gold glyph
- No (✕): 56pt circle, surface fill, 1.5pt divider border, secondary glyph
- Grid-card like: 36pt circle, `#0098DB`, white heart, smaller shadow
- Press: scale 0.9 + light haptic; on "yes" a brief heart pop

### Core Atoms

**Profile Grid Card**
- Aspect ratio 3:4, 16pt corner radius, full-bleed photo, no border
- Top-left "Online" pill (when present): `rgba(0,0,0,0.42)` bg, teal `#00C9B7` 7pt dot, white 12pt w700 "Online", 999pt radius
- Bottom gradient scrim `transparent → rgba(0,0,0,0.78)` carrying: name+age (white 15pt w700) and meta "3 mi · Active now" (white 12pt w600)
- Bottom-right 36pt circular blue like button
- Tap card → profile detail; tap heart → like (heart fills, light haptic)

**Meet Me Card**
- Surface card, 20pt corner radius, centered photo (1:1, 16pt radius), name+age (18pt w800), prompt "Would you like to meet {name}?" (13pt w700 secondary), then the three round buttons row
- Swipeable: drag left = no, right = yes, tap buttons equivalently

**"Online" / Presence Pill**
- Teal dot (`#00C9B7`) + label; on a card it's white text on a dark translucent pill; in a list it's teal text

**Distance / Last-Active Meta**
- "3 mi · Active now" — Nunito Sans 12–14pt w600 secondary; "Active now" may carry the teal dot

**Search / Filter Field**
- Height: 46pt, 12pt corner radius
- Background: `#F4F5F7` / `#23272C`
- Leading magnifier 18pt `#6B7079`
- Placeholder 15pt `#6B7079`; focus: 1.5pt `#0098DB` border

**Unread Badge**
- Pink `#FF4F8B` pill, white 9pt w800 count, min 16pt, on the Messages tab and conversation rows

**Avatar**
- 48pt circle in lists/chat; photo or POF-blue placeholder with white initial

### Navigation

**Bottom Tab Bar**
- Height: 70pt + safe area
- Background: `#FFFFFF` / `#121417` at 94% + blur, 0.5pt top border `#E4E6EA` / `#2C3036`
- 5 slots: Matches, Meet Me, Search, Messages (pink badge), Me
- Icon size: 23pt; active icon+label `#0098DB` (active icon filled); inactive `#9BA1AB`
- Labels: Nunito Sans 10pt w800, always shown
- Messages tab shows a pink unread badge top-right when count > 0

**Top Bar (Matches / grid)**
- Leading: POF logo + "POF" wordmark (Nunito Sans 20pt w900)
- Trailing: filter and search glyphs in `#0098DB`, 22pt
- Below: segmented strip — Matches / Meet Me / Search — active item w800 primary with a 3pt POF-Blue underline; inactive w800 tertiary

**Profile Detail**
- Full-bleed photo carousel with page dots, name+age overlay, scrollable bio + interests + distance, sticky bottom bar with a gradient "Send Message" pill and a circular like

### Input Fields

**Text Field (search, edit profile)**
- Height: 46pt, 12pt corner radius
- Background: `#F4F5F7` / `#23272C`, no border at rest
- Focus: 1.5pt `#0098DB` border
- Placeholder `#6B7079` 15pt; text primary 16pt

**Message Composer**
- Rounded 999pt pill input, leading `#23272C` field, trailing circular `#0098DB` send button (white arrow) when text present
- Photo/GIF affordances inside the field at 22pt `#6B7079`

### Distinctive Components

**Match Grid (signature)**
- The 2-column full-bleed photo grid with name/meta scrims and inline blue like buttons. POF's core "browse many at once" surface — content-dense, photo-forward.

**Meet Me (signature)**
- The yes/maybe/no card game. One big profile at a time; ✕ / ? / ♥ round buttons; ♥ in POF Blue with a glow and heart pop. The fastest decision loop in the app.

**"Online" Presence System**
- Teal-dot "Online" pill on cards + "Active now" meta — POF surfaces presence aggressively to drive timely conversations.

**Messages-First Inbox**
- A prominent Messages tab with pink unread badges; conversation rows show avatar, name, last message, time, and an online dot — messaging is a destination, not an afterthought.

**Upgrade Surface**
- Gold-accented sheets/cards ("Get Premium" / "See who likes you") with a gold CTA — the single place gold dominates.

**Profile Detail Carousel**
- Full-bleed swipeable photo carousel with page dots, overlaid name+age, then a scroll of bio/interest chips/distance and a sticky gradient message bar.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 10, 12, 14, 16, 20, 24, 28, 32, 40
- Grid outer inset: 14pt; card gutter: 10pt; 2 columns on iPhone
- Section vertical rhythm: 18–22pt before a header, 12pt header→content

### Grid & Container
- iPhone: 2-column match grid, 14pt insets, 10pt gutter
- iPhone Plus/Max: 2-column with larger cards (same gutter)
- iPad portrait: 3-column grid; Meet Me card centered max ~480pt wide
- iPad landscape: 4-column grid; profile detail in a centered sheet
- Profile detail photo: full-bleed, fixed 4:5 region with carousel dots

### Whitespace Philosophy
- **Photo-forward density**: cards are tightly gridded (10pt gutters) — busy but rhythmic; photography fills the screen
- **Content is loud, chrome is quiet**: profile photos are full-color edge-to-edge; surrounding UI is neutral + POF Blue
- **Rounded warmth**: 16–20pt card radii, 999pt pill buttons, circular actions — friendly, never sharp
- **Color is rationed by meaning**: blue = brand/action, teal = presence, pink = unread, gold = upgrade

### Border Radius Scale
- Square (0pt): full-bleed carousel edges
- Subtle (6pt): small chips, badges
- Standard (10–12pt): search field, interest chips, message bubbles
- Comfortable (16pt): profile grid cards
- Large (20pt): Meet Me card, sheets
- Extra (28pt): large bottom sheets/modals
- Pill (500pt): all buttons, "Online" pill, composer
- Circle (50%): avatars, Meet Me / like action buttons

## 6. Depth & Elevation

POF is mostly flat with soft shadows reserved for raised cards and the circular action buttons.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Backgrounds, dividers, list rows |
| Card (Level 1) | `rgba(22,24,28,0.10) 0 4px 14px` (light) | Profile grid cards, Meet Me card on light |
| Action Glow | `rgba(0,152,219,0.5) 0 6px 16px` | The blue like / Meet Me "yes" button |
| Sheet (Level 2) | `rgba(22,24,28,0.14) 0 -6px 28px` | Bottom sheets (filters, report, upgrade) |
| Modal Overlay | `rgba(22,24,28,0.5)` | Dim behind sheets and the photo viewer |

**Shadow Philosophy**: shadows are soft and friendly, lifting photo cards off the background and giving the blue like/yes button a confident glow so it reads as *the* primary action. On dark mode shadows nearly vanish, so cards rely on the `#1A1D21` surface step and sheets add a faint 1pt `#2C3036` top border.

### Motion
- **Like (grid card)**: heart scales 1→1.25→1 over 220ms with a light haptic; on mutual like a "It's a Match" overlay slides up (320ms ease-out)
- **Meet Me decision**: tapping ♥/✕ slides the card off (right/left) over 260ms ease-in with a light haptic; "?" lifts the card up and away (240ms); the next card eases in beneath
- **Meet Me swipe**: drag tracks the finger with slight rotation; release past 35% commits; spring-back if under threshold (damping 0.8)
- **Tab switch**: instant content swap; active icon does a subtle 1.05 pop (120ms); badge count animates a quick scale
- **Profile open**: card photo shared-element zooms into the detail carousel over 320ms
- **Message send**: bubble scales in from the composer (180ms ease-out); send button does a 0.9 press dip
- **Online dot**: a slow 2s pulse on the teal presence dot (opacity 1↔0.5) to signal "live"
- **Skeleton loading**: grid shows shimmering rounded photo placeholders (1.2s loop) while photos fetch
- **Haptic feedback**: light impact on like, Meet Me decision, tab switch; success haptic on a new match

## 7. Do's and Don'ts

### Do
- Lead with POF Blue (`#0098DB → #00A6E2`) for brand + every primary action — logo, CTA, active tab, like button
- Build the match grid as a 2-column full-bleed photo grid with bottom gradient name/meta scrims
- Make Meet Me one big card + three round buttons (✕ / ? / ♥), the ♥ in POF Blue with a glow
- Show presence aggressively — teal "Online" pill + "Active now" meta; pulse the teal dot
- Treat Messages as a primary destination — prominent tab with a pink unread badge
- Keep text on photos white over a bottom gradient scrim — always
- Round everything — 16–20pt cards, 999pt pill buttons, circular actions
- Use heavy rounded type (800/900) for names and titles — approachable, unintimidating
- Use gold (`#FFB23E`) only for upgrade/premium; pink (`#FF4F8B`) only for unread/notification
- Keep profile photography full-color edge-to-edge in both themes — chrome dims, content does not

### Don't
- Don't use pure black (`#000000`) for canvas or text — cool near-black `#121417` / near-black `#16181C`
- Don't put colored text on profile photos — white on a gradient scrim only
- Don't make the app a single swipe deck — POF is grid-first + Meet Me; show many people at once
- Don't bury messaging — the Messages tab and unread badge are prominent by design
- Don't use sharp corners — POF's rounded warmth (16–20pt, pills, circles) is the brand feel
- Don't recolor POF Blue per screen — one brand blue everywhere for brand/action
- Don't use the brand blue for upgrade — upgrade is gold; presence is teal; unread is pink
- Don't desaturate profile photos in dark mode — only chrome dims
- Don't use thin/hairline weights for names — names lead and are bold (700+)
- Don't put shadows on flat list rows — shadows are for raised photo cards and the blue action button only
- Don't hide distance/last-active — "3 mi · Active now" is core metadata, not optional decoration

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 2-col grid, 12pt insets; Meet Me buttons 52pt |
| iPhone 13/14/15 | 390pt | Standard 2-col grid, 14pt insets, 10pt gutter |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated by the top bar |
| iPhone 15/16 Pro Max | 430pt | 2-col grid larger cards; insets 16pt |
| iPad (portrait) | 768pt | 3-col grid; Meet Me card centered max ~480pt |
| iPad (landscape) | 1024pt+ | 4-col grid; profile detail as a centered sheet |

### Dynamic Type
- Display, titles, body, meta: full scale
- Card-scrim name/meta, tab labels (10pt), unread badge (9pt), status chips (12pt): FIXED (layout-sensitive over imagery)
- Profile name in detail scales; card-scrim name stays fixed to protect the photo composition

### Orientation
- Grid reflows column count on rotation (2→3→4-up)
- Meet Me card re-centers and caps width; swipe physics unchanged
- Profile detail carousel keeps a fixed aspect region; rotates with letterboxing if needed

### Touch Targets
- Tab icons: 23pt glyph, 44pt hit
- Grid-card like: 36pt circle, 44pt hit
- Meet Me action buttons: 56pt circle (52pt SE)
- Top-bar icons: 22pt glyph, 44pt hit
- Full grid card: entire card tappable to open profile

### Safe Area Handling
- Top: top bar + segmented strip respect safe area + Dynamic Island
- Bottom: tab bar + home indicator respected; the profile-detail message bar docks above the home indicator
- Keyboard: message composer rises above the keyboard; search field scrolls clear
- Sides: 14pt grid inset (12pt SE)

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF` / bg `#F4F5F7`
- Canvas (dark): `#121417`
- Surface 1 (dark): `#1A1D21` · Surface 2 (dark): `#23272C`
- Divider: `#E4E6EA` light / `#2C3036` dark
- Text primary: `#16181C` light / `#EDEFF2` dark
- Text secondary: `#6B7079` / `#9BA1AB`
- Text on photo: `#FFFFFF`
- POF Blue: `#0098DB` · POF Blue Light: `#00A6E2`
- Brand gradient: `#0098DB → #00A6E2` (135°)
- Online teal: `#00C9B7` · Unread pink: `#FF4F8B` · Upgrade gold: `#FFB23E`
- Success: `#20C997` · Error: `#FF5267`
- Card scrim: `transparent → rgba(0,0,0,0.78)`
- Selection tint: `#0098DB1F`

### Example Component Prompts
- "Build the POF match grid in SwiftUI: a 2-column `LazyVGrid`, 14pt outer inset, 10pt spacing. Each card is 3:4, 16pt corner radius, full-bleed photo. Top-left an 'Online' pill — `rgba(0,0,0,0.42)` bg, a 7pt teal `#00C9B7` dot + 'Online' in white Nunito Sans 12pt w700, 999pt radius. Bottom gradient scrim `transparent → rgba(0,0,0,0.78)` with name+age white 15pt w700 and '3 mi · Active now' white 12pt w600. Bottom-right a 36pt circular `#0098DB` like button with a white heart and shadow `rgba(0,152,219,0.5) 0 4px 12px`."

- "Create the POF Meet Me card: a `#1A1D21` surface card, 20pt corner radius, a centered 1:1 photo (16pt radius), name+age in Nunito Sans 18pt w800, prompt 'Would you like to meet {name}?' in 13pt w700 `#9BA1AB`, then a row of three 56pt circular buttons — No (✕, surface + 1.5pt divider border, secondary glyph), Maybe (?, surface + 1.5pt `#FFB23E` border, gold glyph), Yes (♥, solid `#0098DB`, white heart, shadow `rgba(0,152,219,0.5) 0 6px 16px`). Drag right = yes / left = no, commit past 35%, spring-back under threshold."

- "Render the POF bottom tab bar: 5 slots — Matches, Meet Me, Search, Messages, Me. Background `#121417` @94% + blur, 0.5pt top border `#2C3036`. 23pt glyphs + 10pt w800 labels, active `#0098DB` (filled icon), inactive `#9BA1AB`. The Messages tab shows a pink `#FF4F8B` unread badge (white 9pt w800, min 16pt) top-right when count > 0."

- "Build a POF primary button: pill (999pt), linear-gradient `#0098DB → #00A6E2` at 135°, white Nunito Sans 16pt w800, 14pt/28pt padding, pressed = brightness 0.93 + scale 0.98. Add a gold Upgrade pill (`#FFB23E` bg, `#2A1B05` text, 14pt w800) and a blue outline 'View Profile' pill (1.5pt `#0098DB`, `#0098DB` text)."

- "Create a POF 'Online' presence pill + meta row: a 7pt teal `#00C9B7` dot with a slow 2s opacity pulse (1↔0.5), 'Online' or 'Active now' in Nunito Sans 12pt w700, followed by '· 3 mi' in `#9BA1AB`. Over a photo, wrap it in a `rgba(0,0,0,0.42)` 999pt pill with white text."

- "Build the POF profile detail: a full-bleed swipeable photo carousel with page dots, an overlaid name+age (white 26pt w800 over a bottom scrim), a scroll of bio (Nunito Sans 16pt w400) + rounded interest chips + '3 miles away · Active now', and a sticky bottom bar with a gradient 'Send Message' pill `#0098DB → #00A6E2` and a 48pt circular like button."

### Iteration Guide
1. Canvas is white (`#FFFFFF`) light / cool near-black `#121417` dark — NOT pure black
2. POF Blue `#0098DB → #00A6E2` is *the* brand color — logo, primary CTA, active tab, like/yes button; one blue everywhere
3. The match grid is 2-column full-bleed photo cards with bottom gradient name/meta scrims — content-dense, photo-forward
4. Meet Me = one big card + ✕ / ? / ♥ round buttons; ♥ is POF Blue with a glow and a heart pop
5. Presence is teal (`#00C9B7`) and aggressive — "Online" pill + "Active now" meta + a pulsing dot
6. Messaging is a primary destination — prominent Messages tab with a pink (`#FF4F8B`) unread badge
7. Text on photos is always white over a `transparent → rgba(0,0,0,0.78)` scrim — never colored text on imagery
8. Round everything — 16–20pt cards, 999pt pill buttons, circular actions; heavy rounded type (800/900) for names/titles
9. Color is rationed by meaning — blue = brand/action, teal = presence, pink = unread, gold = upgrade
10. Profile photography stays full-color edge-to-edge in both themes — chrome dims, content does not; motion is friendly (180–320ms)
