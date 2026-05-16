# Design System Inspiration of Twitch (iOS)

## 1. Visual Theme & Atmosphere

Twitch's iOS app is a live-first surface built on a near-black canvas (`#0E0E10`) that keeps the focus on streamer video and the constant motion of chat. The background is a true near-black with the faintest cool tint, and elevated surfaces (`#18181B`, `#1F1F23`) step up in cool grays so cards, the chat column, and sheets separate without borders. The interface is dense and kinetic: thumbnails are live previews, viewer counts tick, the LIVE pill pulses, and chat scrolls continuously. The chrome is deliberately quiet so the energy comes from content and community.

The accent system is a duo with strict roles. Twitch Purple (`#9146FF`) is the brand and primary-action color — Follow, Subscribe, the active tab, links, and focus. Live Red (`#EB0400`) is reserved exclusively for liveness — the "● LIVE" pill and the now-live indicator. The two never blur: purple is "act," red is "this is happening right now." Everything else is white, cool gray, or near-black. This split is the core of Twitch's visual language — community action vs. live status.

Typography is Roobert (Twitch's brand typeface), rendered here with an Inter fallback that keeps its rounded-grotesque warmth. The hierarchy is compact and functional: 11-28pt at weights 400 / 600 / 700. There are no oversized editorial headlines — the live video and the streamer's thumbnail are the spectacle. Type labels and identifies: stream titles at 15-16pt weight 600, channel names at 14-15pt weight 700, metadata (game, viewers) at 12-13pt weight 400 in gray, section headers at 20-22pt weight 700. The most expressive type moment is the channel page, where the streamer's display name sits at 22pt weight 700 beside a live avatar ring.

**Key Characteristics:**
- Near-black canvas (`#0E0E10`) with the faintest cool tint — quiet chrome, kinetic content
- Twitch Purple (`#9146FF`) for brand + primary action; Live Red (`#EB0400`) only for liveness
- Live thumbnail cards — preview image + red "● LIVE" pill + viewer count + game/category tag
- Stream view with a docked chat column — video on top, continuously scrolling chat below
- Channel card — circular avatar with a purple/red live ring, display name, category
- Emote chips — inline emote/badge tokens in chat and the emote picker
- Live chat overlay — the signature community surface, semi-transparent over video in theater/landscape
- Roobert typeface (Inter fallback), weights 400/600/700
- LIVE pill pulse and viewer-count ticking — motion signals "happening now"
- Bottom tab bar: Following, Browse, Search, Notifications, Profile

## 2. Color Palette & Roles

### Primary
- **Twitch Purple** (`#9146FF`): Brand color, primary CTA (Follow / Subscribe), active tab icon + label, links, focus ring, live avatar ring.
- **Purple Pressed** (`#772CE8`): Pressed/active state for purple CTAs and toggles.
- **Purple Glow** (`rgba(145,70,255,0.35)`): Soft glow under the primary CTA and around a focused live card.

### Liveness (Reserved)
- **Live Red** (`#EB0400`): The "● LIVE" pill and now-live indicator — used for nothing else.
- **Live Red Pressed** (`#C20300`): Pressed state if the LIVE pill is interactive (jump-to-live).

### Canvas & Surfaces
- **Canvas** (`#0E0E10`): Primary near-black app background — faint cool tint.
- **Deep Black** (`#000000`): Full-screen / theater video background.
- **Surface 1** (`#18181B`): Card backgrounds, sheets, the chat column, elevated rows.
- **Surface 2** (`#1F1F23`): Higher elevation — modals, active rows, chat input, chip default.
- **Surface 3** (`#2A2A2D`): Pressed state on dark surfaces, hairline divider tone.
- **Divider** (`#2A2A2D`): 1pt separators between settings rows and list blocks.

### Text
- **Text Primary** (`#EFEFF1`): Stream titles, channel names, screen headers (intentionally off-white, not pure `#FFFFFF`).
- **Text Secondary** (`#ADADB8`): Metadata (game · viewers), descriptions, section subtitles.
- **Text Tertiary** (`#6F6F7B`): Disabled labels, very low-emphasis captions, placeholder text.

### Semantic
- **Online Green** (`#00C16E`): "Online" presence dot on the Following list (channel currently up but not the LIVE pill).
- **Mention Purple Tint** (`rgba(145,70,255,0.20)`): Highlighted chat message background when you're @mentioned.
- **Sub Badge Gradient** (`#9146FF → #772CE8`): Subscriber-badge accent in chat.
- **Error Red** (`#EB0400`): Playback errors, stream-offline, connection-lost (shares the live red hue).

### Light Mode (Limited Use)
Twitch's iOS app is effectively dark-only — the live-streaming atmosphere depends on the near-black canvas. A light variant is not part of the core product.
- **Light Canvas** (`#FFFFFF`)
- **Light Text** (`#0E0E10`)

## 3. Typography Rules

### Font Family
- **Primary**: `Roobert` (Twitch brand typeface) — rounded grotesque with friendly terminals
- **Fallback Stack**: `'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for viewer counts, timestamps, and follower totals

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Roobert | 28pt | 700 | 1.15 | -0.4pt | "Browse", "Following" large nav title |
| Channel Name (Hero) | Roobert | 22pt | 700 | 1.2 | -0.3pt | Streamer display name on channel page |
| Section Header | Roobert | 20pt | 700 | 1.2 | -0.3pt | "Live channels", "Recommended" |
| Stream Title | Roobert | 16pt | 600 | 1.25 | -0.1pt | Stream/VOD title on a card or channel |
| Channel Label | Roobert | 15pt | 700 | 1.25 | -0.1pt | Channel name under a card |
| Body | Roobert | 15pt | 400 | 1.45 | 0pt | Stream "About", panel text |
| Chat Message | Roobert | 14pt | 400 | 1.35 | 0pt | Chat line body (username bolded inline) |
| Chat Username | Roobert | 14pt | 700 | 1.35 | 0pt | Colored display name in a chat line |
| Metadata | Roobert | 13pt | 400 | 1.3 | 0pt | "Just Chatting · 12.4K viewers" |
| Card Subtitle | Roobert | 12pt | 400 | 1.3 | 0pt | Game/category line under a thumbnail |
| Label (UPPER) | Roobert | 11pt | 700 | 1.2 | 0.6pt | "LIVE CHANNELS" eyebrow labels |
| Button (Primary) | Roobert | 15pt | 700 | 1.0 | 0.2pt | Purple "Follow" / "Subscribe" |
| Button (Secondary) | Roobert | 14pt | 600 | 1.0 | 0pt | "Following", outline buttons |
| Tab Label | Roobert | 10pt | 600 | 1.0 | 0.2pt | Bottom tab bar labels |
| Badge | Roobert | 11pt | 700 | 1.0 | 0.4pt | "LIVE", viewer-count pill, sub badge |

### Principles
- **Weights concentrated at 400 / 600 / 700**: Regular for body/metadata/chat, semibold for titles, bold for names, headers, CTAs — no thin, no medium, no black
- **Off-white primary text**: `#EFEFF1`, not pure `#FFFFFF` — softer on the near-black canvas for long chat-reading sessions
- **Density for community**: No hero display type; live video + scrolling chat are the energy
- **All-caps only for eyebrows and the LIVE badge**: never for titles or chat
- **Color split is structural**: purple = action/brand, red = liveness — never decorative, never swapped

## 4. Component Stylings

### Buttons

**Primary CTA (Follow / Subscribe — The Purple Button)**
- Shape: Rounded rectangle, height 40pt (auto-width) or full-width on sheets
- Background: `#9146FF`
- Text: `#FFFFFF`
- Icon: optional SF Symbol `heart.fill` (Follow) / `star.fill` (Subscribe) 14pt leading, `#FFFFFF`
- Corner radius: 6pt
- Font: Roobert 15pt weight 700, letter-spacing 0.2pt
- Shadow: `rgba(145,70,255,0.35) 0 6px 18px` on the channel-page primary CTA
- Pressed: background `#772CE8`, scale 0.97, with `.impactOccurred(.light)` haptic

**Following / Subscribed (active state)**
- Background: `#1F1F23`
- Text: `#EFEFF1` with a small leading `checkmark` or `heart.fill` in `#9146FF`
- Height: 40pt, corner radius 6pt
- Font: Roobert 14pt weight 600
- Pressed: background `#2A2A2D`

**Icon Actions (Share, Notifications bell, More)**
- Size: 22pt glyph, 44pt hit area
- Default: `#ADADB8`
- Active: `#9146FF` (notifications on, in a clip)
- Layout: trailing the channel header, 20pt gap

**Live / Jump-to-Live Pill (interactive variant)**
- Background: `#EB0400`
- Content: white `●` dot + "LIVE", Roobert 11pt weight 700 uppercase
- Height: 22pt, corner radius 4pt, horizontal padding 8pt
- Pressed: `#C20300`

**Text Button ("See all")**
- Font: Roobert 13pt weight 600
- Color: `#ADADB8`
- No underline, 44pt hit area, trailing each section header

### Cards & Containers

**Live Thumbnail Card (Horizontal rail / grid)**
- Width: 280pt (rail) or full-width minus margins (grid 1-col)
- Aspect: 16:9 live preview
- Corner radius: 6pt on the thumbnail
- Overlays on the thumbnail: red "● LIVE" pill top-left, viewer-count pill (`rgba(0,0,0,0.6)` + white `● 12.4K`) bottom-left
- Below: avatar (32pt circle) + stream title (16pt w600, 2-line clamp) + channel name (13pt w400 `#ADADB8`) + game/category tag (12pt w400 `#ADADB8`)
- Press: scale 1.03 with focus lift `rgba(0,0,0,0.5) 0 12px 28px` and a subtle purple ring; thumbnail begins live preview after ~600ms

**Channel Card (compact, Following list)**
- Layout: 44pt circular avatar with a 2pt purple ring (red ring if currently LIVE) → display name (15pt w700) + "playing Game · 8.1K" (13pt w400 `#ADADB8`) → trailing red LIVE pill or gray "offline" time
- Background: transparent (`#0E0E10`), pressed `#18181B`
- Height: 64pt

**Stream View (video + docked chat)**
- Top: 16:9 video player (full-bleed width), tap to reveal controls over a `rgba(0,0,0,0.5)` scrim
- Under video: channel bar — avatar + name + Follow/Subscribe purple CTA + viewer count + LIVE pill
- Below: docked **chat column** filling the rest of the screen — continuously scrolling messages on `#18181B`, chat input pinned at the bottom
- Landscape/theater: video goes full-screen and chat becomes a **semi-transparent overlay** on the right (`rgba(14,14,16,0.72)` with blur)

**Chat Message Row**
- Layout: inline badges (sub/mod/verified) → colored username (14pt w700, user's chosen chat color) → ": " → message body (14pt w400 `#EFEFF1`) → inline emote chips
- Padding: 4pt vertical, 12pt horizontal; no row background by default
- @mention to you: row background `rgba(145,70,255,0.20)` with a 2pt leading purple bar
- Emote: 24-28pt inline image token, baseline-aligned

**Chat Input**
- Background: `#1F1F23`, height 44pt, corner radius 8pt
- Leading emote-picker icon (`face.smiling`) 20pt `#ADADB8`, trailing send/`paperplane.fill` (purple when text present)
- Placeholder: "Send a message", 15pt w400 `#ADADB8`

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(14,14,16,0.94)` with `.regularMaterial` blur
- Tabs: Following, Browse, Search, Notifications, Profile
- Icon size: 24pt
- Active color: `#9146FF` (icon + label both turn purple — purple is the indicator)
- Inactive: `#ADADB8`
- Labels: Roobert 10pt w600, always shown
- Active tab icon: filled SF Symbol; inactive: outlined
- Notifications tab: red dot badge when unread

**Channel Header**
- 48pt + safe area, transparent over the video; back chevron left, Share + More right
- On scroll into the channel page: solid `rgba(14,14,16,0.94)` with display name centered

**Player Controls Overlay**
- Tap video → controls fade in over `rgba(0,0,0,0.5)`
- Center: 56pt circular `rgba(255,255,255,0.16)` play/pause; for live, a "● LIVE / jump to live" pill replaces the scrubber's end
- Bottom: live progress is a thin red line at the live edge; for VODs, a purple scrubber (2pt track `rgba(255,255,255,0.3)`, `#9146FF` fill, 14pt thumb)
- Top: title + close; bottom-right: quality, captions, chat-toggle, AirPlay

### Input Fields

**Search Bar**
- Background: `#1F1F23`
- Height: 44pt, corner radius 8pt
- Leading SF Symbol `magnifyingglass` 18pt `#ADADB8`
- Placeholder: "Search", 16pt w400 `#ADADB8`
- Text color: `#EFEFF1`
- Focus: 1.5pt `#9146FF` ring

**Category / Filter Chip**
- Background: `#1F1F23` default / `#9146FF` selected
- Text: `#EFEFF1` default / `#FFFFFF` selected
- Height: 36pt, corner radius 500pt (full pill), horizontal padding 16pt
- Font: Roobert 14pt weight 600

### Distinctive Components

**Live Chat Overlay**
- The signature Twitch surface: in theater/landscape, the docked chat column lifts off the layout and becomes a semi-transparent, blurred panel (`rgba(14,14,16,0.72)`) overlaid on the right third of full-screen video. Messages keep scrolling; the input floats at the bottom with a "hide chat" affordance. This is community layered directly onto content.

**Live Thumbnail Card Stack**
- Preview thumbnail + red "● LIVE" pill (top-left) + black viewer-count pill `● 12.4K` (bottom-left) + game tag (below). On focus the still becomes a muted live preview after ~600ms — the grid is alive.

**Channel Live Ring**
- The streamer's circular avatar carries a 2pt ring: purple when the channel is the focus/brand, switching to `#EB0400` when they are currently LIVE — presence encoded in the avatar.

**Emote Chips**
- Inline emote and badge tokens (24-28pt) in chat and a bottom-sheet emote picker; sub badges use the purple gradient. Emotes are first-class citizens of the type system, baseline-aligned with text.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 52, 64
- Standard margin: 16pt horizontal
- Between sections: 24pt vertical
- Card gap: 16pt (rails), 12pt (grid)
- Chat line spacing: 4pt vertical (tight — chat is high-density)

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Horizontal rails: begin at 16pt left inset, peek last card at right edge
- Browse grid: 1-column full-width live cards (portrait), 2-column on large devices
- Stream view: video (16:9) fixed top + chat column flex-fill below

### Whitespace Philosophy
- **Dense chat, breathing cards**: Chat is intentionally tight (4pt lines) for throughput; live cards get 16pt gaps so each preview reads
- **Video is sacred**: The stream player owns its 16:9 untouched; chat and chrome arrange around it
- **Quiet chrome**: Generous margins on nav and headers keep attention on content and community

### Border Radius Scale
- Sharp (0pt): Full-bleed / theater video surface
- Tight (4pt): LIVE pill, viewer-count pill, sub badges
- Soft (6pt): Thumbnails, primary CTA, cards
- Standard (8pt): Search bar, chat input, category chips (when not full-pill)
- Comfortable (12-16pt): Bottom sheets, emote picker
- Full Pill (500pt): Category/filter chips, follower-count pills
- Circle (50%): Avatars, player center button

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, rails, chat, list rows |
| Card Focus (Level 1) | `rgba(0,0,0,0.5) 0 12px 28px` | Live card lifted on press/focus |
| Card (Level 2) | `rgba(0,0,0,0.4) 0 6px 18px` | Sheets, channel panels |
| Purple Glow (Accent) | `rgba(145,70,255,0.35) 0 6px 18px` | Under the channel-page CTA, focused-card purple ring |
| Chat Overlay | `rgba(14,14,16,0.72)` + backdrop-blur 24 | Theater-mode chat panel over full-screen video |
| Sheet (Level 3) | `rgba(0,0,0,0.6) 0 -16px 44px` | Bottom sheets, emote picker |
| Tab Bar Material | `.regularMaterial` over `rgba(14,14,16,0.94)` | Bottom tab bar over scrolling content |

**Shadow Philosophy**: On the near-black canvas, neutral shadows must be deep (0.4-0.5 opacity, 18-28pt blur) to register. The signature "elevation" is actually translucency: the theater-mode chat overlay floats on a blurred 72%-opaque panel, and the purple glow under the channel CTA does the chromatic lifting. Everything else is flat — depth is cool-gray surface-step color, never borders.

### Motion
- **LIVE pill pulse**: continuous 1.6s opacity/scale pulse on the red dot (1.0 → 0.5 → 1.0)
- **Card press**: scale 1.0 → 1.03 over 180ms ease-out, focus shadow + purple ring; live preview fades in after ~600ms
- **Follow CTA tap**: scale 0.97 spring (damping 0.7), purple darkens, `.impactOccurred(.light)`
- **Chat autoscroll**: new messages slide up 8pt + fade in over 120ms; "chat paused" pill appears on manual scroll-up
- **Viewer count**: number rolls (odometer-style) on update
- **Theater toggle**: video expands to full-screen over 250ms; chat cross-fades from docked to overlay

## 7. Do's and Don'ts

### Do
- Use `#0E0E10` near-black canvas — faint cool tint, quiet chrome so content/chat carry energy
- Keep the color split strict: Twitch Purple (`#9146FF`) = brand/action; Live Red (`#EB0400`) = liveness only
- Use off-white `#EFEFF1` for primary text — softer than pure white for long chat reading
- Put the red "● LIVE" pill top-left and the black viewer-count pill bottom-left on live thumbnails
- Encode presence in the avatar ring — purple normally, red when currently LIVE
- Keep chat dense (4pt lines) and let live cards breathe (16pt gaps)
- In theater/landscape, float chat as a blurred 72%-opaque overlay on the right
- Make the active tab purple (icon + label) — purple is the indicator
- Treat emotes as first-class type tokens, baseline-aligned

### Don't
- Don't use pure black `#000000` for the app canvas — that's the theater/player surface only
- Don't use Live Red for anything except liveness — it must always mean "happening now"
- Don't use Twitch Purple for the LIVE state — purple is action/brand, never status
- Don't use pure white `#FFFFFF` for body text — `#EFEFF1` is the intentional softer choice
- Don't use thin or medium weights — Roobert here is 400 / 600 / 700 only
- Don't space chat loosely — community throughput depends on tight 4pt lines
- Don't introduce visible card borders — elevation is cool-gray surface-step + translucency
- Don't over-animate — card scale is subtle (1.03); the continuous motion is the LIVE pulse and chat scroll only

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 240pt rail cards; 1-col browse grid |
| iPhone 13/14/15 | 390pt | Standard 280pt rail cards |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in transparent channel header |
| iPhone 15/16 Pro Max | 430pt | 320pt rail cards; chat column taller in stream view |
| iPad | 768pt+ | 2-3 col browse grid; stream view = video + persistent side chat column |

### Dynamic Type
- Stream titles, channel names, chat messages, "About": full scale
- Card subtitle / metadata / viewer pills: scales but clamps at +2 steps (layout-sensitive)
- LIVE pill, tab labels, scrubber times: fixed (never scale)
- Chat: respects Dynamic Type but caps growth to keep throughput readable

### Orientation
- Following / Browse / Search / Channel: **portrait-locked**
- Stream view: **rotates to landscape → theater mode** — video full-screen, chat becomes the overlay
- Clips player: rotates to landscape

### Touch Targets
- Follow / Subscribe CTA: 40pt height — generous tap
- Live cards: full-card tappable, ≥ 110pt tall hit
- Channel rows: 64pt height, full-row tappable
- Chat send / emote picker: 44pt hit area
- Tab bar icons: 24pt icon, 44pt effective hit

### Safe Area Handling
- Top: transparent channel header respects safe area / Dynamic Island over the video
- Bottom: tab bar floats above home indicator; chat input docks above it; theater overlay insets from edges
- Sides: 16pt content margins; rails bleed to the right edge intentionally

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#0E0E10`
- Surface 1 (cards/chat): `#18181B`
- Surface 2 (sheets/input/chips): `#1F1F23`
- Surface 3 / Divider: `#2A2A2D`
- Primary text: `#EFEFF1`
- Secondary text: `#ADADB8`
- Tertiary text: `#6F6F7B`
- Twitch Purple (brand/action): `#9146FF`
- Purple pressed: `#772CE8`
- Live Red (liveness only): `#EB0400`
- Online green (presence): `#00C16E`

### Example Component Prompts
- "Create a SwiftUI Twitch live thumbnail card: 280×158pt (16:9) preview, 6pt corner radius. Overlay a red '● LIVE' pill top-left (#EB0400, Roobert 11pt weight 700 uppercase, white) and a rgba(0,0,0,0.6) viewer-count pill bottom-left ('● 12.4K' 11pt weight 700 white). Below: 32pt circular avatar + stream title 'Ranked grind to Masters' in Roobert 16pt weight 600 #EFEFF1 (2-line clamp) + channel name in 13pt weight 400 #ADADB8 + 'Just Chatting' game tag in 12pt #ADADB8. On press: scale 1.03 with shadow rgba(0,0,0,0.5) 0 12px 28px and a purple ring."
- "Build the Twitch Follow button: auto-width, 40pt tall, 6pt corner radius, background #9146FF, label 'Follow' in Roobert 15pt weight 700 #FFFFFF with a leading 14pt heart.fill. Channel-page glow shadow rgba(145,70,255,0.35) 0 6px 18px. Pressed: background #772CE8, scale 0.97, light haptic. Followed state: background #1F1F23, #EFEFF1 text with a purple checkmark."
- "Design the Twitch stream view: 16:9 video player at the top (full-bleed width), a channel bar under it (44pt avatar with a red live ring + display name in Roobert 22pt weight 700 #EFEFF1 + purple Follow CTA + '12.4K viewers' + red LIVE pill), then a docked chat column on #18181B filling the rest — chat lines at Roobert 14pt (colored bold username weight 700, message body weight 400 #EFEFF1, inline 24pt emote chips), chat input pinned at bottom on #1F1F23."
- "Create the Twitch theater-mode chat overlay: full-screen 16:9 video with a semi-transparent chat panel on the right third — background rgba(14,14,16,0.72) with backdrop blur 24, continuously scrolling messages, @mention rows highlighted with rgba(145,70,255,0.20) and a 2pt leading purple bar, a floating 'hide chat' button and chat input at the bottom."
- "Build the Twitch bottom tab bar: 52pt + safe area, background rgba(14,14,16,0.94) with regularMaterial blur. Tabs Following, Browse, Search, Notifications, Profile. Active icon + label both #9146FF (purple is the indicator), inactive #ADADB8. Labels Roobert 10pt weight 600, filled SF Symbol when active; red dot badge on Notifications when unread."

### Iteration Guide
1. Canvas is `#0E0E10` near-black with a faint cool tint — quiet chrome, content/chat carry the energy
2. Strict color split: Twitch Purple (`#9146FF`) = brand/action; Live Red (`#EB0400`) = liveness ONLY — never swap
3. Primary text is off-white `#EFEFF1`, not pure white — softer for long chat sessions
4. Live thumbnail = preview + red LIVE pill (top-left) + black viewer pill (bottom-left) + game tag
5. Encode presence in the avatar ring: purple normally, red when currently LIVE
6. Chat is dense (4pt lines); cards breathe (16pt gaps); emotes are first-class baseline-aligned tokens
7. Theater/landscape = video full-screen + chat as a blurred 72%-opaque right-side overlay
8. Typography is Roobert (Inter fallback), weights 400/600/700 only; active tab is purple
