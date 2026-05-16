# Design System Inspiration of WeChat (iOS)

## 1. Visual Theme & Atmosphere

WeChat's iOS app is not really a messaging app — it's an operating system wearing a chat app's clothes, and the design reflects that. The visual language is deliberately spare and utilitarian: a light gray system (`#EDEDED` chat backdrop, `#FFFFFF` cards), near-black text (`#181818`), and a single brand color, **WeChat Green** (`#07C160`), used with monastic discipline. There is almost no decoration. The reason is structural: WeChat must hold messaging, payments, Moments (social feed), Mini Programs (third-party apps), Channels (video), Scan, and an entire commerce stack inside one shell. A loud aesthetic would collapse under that weight. So the chrome recedes to gray neutrality and lets the *list* — the most scalable UI primitive ever made — carry everything.

The chat thread looks plain on purpose. Incoming bubbles are flat white (`#FFFFFF`); outgoing bubbles are a pale, slightly acidic green (`#95EC69` — not the brand green, a lighter chat-tint) with black text. Bubbles have a small triangular tail and a modest 6pt radius — squarer and tighter than Western messengers, a distinctly WeChat silhouette. The energy of the app is not in the thread; it is in the **Discover** tab, the super-app hub. Discover is a grouped list of system rows — Moments, Channels, Scan, Mini Programs, Search, Nearby, Top Stories — each a 56pt row with a colored 28pt square icon, a title, and a chevron. This list is the gateway to WeChat's entire ecosystem, and its grouped-list pattern (white rows, gray gutters, hairline dividers) is the single most identifying structural component of the app.

Typography is **PingFang SC / Noto Sans** (deep CJK first, system fallback) at weights 400 / 600. Sizes are conservative: contact names 17pt, message bodies 17pt, list rows 17pt — WeChat uses one dominant body size across nearly everything for a calm, scannable rhythm. The red-dot badge (`#FA5151`) is the universal "attention" primitive, appearing on tabs, rows, and avatars. The four bottom tabs — Chats, Contacts, Discover, Me — are spare and unchanging. The overall feeling is "the quiet infrastructure of daily life in China": gray, list-driven, green-accented, ruthlessly utilitarian, and engineered to scale to a billion users without visual fatigue.

**Key Characteristics:**
- Super-app **Discover hub** — a grouped list (Moments / Channels / Scan / Mini Programs / Search / Nearby) gateway to the whole ecosystem
- Light-gray system: chat backdrop `#EDEDED`, cards `#FFFFFF`, near-black text `#181818`
- WeChat Green (`#07C160`) as the sole accent — used with extreme restraint
- Outgoing bubble pale chat-green `#95EC69`; incoming bubble white `#FFFFFF`; black text
- Tight, squarer bubbles — small triangular tail, 6pt radius (distinct WeChat silhouette)
- Grouped-list pattern everywhere — white 56pt rows, gray gutters, hairline dividers, colored square icons
- Moments feed — a card-light social timeline reached from Discover
- Red-packet (hóngbāo) card — the iconic gold/red money envelope
- Universal red-dot badge (`#FA5151`) — the attention primitive on tabs, rows, avatars
- One dominant body size (17pt) for a calm, scannable rhythm; PingFang SC / Noto Sans 400/600
- Four spare tabs: Chats / Contacts / Discover / Me

## 2. Color Palette & Roles

### Primary
- **WeChat Green** (`#07C160`): Send-ready accent, primary buttons, active tab, confirmation, toggles, "Pay" action.
- **WeChat Green Pressed** (`#06A050`): Pressed/active state for green buttons and toggles.
- **Outgoing Bubble Green** (`#95EC69`): The pale chat-tint of your own message bubbles (NOT the brand green).

### Canvas & Surface (Light)
- **Chat Backdrop** (`#EDEDED`): The chat-room background and grouped-list base.
- **Canvas White** (`#FFFFFF`): Cards, list rows, incoming bubbles, navigation bars, Discover rows.
- **Surface Gray** (`#F7F7F7`): Search field, secondary fills, pressed-row tint base.
- **Divider** (`#D9D9D9`): Hairline dividers between list rows and grouped sections.
- **Text Primary** (`#181818`): Contact names, message text, list-row titles, primary labels.
- **Text Secondary** (`#888888`): Previews, timestamps, captions, secondary labels.
- **Text Tertiary** (`#B2B2B2`): Placeholders, disabled labels.

