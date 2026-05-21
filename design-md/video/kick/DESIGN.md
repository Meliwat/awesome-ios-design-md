# Design System Inspiration of Kick (iOS)

## 1. Visual Theme & Atmosphere

Kick's iOS app has one of the most aggressive, single-minded color identities of any major app: a **near-black canvas (`#0E0E10`)** with a single **electric, almost-radioactive lime green (`#53FC18`)** doing absolutely everything. Kick is a live-streaming platform — a creator-friendly Twitch competitor — and its whole visual language is built to feel **fast, loud, and live**. The green isn't a tasteful accent dropped in occasionally; it *is* the brand. It owns the logo, every primary CTA (Follow, Subscribe, Go Live), the active bottom-tab icon, links, online-avatar rings, and progress states. Everything else is a tightly controlled greyscale (`#0E0E10` → `#161618` → `#1F1F23` → `#2A2A2E`), specifically so the green reads like a neon sign in a dark room. The canvas is `#0E0E10` rather than pure `#000000` on purpose — `#53FC18` on true black vibrates uncomfortably, so the slight lift tames it just enough.

The signature screen is the **live-stream watch page**. It has three stacked parts: (1) the **video player** on top (16:9, with a `LIVE` pill, viewer count, and minimal overlay controls); (2) a **streamer bar** — avatar with a green online ring, streamer name with a green verified check, category + viewer count, and a green **Follow** button; and (3) the **chat panel**, which fills the rest of the screen and is, along with the green, the second pillar of Kick's identity. Kick's chat is dense, fast, and *role-colored*: usernames render in different colors by role (moderator `#3EA6FF`, subscriber `#FFC700`, VIP `#FF4FD8`, regular users in a randomized green-leaning palette), each prefixed by a tiny **badge chip** (MOD / a sub-month number / VIP / OG / VERIFIED), with **custom emote sprites** rendered inline at ~18pt. The chat input sits at the bottom with a green send button. This watch screen — *video + streamer bar + live chat* — is what makes a Kick clone recognizably Kick.

The atmosphere is **streamer-first, fast, and a little raw**. Kick deliberately positions itself as the less-corporate streaming platform, and the UI reflects that: high contrast, big bold weights, no soft gradients on chrome, no rounded-pastel friendliness — just black, white, grey, and that one screaming green. Browse/Home is a grid of **live channel cards** (16:9 thumbnail with a red LIVE tag + viewer count, streamer avatar, title, category). Typography is a tight grotesque (Inter is the closest free analog): heavy 800–900 weights for titles and the wordmark, 600/400 for chat (username semibold, message regular), ALL-CAPS tracked micro-labels for LIVE/badges. The result feels like a broadcast control surface — minimal, monochrome, and electric.

**Key Characteristics:**
- Near-black canvas (`#0E0E10`) — not pure black (keeps the green from vibrating); dark-only, no light mode
- A single electric lime green (`#53FC18`) does *everything* — logo, all primary CTAs, active tab, links, online rings
- Strictly monochrome everywhere else (greys `#161618`/`#1F1F23`/`#2A2A2E`) so the green screams
- Signature screen = **video player + streamer bar + live chat panel** stacked vertically
- Role-colored chat usernames — mod `#3EA6FF`, sub `#FFC700`, VIP `#FF4FD8`, regular randomized green-ish
- Tiny **badge chips** before usernames — MOD / sub-month number / VIP / OG / VERIFIED
- Custom **emote sprites** rendered inline in chat (~18pt)
- Red `#FF1F44` LIVE pill + white viewer-count chip on every stream and channel card
- Green **Follow / Subscribe** buttons; green online ring around live streamers' avatars
- Live channel cards — 16:9 thumbnail + LIVE tag + viewer count + avatar + title + category
- Heavy, tight grotesque type (800–900 titles); chat username 600 / message 400
- Bottom tabs: Home / Browse / Following / Clips / Profile — active icon Kick Green

## 2. Color Palette & Roles

### Primary (Brand & Interactive)
- **Kick Green** (`#53FC18`): THE brand color — logo, Follow/Subscribe/Go-Live buttons, active tab icon, links, online-avatar ring, progress, focus. Text on it is `#0E0E10`.
- **Kick Green Pressed** (`#45D912`): Pressed state of green buttons.
- **Kick Green Deep** (`#00E701`): Alternate/legacy green for subtle accents, "Live" emphasis, hover lift.