### Canvas & Surface (Dark — Limited Use)
WeChat's iOS app is primarily a light surface; a dark variant exists for system-dark users.
- **Dark Canvas** (`#1A1A1A`) (rows/cards)
- **Dark Backdrop** (`#111111`) (chat/grouped base)
- **Dark Incoming Bubble** (`#2C2C2C`)
- **Dark Outgoing Bubble** (`#3EB575`) (muted green in dark)
- **Dark Text Primary** (`#E8E8E8`)
- **Dark Text Secondary** (`#9A9A9A`)

### Message-Specific
- **Incoming Bubble** (`#FFFFFF`): Left-aligned received bubble.
- **Outgoing Bubble** (`#95EC69`): Right-aligned sent bubble (pale chat-green).
- **Bubble Text** (`#181818`): Text in both incoming and outgoing bubbles.
- **Bubble Meta** (`#888888`): System time separators (centered, not per-bubble in WeChat).

### Discover / Icon Accents (per-row square icons)
- **Moments** (`#3F7DD5` blue), **Scan** (`#2EA0F8` light blue), **Channels** (`#FA9D3B` orange), **Mini Programs** (`#3CC51F` green), **Search** (`#5C7CFA` indigo), **Nearby** (`#F76F34` orange), **Top Stories** (`#EB6F6F` red). Each is a 28pt rounded-square glyph chip — color is data-driven, only here.

### Semantic
- **Red Dot / Badge** (`#FA5151`): Universal unread/attention badge on tabs, rows, avatars.
- **Red Packet** (`#FA5151` envelope / `#FBE3B3` gold accent): The hóngbāo money card.
- **Link Blue** (`#576B95`): Inline links / "official account" article links inside bubbles.
- **Error Red** (`#FA5151`): Failed-to-send, destructive actions.

## 3. Typography Rules