### Canvas & Surfaces (Dark — the only mode)
- **Canvas Near-Black** (`#0E0E10`): Primary app background (NOT pure black — that makes the green vibrate).
- **Surface 1** (`#161618`): Cards, the streamer bar, list rows, sheets.
- **Surface 2** (`#1F1F23`): Elevated surfaces, the chat input field, pressed rows, chips.
- **Surface 3** (`#2A2A2E`): Highest elevation — menus, popovers, modals, outline-button borders.
- **Divider** (`#2D2D31`): Hairline separators (chat header underline, streamer-bar border, list rows).

### Text
- **Text Primary** (`#FFFFFF`): Titles, streamer names, chat message body, primary content.
- **Text Secondary** (`#A6A6AD`): Category lines, metadata, chat header, secondary labels.
- **Text Tertiary** (`#6E6E76`): Placeholders, timestamps, disabled, very low-emphasis.
- **Chat Body** (`#E4E4E9`): Chat message text (slightly softer than pure white so usernames pop).
- **Text On-Green** (`#0E0E10`): Text on green buttons/badges (the canvas near-black).

### Chat Role Colors (a defining system)
| Role | Username Color | Badge Chip Fill | Badge Text |
|------|---------------|-----------------|------------|
| Moderator | `#3EA6FF` (Mod Blue) | `#3EA6FF` | `#06121F` |
| Subscriber | `#FFC700` (Sub Gold) | `#FFC700` (shows sub-month #) | `#1A1500` |
| VIP | `#FF4FD8` (VIP Pink) | `#FF4FD8` | `#2A0022` |
| OG / Founder | `#53FC18` (Kick Green) | `#53FC18` | `#0E0E10` |
| Verified / Broadcaster | `#FFFFFF` on `#2A2A2E` chip | `#2A2A2E` | `#FFFFFF` |
| Regular user | Randomized from a green-leaning palette (e.g. `#53FC18`, `#7CE7A4`, `#5AC8FA`, `#FF8A65`, `#C9C9D1`) | none | — |

### Semantic & Accent
- **Live Red** (`#FF1F44`): The universal LIVE pill (player, channel cards) — always red, never green.
- **Success Green** (`#53FC18`): Reuses Kick Green for "Followed", "Subscribed" confirmations.
- **Error Red** (`#FF4A4A`): Stream errors, failed actions, destructive confirms.
- **Highlight / Mention** (`#53FC18` at 12% behind a chat line): when you're @-mentioned, the message row gets a faint green wash + a 2pt green left border.

### Note on Theme
Kick ships **dark-only**. There is no light mode; the near-black canvas is the brand environment (it's tuned specifically so `#53FC18` doesn't strobe). Do not invent a light palette — defer to the OS for system surfaces.

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (by Rasmus Andersson, SIL OFL) — the closest free analog to Kick's tight, modern grotesque brand face. Heavy weights (800–900) reproduce the bold, broadcast feel of the wordmark and titles.
- **Alternative**: `Archivo` (Google Fonts, SIL OFL) — acceptable substitute, slightly more geometric.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for viewer counts and sub-month badge numbers so they don't jitter as they tick.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Inter | 32pt | 900 | 1.05 | -0.5pt | "Go Live", onboarding headlines |
| Screen Title | Inter | 24pt | 800 | 1.15 | -0.3pt | "Following", "Browse", page titles |
| Section | Inter | 20pt | 800 | 1.2 | -0.2pt | "Live Channels", "Categories" rails |
| Streamer Name | Inter | 16pt | 700 | 1.2 | 0pt | Streamer name in the watch-page bar |
| Body | Inter | 15pt | 400 | 1.5 | 0pt | Stream descriptions, about, body copy |
| Card Title | Inter | 13pt | 700 | 1.2 | 0pt | Live-card stream titles (1 line, ellipsis) |
| Chat Username | Inter | 13pt | 700 | 1.35 | 0pt | Role-colored sender name |
| Chat Message | Inter | 13pt | 400 | 1.35 | 0pt | Message body `#E4E4E9` |
| Meta | Inter | 13pt | 400 | 1.3 | 0pt | "Just Chatting · 24,812 watching" |
| Button Label | Inter | 15pt | 800 | 1.0 | 0pt | Follow / Subscribe / Go Live |
| Viewer Count | Inter | 11pt | 700 | 1.0 | 0pt | "24.8K" chip (tabular) |
| Badge / LIVE | Inter | 10pt | 800 | 1.0 | 0.5pt | "LIVE", "MOD", "VIP" — ALL CAPS |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |

### Principles
- **Heavy and broadcast-loud**: titles and the wordmark are 800–900 — Kick's type is bold and confident, never light or delicate.
- **Chat is the type-density workhorse**: username 700 + message 400 at 13pt with 1.35 line-height — optimized for fast-scrolling readability, not elegance.
- **ALL CAPS for status only**: `LIVE`, `MOD`, `VIP`, `OG` are uppercase + positively tracked (`+0.5pt`); message text and titles are sentence case.
- **Tabular numerals for counts**: viewer counts and sub-month numbers use tabular figures so they don't shift as they update live.
- **Cool neutral secondary**: metadata is `#A6A6AD` (a neutral cool grey) — the only "color" besides green/role colors is the role-coded chat.
- **Dynamic Type**: titles, body, descriptions scale; chat text scales to L then truncates the username if needed; LIVE/badge/viewer/tab labels stay FIXED.

## 4. Component Stylings

### Buttons

**Primary Button (Follow / Go Live)**
- Shape: Rounded rectangle, 6pt corner radius (Kick uses tighter radii than most streamers)
- Background: `#53FC18` (Kick Green)
- Text: `#0E0E10`, Inter 15pt weight 800
- Height: 44–48pt; padding 13pt vertical, 28pt horizontal
- Pressed: background `#45D912` + scale 0.98
- Followed state: fills `#1F1F23`, text `#FFFFFF`, 1pt `#2A2A2E` border, label "Following"

**Subscribe Button (high-value action)**
- Background: `#1F1F23`
- Border: 1pt `#53FC18`
- Text: `#53FC18`, Inter 14pt weight 800 (often with a star/gem leading glyph)
- Corner radius: 6pt; height 44pt
- Pressed: background `#252529`
- Active "Subscribed": solid `#53FC18`, `#0E0E10` text

**Secondary / Outline Button (Share / Clip / Settings)**
- Background: transparent
- Border: 1pt `#2A2A2E`
- Text + icon: `#FFFFFF`, Inter 14pt weight 700
- Corner radius: 6pt; height 44pt
- Pressed: background `#1F1F23`

**Text Button (Go Live / See All / Watch Replay)**
- Font: Inter 14pt weight 700
- Color: `#53FC18`
- No background, no underline
- Pressed: opacity 0.7

**Chat Send Button**
- 38×38pt, `#53FC18` fill, 8pt radius, `#0E0E10` paper-plane glyph 16pt
- Disabled (empty field): `#1F1F23` fill, `#6E6E76` glyph

### Core Atoms

**Chat Message (a defining unit)**
- Inline flow: optional **badge chip(s)** → **username** (role-colored, 13pt weight 700) → `:` → **message text** (`#E4E4E9` 13pt weight 400) with inline **emote sprites** (~18×18pt, 3pt radius)
- 9pt vertical gap between messages, 14pt horizontal padding
- Badge chip: ~8–10pt weight 900 text, 1–3pt padding, 3pt radius, role fill (see role table)
- @-mention of you: row gets `#53FC18` @ 12% background + 2pt `#53FC18` left border
- System line (e.g. "X subscribed for 6 months"): centered, `#A6A6AD` 12pt, faint `#1F1F23` pill
- Long-press a message → context sheet (Reply, Mention, Timeout/Ban if mod, Copy)

**Live Channel Card**
- 16:9 thumbnail, 8pt corner radius
- Top-left **LIVE** tag: `#FF1F44` fill, white 8pt weight 800 caps, 2×6pt padding, 3pt radius
- Bottom-left **viewer count** chip: `rgba(0,0,0,0.6)` fill, white 9pt weight 700, "24.8K"
- Below: a row of 30pt avatar (green online ring) + VStack of title (13pt weight 700, 1 line) + "Streamer · Category" (11pt `#A6A6AD`)
- Pressed: thumbnail scales 0.98, brief

**Streamer Bar (watch page)**
- 40pt circular avatar with a 2pt `#53FC18` online ring
- Streamer name 16pt weight 700 + green verified check (13pt)
- Category + viewer line 12pt `#A6A6AD`
- Trailing green **Follow** button
- 0.5pt `#2D2D31` bottom border

**Video Player Overlay**
- Top-left **LIVE** pill: `#FF1F44` fill, white dot + "LIVE" 10pt weight 800
- Top-right **viewer** chip: `rgba(0,0,0,0.55)`, eye glyph + count
- Bottom controls: play/pause, volume, spacer, fullscreen — white 18pt glyphs, fade after 3s idle
- Tap toggles controls; double-tap seeks (VOD) / nothing (live)

**Badge Chip (standalone)**
- MOD `#3EA6FF`/`#06121F`, SUB+# `#FFC700`/`#1A1500`, VIP `#FF4FD8`/`#2A0022`, OG `#53FC18`/`#0E0E10`, VERIFIED `#2A2A2E`/`#FFFFFF`
- 10pt weight 900, 3px 9px padding, 4pt radius (standalone) / tighter inline

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe-area home indicator
- Background: `rgba(14,14,16,0.96)` with `backdrop-filter: blur(20px) saturate(180%)`, 0.5pt top border `#2D2D31`
- Tabs (5): Home, Browse, Following, Clips, Profile
- Icon size: 22pt; label Inter 10pt weight 600
- Active: icon + label in `#53FC18`, icon filled
- Inactive: `#6E6E76`
- No pill indicator — color + fill change only

**Top Nav (Home / Browse)**
- Leading: KICK wordmark (logo green block + white "KICK")
- Trailing: search + notifications (white 22pt) — or a "Go Live" green text button for creators
- `#0E0E10` background, no border until scrolled

**Watch Page Nav**
- Minimal: the video itself is the top; a back chevron + share/cast float over the player (white 24pt, in 32pt `rgba(0,0,0,0.4)` circular scrims)

### Input Fields

**Search Bar**
- Height: 44pt; background `#1F1F23`; corner radius 8pt
- Leading magnifyingglass 16pt `#6E6E76`
- Placeholder "Search channels, categories" 14pt `#6E6E76`
- Text `#FFFFFF` 15pt; focus → 1.5pt `#53FC18` border

**Chat Input**
- Height: 38pt; background `#1F1F23`; corner radius 8pt; 12pt horizontal padding
- Placeholder "Send a message" 13pt `#6E6E76`
- Trailing green send button (see Chat Send Button)
- Above it (optional): emote/sticker picker toggle 18pt `#A6A6AD`

### Distinctive Components

**Live Chat Panel (the signature surface)**
- Header: "STREAM CHAT" 12pt weight 800 caps `#A6A6AD` + a settings gear (chat modes: sub-only, slow, emote-only)
- Scrolling message list (auto-scroll to bottom unless user scrolled up → "↓ More messages" pill in green)
- Input dock with green send. The role-colored usernames + badge chips + inline emotes ARE the signature.

**Role Badge System**
- The MOD/SUB#/VIP/OG/VERIFIED chip + matching username color, applied consistently in chat, viewer list, and on profiles — Kick's most distinctive micro-system.

**Emote Sprite**
- Custom per-channel/global emotes rendered inline in chat and in the emote picker at ~18pt, 3pt radius; the picker is a grid sheet with channel/global tabs.

**Online Avatar Ring**
- A 2pt `#53FC18` ring around a streamer's avatar means "live now" — used on the streamer bar, following list, and channel cards.

**Live Channel Grid (Browse/Home)**
- Two-column grid of Live Channel Cards (see atom), grouped by "Following · Live", "Categories", "Recommended".

**Subscribe Sheet**
- Bottom sheet: streamer header, tier options (cards with price + perks), a big green "Subscribe" CTA, "Use Kick Coins" secondary; confirm → `#53FC18` check + haptic.

**Clip Card**
- 16:9 thumbnail with a play glyph + duration chip, title, streamer, view count — used in the Clips tab and shared deep-links.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 6, 8, 9, 12, 14, 16, 20, 24, 32, 40, 48
- Screen horizontal inset: 16pt
- Chat horizontal padding: 14pt; message vertical gap: 9pt
- Live-card grid gutter: 12pt; 2 columns on phone
- Streamer bar padding: 12pt vertical, 14pt horizontal

### Grid & Container
- Watch page: full-bleed 16:9 video → streamer bar → chat fills remaining height (chat is a flexible-height region, never fixed)
- Browse/Home: 2-column live-card grid, 12pt gutter, 16pt screen inset
- Landscape watch: video left (~65%) + chat docked right (~35%) as a side panel
- iPad: 3-column live-card grid; watch page can show video + persistent side chat in portrait too

### Whitespace Philosophy
- **Chat density is the point**: messages are tightly packed (9pt gap) — a fast live chat should show many lines; this is deliberate, not cramped.
- **Monochrome restraint**: the UI is intentionally sparse and grey so the single green and the role colors carry all the visual energy — don't add decorative color.
- **Video first**: the player is always the largest element on the watch page; chrome is minimal and fades.
- **Cards breathe a little**: the browse grid has comfortable 12pt gutters so thumbnails read clearly, but stays edge-to-edge dense.

### Border Radius Scale
- Square (0pt): video player edges, dividers
- Tiny (3pt): badge chips, emote sprites, LIVE/viewer tags
- Small (6pt): buttons (tighter than most streamers — a Kick trait), pills
- Standard (8pt): cards, thumbnails, search/chat input, chat-send button
- Comfortable (12pt): bottom sheets, modals (top corners)
- Pill (500pt): "↓ More messages" pill, filter chips
- Circle (50%): avatars, the online ring, the live dot

## 6. Depth & Elevation

Kick is **flat and high-contrast**. Elevation is conveyed by surface lightening on the near-black field; shadows are minimal (the aesthetic is a flat broadcast UI, not a soft layered one). The green never casts a glow on chrome — it's a flat fill.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, on `#0E0E10` | Page background, chat list, body text |
| Surface | `#161618`, no shadow | Cards, streamer bar, list rows |
| Raised | `#1F1F23`, optional `0 4px 12px rgba(0,0,0,0.4)` | Chat input, chips, pressed cards |
| Floating (Level 2) | `#2A2A2E`, `0 8px 24px rgba(0,0,0,0.5)` | Context menus, emote picker, dropdowns |
| Sheet (Level 3) | `#161618`, `0 -8px 28px rgba(0,0,0,0.55)` | Bottom sheets (Subscribe, message actions) |
| Modal Overlay | Scrim `rgba(0,0,0,0.7)` | Dim behind modals and sheets |

**Shadow Philosophy**: Kick's surfaces are flat broadcast panels — hierarchy comes from the greyscale step and the green, not from soft shadows. Shadows appear only on genuinely floating UI (sheets, the emote picker, context menus). The video overlay uses a subtle bottom vignette for control legibility, nothing more.

### Motion
- **New chat message**: messages append at the bottom with a 120ms fade-in + tiny 4pt slide-up; the list auto-scrolls (spring, 200ms) unless the user has scrolled up
- **"↓ More messages" pill**: appears when scrolled up; tap → smooth-scroll to bottom (300ms) + fade pill out
- **Follow tap**: button morphs to "Following" with a 200ms cross-fade + scale 1→1.06→1 + soft haptic; green confirm flash
- **Subscribe confirm**: sheet CTA → ✓ with a 250ms spring, brief full-green flash + success haptic
- **Live pill**: the red dot can do a subtle 1.5s pulse (opacity 0.6↔1.0) to signal "live"
- **Player controls**: tap toggles with 200ms fade; auto-hide after 3s idle
- **Tab switch**: instant content swap; active icon animates color to green + fill over 200ms
- **Emote picker**: bottom sheet slides up 300ms ease-out
- **Card press**: thumbnail scales to 0.98 over 120ms, releases on tap-up
- **Stream join**: tapping a card cross-fades into the watch page over ~300ms; chat starts streaming immediately
- **Haptics**: soft impact on Follow/Subscribe confirm and chat send; selection tick on tab switch

## 7. Do's and Don'ts

### Do
- Use the near-black canvas (`#0E0E10`) — NOT pure `#000000` (true black makes `#53FC18` vibrate)
- Make Kick Green (`#53FC18`) do everything brand/action — logo, all primary CTAs, active tab, links, online rings
- Keep everything else strictly monochrome grey so the green screams
- Build the watch page as **video → streamer bar → live chat** stacked
- Color-code chat usernames by role (mod `#3EA6FF`, sub `#FFC700`, VIP `#FF4FD8`, OG/regular green-ish)
- Prefix usernames with tiny role **badge chips** (MOD / sub-month # / VIP / OG / VERIFIED)
- Render custom **emote sprites** inline in chat at ~18pt
- Use a red `#FF1F44` LIVE pill + viewer-count chip on every stream and channel card
- Put a 2pt green online ring around live streamers' avatars
- Use tighter radii (6pt buttons, 8pt cards) — Kick is less rounded than most streamers
- Pack chat densely (9pt gaps) — a fast live chat should show many lines
- Use tabular numerals for viewer counts and sub-month numbers

### Don't
- Don't add a light mode — Kick is dark-only; defer to OS for system sheets
- Don't use pure black `#000000` — it makes the green strobe; stay at `#0E0E10`
- Don't introduce extra accent colors — green + role colors only; the monochrome restraint is the identity
- Don't make the LIVE indicator green — it's always red `#FF1F44` (green is for actions/brand)
- Don't render chat usernames all one color — role coloring is a defining system
- Don't drop the badge chips or inline emotes — they ARE Kick's chat
- Don't use big soft radii or pastel friendliness — Kick is tight, bold, raw
- Don't space chat messages loosely — density is intentional for a fast live chat
- Don't put a glow/shadow on the green — it's a flat fill, not a neon bloom on chrome
- Don't use warm grey for metadata — neutral cool grey `#A6A6AD`
- Don't bury the video — it's always the largest element on the watch page
- Don't animate chat heavily — 120ms fades; the chat must stay fast and legible

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Video ~211pt tall; chat fills the rest; 2-col cards tighter |
| iPhone 13/14/15 | 390pt | Standard: video ~219pt; chat region flexible-height |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated above the video |
| iPhone 15/16 Pro Max | 430pt | Larger video (~242pt); more chat lines visible; 2-col cards |
| iPad (portrait) | 768pt | Video + persistent side chat possible; 3-col browse grid |
| iPad (landscape) | 1024pt+ | Video left ~65% + docked chat panel right ~35%; 4-col browse grid |

### Dynamic Type
- Screen titles, body, descriptions, streamer name: full scale
- Chat username + message: scale to L, then truncate the username with ellipsis (keep messages readable)
- LIVE/role badges, viewer count, tab labels: FIXED (layout-sensitive, must stay compact)

### Orientation
- Phone: watch page is portrait-primary (video + chat stacked); rotate → fullscreen video with chat as a slide-over drawer
- iPad: landscape gets the side-by-side video + docked chat
- Browse grid reflows column count by width

### Touch Targets
- Chat message: full row long-press target (≥36pt tall effective)
- Chat send button: 38pt (acceptable; expand hit-area to 44pt)
- Follow/Subscribe buttons: ≥44pt tall
- Tab bar icons: 22pt glyph, 52pt row, full-segment tap
- Live card: full card tappable
- Player controls: 44pt hit areas around 18pt glyphs

### Safe Area Handling
- Top: video bleeds under the status bar / Dynamic Island; floating back/share avoid the notch
- Bottom: chat input dock + tab bar respect the home indicator; keyboard pushes the chat input up (chat list scrolls above it)
- Sides: 16pt content inset; chat at 14pt; video is full-bleed width
- Landscape: player controls and chat drawer avoid the notch side

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (near-black): `#0E0E10` (NOT pure black)
- Surface 1 / 2 / 3: `#161618` / `#1F1F23` / `#2A2A2E`
- Divider: `#2D2D31`
- Kick Green: `#53FC18` (pressed `#45D912`, deep `#00E701`) / text on it `#0E0E10`
- Live Red: `#FF1F44`
- Chat roles — Mod `#3EA6FF`, Sub `#FFC700`, VIP `#FF4FD8`, OG `#53FC18`, Verified chip `#2A2A2E`
- Text primary / secondary / tertiary: `#FFFFFF` / `#A6A6AD` / `#6E6E76`
- Chat body text: `#E4E4E9`
- Error: `#FF4A4A`

### Example Component Prompts
- "Build the Kick watch page in SwiftUI: a 16:9 video player on top with a top-left LIVE pill (`#FF1F44` fill, white dot + 'LIVE' Inter 10pt weight 800) and a top-right viewer chip (`rgba(0,0,0,0.55)`, eye glyph + '24.8K'). Below: a streamer bar — 40pt circular avatar with a 2pt `#53FC18` online ring, name Inter 16pt weight 700 + a `#53FC18` verified check, 'Just Chatting · 24,812 watching' in Inter 12pt `#A6A6AD`, trailing a `#53FC18` Follow button (6pt radius, `#0E0E10` text weight 800). Below that, the chat panel fills the rest of the screen."

- "Create a Kick chat message row: horizontal flow of optional badge chip(s) → role-colored username (Inter 13pt weight 700) → ':' → message text (`#E4E4E9` Inter 13pt weight 400) with inline 18×18pt emote sprites (3pt radius). 9pt vertical gap, 14pt horizontal padding. Badge chips: MOD `#3EA6FF`/`#06121F`, SUB-with-month-# `#FFC700`/`#1A1500`, VIP `#FF4FD8`/`#2A0022` — 8pt weight 900, 1×4pt padding, 3pt radius. If the message @-mentions the current user, give the row a `#53FC18` @ 12% background + 2pt `#53FC18` left border."

- "Render a Kick live channel card: a 16:9 thumbnail with an 8pt radius, a top-left 'LIVE' tag (`#FF1F44`, white 8pt weight 800, 2×6pt padding, 3pt radius) and a bottom-left viewer chip (`rgba(0,0,0,0.6)`, white 9pt weight 700 '24.8K'). Below: a row of a 30pt avatar with a 2pt `#53FC18` ring + a VStack of title (Inter 13pt weight 700, 1 line, ellipsis) and 'NovaPlays · FPS' (Inter 11pt `#A6A6AD`). 2-column grid, 12pt gutter."

- "Build the Kick chat input dock: a 38pt-tall `#1F1F23` field (8pt radius, 12pt horizontal padding, placeholder 'Send a message' Inter 13pt `#6E6E76`) with a trailing 38×38pt `#53FC18` send button (8pt radius, `#0E0E10` paper-plane glyph 16pt). Disabled state when empty: `#1F1F23` button, `#6E6E76` glyph. The dock has a 0.5pt `#2D2D31` top border and pushes up above the keyboard."

- "Build the Kick bottom tab bar: 52pt + safe area, `rgba(14,14,16,0.96)` with `backdrop-filter: blur(20px) saturate(180%)`, 0.5pt top border `#2D2D31`. Five tabs — Home, Browse, Following, Clips, Profile — 22pt icons, Inter 10pt weight 600 labels. Active tab icon + label `#53FC18` with the icon filled; inactive `#6E6E76`. No pill indicator."

- "Create the Kick Follow button states: default `#53FC18` fill, `#0E0E10` text Inter 15pt weight 800, 6pt radius, 44pt tall — on tap it morphs to 'Following' with a 200ms cross-fade + scale 1→1.06→1 + soft haptic, becoming `#1F1F23` fill, `#FFFFFF` text, 1pt `#2A2A2E` border. Pressed (while green): `#45D912` + scale 0.98."

### Iteration Guide
1. Canvas is near-black `#0E0E10` — NEVER pure `#000000` (true black makes the green strobe); dark-only
2. Kick Green `#53FC18` does *everything* brand/action — logo, all primary CTAs, active tab, links, online rings — and almost nothing else
3. Everything non-green is strictly monochrome grey (`#161618`/`#1F1F23`/`#2A2A2E`) so the green screams
4. The signature screen is **video → streamer bar → live chat panel** stacked vertically
5. Chat usernames are role-colored (mod `#3EA6FF`, sub `#FFC700`, VIP `#FF4FD8`, OG/regular green-ish) with tiny badge chips before them
6. Render custom emote sprites inline in chat at ~18pt; chat is dense (9pt gaps) and fast
7. The LIVE indicator is always red `#FF1F44` — never green; green is reserved for actions/brand
8. A 2pt `#53FC18` ring around an avatar = "live now"
9. Radii are tight (6pt buttons, 8pt cards) — Kick is bolder/rawer/less rounded than other streamers
10. Bottom tabs Home / Browse / Following / Clips / Profile — active icon green `#53FC18`, no pill