### Font Family
- **Primary**: `PingFang SC` (Apple's CJK system face on iOS) / `Noto Sans` for Latin
- **Fallback Stack**: `-apple-system, 'PingFang SC', 'Noto Sans', BlinkMacSystemFont, 'SF Pro Text', 'Hiragino Sans', sans-serif`
- **Weights**: 400 (regular), 600 (semibold) — WeChat's hierarchy is regular vs semibold only

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Nav Title | PingFang SC | 17pt | 600 | 1.3 | 0pt | Centered title in the top bar |
| Contact Name | PingFang SC | 17pt | 400 | 1.3 | 0pt | Row title in chat / contacts list |
| List Row Title | PingFang SC | 17pt | 400 | 1.3 | 0pt | Discover / Me / settings row title |
| Message Body | PingFang SC | 17pt | 400 | 1.4 | 0pt | The bubble text — the dominant size |
| Section Header | PingFang SC | 14pt | 400 | 1.2 | 0pt | Grouped section caption ("A"–"Z" / labels) |
| Message Preview | PingFang SC | 14pt | 400 | 1.3 | 0pt | Last-message snippet in chat list |
| Timestamp | PingFang SC | 13pt | 400 | 1.2 | 0pt | Time on a chat row |
| Time Separator | PingFang SC | 12pt | 400 | 1.2 | 0pt | Centered date/time chip in a thread |
| Button (Primary) | PingFang SC | 17pt | 600 | 1.0 | 0pt | Green primary button label |
| Button (Secondary) | PingFang SC | 16pt | 400 | 1.0 | 0pt | Outline / cancel button label |
| Tab Label | PingFang SC | 10pt | 400 | 1.0 | 0pt | Bottom tab bar labels |
| Badge Count | PingFang SC | 11pt | 600 | 1.0 | 0pt | Number in the red badge |
| Moments Name | PingFang SC | 15pt | 600 | 1.3 | 0pt | Author name in the Moments feed (link blue) |
| Caption / Meta | PingFang SC | 13pt | 400 | 1.3 | 0pt | Moments timestamp, footnotes |

### Principles
- **One dominant size (17pt)**: contact names, list rows, and message bodies all sit at 17pt for a calm, uniform scan rhythm
- **Two weights only**: PingFang SC 400/600 — semibold means "title/primary", nothing else
- **CJK-first metrics**: line-height kept generous (1.4 on body) so dense CJK never feels cramped
- **Color is rationed**: text is `#181818`/`#888888`; the only colored text is Moments author names (link blue) and links
- **The red dot is the loudest element** — a tiny `#FA5151` badge outranks any typographic emphasis for "attention"
- **Dynamic Type respected** on names/rows/bodies; tab labels, badge counts, time separators are layout-fixed

## 4. Component Stylings

### Buttons

**Primary Button ("Send", "Pay", "Confirm")**
- Background: `#07C160`
- Text: `#FFFFFF`, PingFang SC 17pt weight 600
- Padding: 12pt vertical, full-width (16pt horizontal inset) or compact for inline
- Corner radius: 6pt (rectangular, tight — WeChat's button silhouette)
- Pressed: `#06A050`, scale 0.99

**Outline / Secondary Button ("Cancel")**
- Background: `#FFFFFF`
- Text: `#181818`, PingFang SC 16pt weight 400
- Border: 1pt solid `#D9D9D9`
- Corner radius: 6pt
- Pressed: background `#F7F7F7`

**Send Button**
- When composer empty: a "+" (more) and a voice/sticker glyph cluster, no send
- When text exists: a green `#07C160` rectangular button, label "Send" (PingFang SC 16pt w600 white), corner radius 4pt, compact
- Pressed: `#06A050`

**Text Button ("Edit", inline actions)**
- PingFang SC 16pt weight 400, color `#576B95` (WeChat's link blue) or `#07C160` for confirming actions
- No background; 44pt hit area; pressed opacity 0.5

**Icon Action (Voice, Sticker, Plus)**
- Glyph 26pt, hit area 44pt, color `#181818` (WeChat tints composer glyphs near-black, not gray)
- The "+" opens the attachment grid (album, camera, video call, location, red packet, transfer…)

### Cards & Containers

**Chat / Contacts List Row**
- Height: 64pt (chat list) / 56pt (contacts)
- Layout: rounded-square avatar 44–48pt (leading) → name (17pt w400) + last-message preview (14pt w400 `#888888`, 1-line) → trailing: timestamp (13pt) + red-dot/count badge
- Avatar shape: WeChat avatars are **rounded squares** (≈8pt radius), not circles — a key brand tell
- Background: `#FFFFFF`; pressed `#ECECEC`
- Divider: hairline `#D9D9D9` inset to text origin (not under avatar)

**Message Bubble — Incoming**
- Background: `#FFFFFF`
- Text: `#181818`, PingFang SC 17pt w400
- Padding: 9pt vertical, 12pt horizontal
- Corner radius: 6pt with a small solid triangular tail pointing toward the leading avatar
- Sender avatar (40pt rounded square) shown leading on each message
- Max width: 72% of chat width; left-aligned with 8pt inset after avatar

**Message Bubble — Outgoing**
- Background: `#95EC69` (pale chat-green)
- Text: `#181818`, PingFang SC 17pt w400
- Corner radius: 6pt with a triangular tail pointing toward the trailing avatar
- Your avatar (40pt rounded square) shown trailing
- Max width: 72%; right-aligned with 8pt trailing inset
- Time is NOT per-bubble — WeChat shows a centered time chip between time-grouped runs

**Discover Grouped Row (Signature — the super-app hub)**
- Height: 56pt, background `#FFFFFF`
- Leading: a 28pt rounded-square colored icon chip (per-feature color, see §2)
- Title: PingFang SC 17pt w400 `#181818`
- Trailing: optional red-dot/badge, then a `#B2B2B2` chevron
- Grouped: rows sit on `#FFFFFF`, separated into clusters by `#EDEDED` gutters with hairline `#D9D9D9` dividers between rows within a cluster
- Order: [Moments] · [Channels, Scan, Top Stories] · [Search, Nearby] · [Mini Programs]
- This grouped list IS WeChat's most identifying structural component

**Moments Card**
- A near-card-less timeline on `#FFFFFF`: 40pt rounded-square avatar, author name (15pt w600 `#576B95`), post text (17pt `#181818`), a 1–9 image grid (square thumbs, 3pt gaps), timestamp (13pt `#888888`), and a light gray comment/like bar
- Divider hairline between posts

**Red Packet (Hóngbāo) Card (Signature)**
- A rich red (`#FA5151` → deeper red gradient acceptable) card, ~260pt wide, corner radius 8pt
- A circular gold (`#FBE3B3`) "open" coin/seal centered, white blessing text ("Best wishes!"), sender line
- Appears inline in a thread as its own special bubble-card; tapping animates the seal opening

**System Time Separator**
- Centered, full-width, PingFang SC 12pt `#888888` on the `#EDEDED` backdrop (no plate), e.g. "2:14 PM" / "Yesterday"

### Navigation

**Bottom Tab Bar**
- Height: 50pt + safe area
- Background: `#F7F7F7` (very light, near-white), top hairline `#D9D9D9`
- Tabs: Chats, Contacts, Discover, Me
- Icon 26pt; active `#07C160`, inactive `#999999`
- Labels: PingFang SC 10pt w400, always shown
- Chats tab: aggregated unread red badge `#FA5151`; Discover: a red dot when there's new Moments/activity

**Top Bar**
- Height: 44pt + safe area, background `#EDEDED` (matches backdrop) or `#FFFFFF` on white screens
- Centered title (17pt w600 `#181818`)
- Leading: back chevron 22pt `#181818` (with chat name)
- Trailing: a single "+" or "⋯" glyph 22pt `#181818` (WeChat keeps the top bar minimal — one trailing action)

**Thread Header**
- Height: 44pt + safe area, `#EDEDED` background, bottom hairline
- Leading: back chevron + chat name (17pt w600)
- Trailing: a "⋯" (more) glyph 22pt `#181818` — opens chat info (members, mute, etc.)

### Input Fields

**Composer**
- Sits on the `#EDEDED` backdrop
- Leading: a voice glyph 26pt `#181818` (tap to switch to hold-to-talk)
- Field: `#FFFFFF`, min height 36pt, corner radius 6pt (squarer than Western messengers)
- Trailing on the backdrop: a sticker (smiley) glyph and a "+" glyph, 26pt `#181818`
- When text exists: a compact green "Send" button replaces the "+"
- Field text: PingFang SC 17pt w400

**Search Field**
- Background: `#FFFFFF` on an `#EDEDED` strip
- Height: 36pt, corner radius 6pt
- Centered (placeholder + magnifier) when idle: "Search" `#B2B2B2`, magnifier 15pt `#888888`
- Focus: left-aligns, cursor `#07C160`

### Distinctive Components

**Discover Super-App Hub**
- The grouped list of feature rows is THE identifying screen — Moments, Channels, Scan, Mini Programs, Search, Nearby — the gateway to WeChat's entire ecosystem
- White rows, `#EDEDED` gutters, hairline dividers, 28pt colored square icons, chevrons

**Rounded-Square Avatars**
- WeChat avatars are rounded squares (~8pt radius) everywhere — lists, bubbles, Moments, Me — a constant brand tell

**Red Packet (Hóngbāo)**
- The gold-on-red money envelope card with an animated opening seal — culturally iconic, unmistakably WeChat

**Universal Red Dot**
- A tiny `#FA5151` badge is the single most important attention primitive — on tabs, rows, avatars, Discover entries

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 44, 56
- Bubble inner padding: 9pt vertical, 12pt horizontal
- Inter-bubble gap: 4pt within a run, 12pt between senders
- Row inset: avatar leading 12pt, text origin 64pt (divider starts here)
- Grouped-list gutter: 12pt `#EDEDED` between row clusters

### Grid & Container
- Single column for chat, lists, Discover, Me
- Bubble max width: 72% of chat width
- Moments image grid: 3-column square, 3pt gaps (1 image = larger single; 4 = 2×2)
- Discover/Me are grouped vertical lists, not cards
- iPad: master-detail split (list left, chat right)

### Whitespace Philosophy
- **Calm uniformity**: one 17pt body size + gray neutrality = a screen you can scan for years without fatigue
- **The list is the layout**: WeChat's scalability comes from grouped lists, not bespoke layouts
- **Gutters separate, hairlines divide**: 12pt gray gutters cluster groups; 1px dividers separate rows within
- **Color earns its place**: only icons, the red dot, and the red packet get saturated color

### Border Radius Scale
- Tight (4pt): the compact "Send" button
- Standard (6pt): bubbles, primary/outline buttons, search field, composer field — WeChat's squarer signature
- Avatar (8pt): rounded-square avatars, red-packet card
- Icon Chip (8pt): the 28pt Discover square icons
- Circle (50%): the open-seal coin in a red packet, a few status dots
- (WeChat deliberately avoids large pill radii — its silhouette is squarer than Western messengers)

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Lists, Discover rows, bubbles, the backdrop — the default everywhere |
| Bubble (Level 0) | No shadow (fill + tail) | Bubbles separate by fill + triangular tail, not elevation |
| Bar Hairline (Level 1) | `rgba(0,0,0,0.05) 0 0.5px 0` | Tab bar + headers |
| Sheet (Level 2) | `rgba(0,0,0,0.16) 0 -8px 28px` | Attachment grid, action sheet, sticker panel |
| Red Packet (Level 1) | `rgba(0,0,0,0.12) 0 2px 8px` | The hóngbāo card lifts slightly to feel like a physical envelope |
| Toast (Level 3) | `rgba(0,0,0,0.18) 0 6px 18px` | "Sent", "Saved" confirmations |

**Shadow Philosophy**: WeChat is almost entirely flat — flatness is what lets a billion-user, infinitely-deep app stay calm. Lists, rows, and bubbles have zero shadow; bubbles separate by the green/white fill and their triangular tail. The only deliberate lift is the red-packet card, which gets a small shadow so it reads as a physical envelope you can "open." Real elevation is reserved for the attachment grid and sheets.

### Motion
- **Red-packet open**: tapping the seal triggers a coin-flip / shimmer reveal (≈600ms) — the signature animation
- **Send (text)**: bubble appears with a quick 150ms fade + slight scale, no flourish
- **Press states**: rows and buttons use a subtle background tint change (`#ECECEC` / `#06A050`), ~120ms — WeChat's restraint extends to motion
- **Attachment grid present**: slides up 260ms ease; the "+" rotates 45° → "×"
- **Tab switch**: instant, no animation
- **Pull-to-refresh (Moments)**: minimal system spinner
- **Red dot**: appears/disappears instantly (no animation) — it must feel authoritative, not playful

## 7. Do's and Don'ts

### Do
- Treat **Discover as a grouped list** of feature rows (Moments / Channels / Scan / Mini Programs / Search / Nearby) — the super-app hub is the identity
- Use the gray system: chat backdrop `#EDEDED`, white cards/rows, near-black `#181818` text
- Make outgoing bubbles pale chat-green `#95EC69`, incoming white, text black — NOT the brand green for bubbles
- Reserve WeChat Green `#07C160` for "go": send, pay, primary buttons, active tab, toggles
- Use **rounded-square avatars** (~8pt radius) everywhere — not circles
- Keep bubbles tight and squarer (6pt radius + a triangular tail)
- Use the red dot (`#FA5151`) as the universal attention primitive on tabs/rows/avatars
- Hold to ONE dominant body size (17pt), two weights (400/600), CJK-friendly line-height

### Don't
- Don't make Discover a colorful dashboard — it's a calm grouped list; color lives only in the 28pt icons
- Don't use circular avatars — WeChat's rounded square is a constant brand tell
- Don't use the saturated brand green for the outgoing bubble — that's the pale `#95EC69`
- Don't round bubbles like Western messengers — keep the 6pt squarer silhouette with a tail
- Don't add a second accent or decorative gradients — gray + green + the red dot is the entire system
- Don't put per-bubble timestamps — WeChat uses centered time separators between runs
- Don't animate aggressively — WeChat's motion is near-absent (the red-packet open is the one flourish)
- Don't introduce extra type sizes/weights — 17pt body + 400/600 keeps a billion-user app calm

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 60pt chat rows, bubble max 76%, Discover row 52pt |
| iPhone 13/14/15 | 390pt | Standard — 64pt chat rows, Discover 56pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the header |
| iPhone 15/16 Pro Max | 430pt | Larger avatars (50pt), wider thread |
| iPad | 768pt+ | Master-detail split: list (left, 360pt) + chat/Discover (right) |

### Dynamic Type
- Contact names, list rows, message bodies: full scale (they share the 17pt base)
- Message body caps at ~22pt; bubble grows with content
- Tab labels, badge counts, time separators: layout-fixed (do not scale)
- Discover icon chips: fixed 28pt (grid-sensitive)

### Orientation
- Lists / chat / Discover / Me: **portrait-locked** on iPhone (WeChat is portrait-first)
- iPad: split view in both orientations
- Image/video viewer & Channels video: rotate freely

### Touch Targets
- Compact "Send" button: ≥44pt effective hit
- Discover rows: full 56pt row tappable
- Chat rows: full 64pt row tappable
- Composer glyphs (voice/sticker/+): 44pt hit
- Tab items: 26pt icon, 49pt hit

### Safe Area Handling
- Top: header respects safe area / Dynamic Island
- Bottom: composer + tab bar respect home indicator; attachment grid rises above it
- Sides: 8pt chat insets, 12pt grouped-list gutters

## 9. Agent Prompt Guide

### Quick Color Reference
- Chat backdrop / grouped base: `#EDEDED`
- Canvas (rows/cards): `#FFFFFF`
- Surface: `#F7F7F7`
- Incoming bubble: `#FFFFFF`; outgoing bubble: `#95EC69` (pale green); bubble text: `#181818`
- Divider: `#D9D9D9`
- Text primary: `#181818`; secondary: `#888888`; tertiary: `#B2B2B2`
- WeChat Green (chrome/CTA): `#07C160`; pressed `#06A050`
- Red dot / badge / packet: `#FA5151`; link blue: `#576B95`

### Example Component Prompts
- "Create the SwiftUI WeChat Discover grouped list (the super-app hub): white 56pt rows on an #EDEDED base, clustered with 12pt gray gutters and 1px #D9D9D9 dividers between rows in a cluster. Each row: a 28pt rounded-square colored icon chip (Moments #3F7DD5, Scan #2EA0F8, Channels #FA9D3B, Mini Programs #3CC51F), title PingFang SC 17pt w400 #181818, trailing optional #FA5151 red dot then a #B2B2B2 chevron. Cluster order: [Moments] · [Channels, Scan, Top Stories] · [Search, Nearby] · [Mini Programs]."
- "Build a WeChat chat list row: white background, 64pt height, a 48pt ROUNDED-SQUARE avatar (~8pt radius, NOT a circle) leading, name 'Li Wei' PingFang SC 17pt w400 #181818, last-message preview '[Photo]' PingFang SC 14pt #888888 (1-line). Trailing: timestamp '2:14 PM' PingFang SC 13pt #888888 and a #FA5151 red badge with white '3'. Pressed background #ECECEC. Divider hairline #D9D9D9 inset to text origin."
- "Design WeChat message bubbles on the #EDEDED backdrop: incoming #FFFFFF, outgoing #95EC69 (pale chat-green, NOT the brand green), text #181818 PingFang SC 17pt w400, padding 9pt/12pt, corner radius 6pt (squarer than Western messengers) with a small solid triangular tail pointing toward that sender's rounded-square avatar. NO per-bubble timestamp — use a centered '2:14 PM' separator PingFang SC 12pt #888888 between time-grouped runs."
- "Build the WeChat red-packet (hóngbāo) card: a rich #FA5151 card ~260pt wide, corner radius 8pt, slight shadow rgba(0,0,0,0.12) 0 2px 8px so it feels like a physical envelope. A circular #FBE3B3 gold 'open' seal centered, white blessing text 'Best wishes!' PingFang SC, and a sender line. It appears inline as its own bubble-card; tapping animates the seal opening with a ~600ms coin-flip shimmer."
- "Build the WeChat bottom tab bar: 50pt + safe area, background #F7F7F7, top hairline #D9D9D9. Four tabs Chats / Contacts / Discover / Me, icons 26pt, active #07C160, inactive #999999, labels PingFang SC 10pt w400. Chats shows an aggregated #FA5151 unread badge; Discover shows a #FA5151 red dot when there's new activity."

### Iteration Guide
1. **Discover is a grouped list** of feature rows — the super-app hub is THE identity; never a colorful dashboard
2. Gray system: backdrop `#EDEDED`, white rows/cards, near-black `#181818` text — calm at a billion-user scale
3. Outgoing bubble = pale chat-green `#95EC69`; incoming = white; text always `#181818` — never the brand green for bubbles
4. WeChat Green `#07C160` only for "go": send, pay, primary buttons, active tab, toggles
5. Avatars are **rounded squares** (~8pt), never circles — a constant brand tell
6. Bubbles are tight and squarer (6pt + triangular tail); timestamps are centered separators, not per-bubble
7. The red dot (`#FA5151`) is the universal attention primitive — tabs, rows, avatars, Discover
8. One dominant 17pt body size, two weights (400/600), CJK-friendly line-height; motion is near-absent except the red-packet open
