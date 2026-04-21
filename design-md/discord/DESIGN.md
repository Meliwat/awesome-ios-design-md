# Design System Inspiration of Discord (iOS)

## 1. Visual Theme & Atmosphere

Discord's iOS app is a dark-first community UI built around one load-bearing color: Blurple (`#5865F2`). Open the app and you're dropped into a three-pane architecture — a leftmost server rail of rounded-square server icons, a channel list in the middle, and a message thread on the right — compressed into a mobile form factor via horizontal swipe navigation. The canvas is a near-black gradient of grays: the server rail at `#1E1F22`, the channel list at `#2B2D31`, the chat area at `#313338`. These three adjacent grays are so carefully tuned that they read as sibling surfaces rather than competing panels. The whole app feels like a gaming tool: purpose-built, dense, slightly nerdy, unapologetically dark.

The mood is community clubhouse, not broadcast platform. Presence is everywhere — a green (`#23A55A`) dot next to online users, yellow (`#F0B232`) for idle, red (`#F23F43`) for do-not-disturb, gray for offline. Role colors tint usernames throughout chat (admins in red, mods in green, VIPs in hot pink — whatever the server owner configures). Nitro-subscribed users get gradient avatars and animated badges. Custom emoji reactions flood threads during peak moments. The density of visual signals — per-user color, per-role hue, per-channel pings, per-user presence — means the UI has to stay chromatically restrained in its own chrome so server content can scream.

Typography is gg sans (Discord's proprietary font, replacing Whitney in 2023) for titles and UI, with SF Pro Text as the iOS fallback when gg sans isn't loaded. The type scale is compact — 14-20pt working range — because Discord expects many messages per screen. The single moment of chromatic excess is the Nitro Gradient: a purple-to-pink gradient (`#5865F2 → #EB459E`) used on Nitro badges, Boost badges, and the occasional promo banner. Everything else stays in the gray-and-blurple lane.

**Key Characteristics:**
- Dark-first canvas — Discord exists in dark mode by default, with carefully tuned gray siblings (`#1E1F22` / `#2B2D31` / `#313338`)
- Blurple (`#5865F2`) as the single brand accent — primary CTAs, active states, mentions
- Left server rail of 48pt rounded-square server icons with active-indicator bar
- Three-pane architecture: Server rail → Channel list → Chat — compressed via horizontal swipe on mobile
- Role colors on usernames throughout chat — any hex value, user-per-server custom
- Presence dots (green/yellow/red/gray) on every avatar — always visible
- Gradient avatars + Nitro badges for premium users
- Voice channels as first-class UI — tap to join, speaking ring pulses on active speakers
- Custom emoji + animated emoji reactions — heavy use, many per message
- Mention pings with red unread badge — hierarchy: @mention > role > unread
- Spoiler tags, code blocks, Markdown formatting rendered inline
- gg sans (proprietary, 2023+) with SF Pro fallback; compact 14-20pt working range

## 2. Color Palette & Roles

### Primary
- **Blurple** (`#5865F2`): THE brand color — primary CTAs, active send button, active tab indicator, mention highlight, link color, online-user voice-channel bubble.
- **Blurple Hover** (`#4752C4`): Pressed / hovered state for blurple CTAs.
- **Blurple Light** (`#7289DA`): Legacy brand blurple (pre-2021), still seen on occasional assets.
- **Nitro Gradient** (`#5865F2 → #EB459E`): Diagonal gradient used on Nitro badges, Boost icons, promo ribbons.

### Canvas & Surfaces (Dark — Default)
The three-pane gray system is Discord's signature surface language.
- **Server Rail (`#1E1F22`)**: The leftmost darkest pane — holds the rounded-square server icons.
- **Channel List Pane (`#2B2D31`)**: The middle pane — server name + channel list + member list preview.
- **Chat Canvas (`#313338`)**: The right pane where messages live. Slightly lighter so it reads as "the document".
- **Input Bar / Surface 2 (`#383A40`)**: Compose bar background, hovered channel row.
- **Elevated Modal (`#2B2D31`)**: Modal and popover surfaces match the channel-list pane tone.
- **Divider (`#3F4147`)**: Hairline separators.

### Canvas & Surfaces (Light — Rarely Used)
Discord does ship a light mode but ~80% of users stay on dark. Light mode is a straightforward inversion:
- **Light Canvas** (`#FFFFFF`)
- **Light Surface 1** (`#F2F3F5`)
- **Light Surface 2** (`#EBEDEF`)
- **Light Divider** (`#E3E5E8`)

### Text
- **Text Primary** (`#F2F3F5`): Message text, usernames (default, unroled), titles.
- **Text Secondary** (`#B5BAC1`): Usernames in member list, metadata, small labels.
- **Text Muted** (`#949BA4`): Timestamps, "was typing", inactive labels.
- **Text Link** (`#00A8FC`): Hyperlinks inside messages (distinct blue from Blurple).
- **Text Disabled** (`#5D6069`): Disabled labels, section subtitles.

### Status (Presence Dots)
- **Online Green** (`#23A55A`): Active users, successful actions.
- **Idle Yellow** (`#F0B232`): Away / idle status.
- **DND Red** (`#F23F43`): Do Not Disturb, error states, @mention pings.
- **Offline Gray** (`#80848E`): Offline or invisible users.
- **Streaming Purple** (`#593695`): Currently streaming / Go-Live active.

### Role Colors
Server admins define per-role hex colors — any value is valid. Common defaults in Discord's template system:
- Admin Red (`#ED4245`), Mod Green (`#57F287`), Booster Pink (`#EB459E`), VIP Gold (`#FEE75C`), Bot Aqua (`#23C1D2`).
Role color applies to the username in chat and in the right-side member list.

### Semantic
- **Success** (`#23A55A`): Successful actions, check states.
- **Warning** (`#F0B232`): Warning banners, voice-disconnect notice.
- **Danger** (`#F23F43`): Destructive actions, kick, ban, mention ping.
- **Nitro** (Nitro Gradient): Premium-only features, Nitro badge.
- **Boost** (Boost Pink `#EB459E`): Server Boost badge, Level 2/3 indicators.

## 3. Typography Rules

### Font Family
- **Primary (2023+)**: `gg sans` (proprietary, released 2023 to replace Whitney) — geometric sans with generous apertures, optimized for long-form chat scanning.
- **Previous (2017-2023)**: `Whitney` (Hoefler & Co.) — still seen on some legacy assets.
- **Monospace**: `gg mono` (2023+) for code blocks; formerly `Consolas`.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Helvetica Neue', Helvetica, Arial, sans-serif`
- **CJK**: Falls back to iOS system CJK fonts.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | gg sans | 20pt | 700 | 1.2 | -0.2pt | "General", server name at top of channel |
| Channel Name (Active) | gg sans | 16pt | 600 | 1.25 | -0.1pt | "#general" in channel list |
| Channel Name (Inactive) | gg sans | 16pt | 500 | 1.25 | -0.1pt | Unselected channels |
| Username | gg sans | 16pt | 600 | 1.2 | -0.1pt | Sender name above message, tinted with role color |
| Message Body | gg sans | 16pt | 400 | 1.375 | -0.1pt | The actual message text |
| Timestamp (Inline) | gg sans | 12pt | 500 | 1.2 | 0pt | Next to username in chat |
| Reply Context | gg sans | 14pt | 400 | 1.25 | -0.1pt | The replied-to message preview |
| System Message | gg sans | 14pt | 500 | 1.3 | 0pt | "Welcome @user to the server!" joins |
| Section Label | gg sans | 12pt | 700 | 1.2 | 0.5pt | "ANNOUNCEMENTS" — uppercase tracked |
| Member List Name | gg sans | 14pt | 500 | 1.2 | -0.1pt | Right-side member list |
| Button (Primary) | gg sans | 14pt | 500 | 1.0 | 0pt | "Join", "Continue" |
| Tab Label | gg sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Code Block | gg mono | 14pt | 400 | 1.4 | 0pt | Triple-backtick code block |
| Inline Code | gg mono | 14pt | 400 | 1.0 | 0pt | Backtick-wrapped inline code |
| Blockquote | gg sans | 16pt | 400 | 1.4 | -0.1pt | `> quote` rendered with leading bar |

### Principles
- **Compact working range**: Most UI sits at 14-16pt. 20pt is the largest non-display size.
- **Role colors on usernames**: Per-server username coloring from role assignments — applies in chat and member list.
- **Weight used sparingly**: 400 for body, 500 for labels, 600 for active states, 700 only on screen titles and section labels.
- **Markdown inline rendering**: `**bold**`, `*italic*`, `__underline__`, `~~strikethrough~~`, `` `code` ``, `||spoiler||`, `> quote` — all rendered inline by default.
- **gg mono for code**: Monospace shifts to `gg mono` inside code blocks, inheriting the 14pt / 1.4 line-height.

## 4. Component Stylings

### Buttons

**Primary Blurple Button**
- Shape: Rectangle, 8pt corner radius (or 4pt for compact inline)
- Background: `#5865F2`
- Text: white, gg sans 14pt weight 500
- Padding: 10pt vertical, 16pt horizontal (standard); 8pt / 12pt compact
- Pressed: `#4752C4` + scale 0.97
- Used for: "Continue", "Send Friend Request", "Join Voice"

**Secondary Button**
- Background: `#4E5058` (dark gray)
- Text: white, gg sans 14pt weight 500
- Pressed: `#6D6F78`
- Used for: "Cancel", "Maybe Later"

**Destructive Button**
- Background: `#DA373C`
- Text: white, same typography as primary
- Used for: "Leave Server", "Delete Message", "Kick User"

**Tertiary / Ghost Button**
- Background: transparent
- Text: `#5865F2` (Blurple) for affirmative, `#F23F43` for destructive
- No border, no fill — text link style

**Icon Actions (Pin, Thread, Reply, Reaction, Share)**
- Size: 20pt glyph, 36pt hit area
- Default: `#B5BAC1`
- Hover/active: `#F2F3F5`
- Active role (e.g. pinned): `#5865F2`

**Send Button (Compose Bar)**
- Shape: Icon only (no container) when message short; becomes a `#5865F2` circle 32pt when focused
- Paper-plane icon 20pt `#5865F2` or white (on filled circle)

### Cards & Containers

**Server Icon (Left Rail)**
- Shape: Rounded square, 16pt corner radius default; transitions to 12pt corner radius on hover/active (the "squircle-to-squircle-er" effect)
- Size: 48pt × 48pt
- Padding between icons: 8pt vertical
- Active indicator: 4pt × 40pt vertical bar on the leading edge, white fill, 2pt from icon
- Unread indicator: 8pt × 8pt white dot on leading edge (shorter than active bar)
- Mention indicator: same position as unread dot, `#F23F43` red, with white digit count
- Custom server image OR initials (first 1-2 letters of server name) on Blurple gradient default background
- Folder grouping: servers can nest inside folders — folder icon is a 2x2 mini-grid of server icons

**Channel List Row**
- Height: 36pt
- Structure: leading `#` (text channel), `🔊` (voice), `📢` (announcement), or custom icon → channel name → trailing pin icon + member count (voice only)
- Background: transparent default, `#404249` on hover, `#35373C` on active
- Active channel: background `#404249` + white name + vertical leading accent bar `#5865F2` 3pt
- Unread: channel name in white + a small white dot at the far-left
- Mentioned: channel name in white + red "@" pill with count trailing

**Message Row**
- Layout: 40pt avatar (leading, 16pt inset) → 12pt gap → (username + timestamp inline on first line; message body below)
- Grouping: sequential messages from same user within 5 min skip the avatar + username and just indent
- Padding: 4pt vertical (grouped) / 16pt vertical (first of group)
- Background: transparent default, `#2E3035` on hover (desktop — mobile long-press instead)
- Reply quote: nested above message, with leading vertical bar in that sender's role color

**Voice Channel (Active)**
- Channel row expands to show active participants
- Each participant: 24pt avatar + name in role color + speaking ring
- Speaking ring: 2pt green `#23A55A` ring around avatar, pulses scale 1.0 → 1.1 when mic is active
- Join button: "Join Voice" pill with `#23A55A` background, white text
- Already joined: green `#23A55A` phone icon indicator

**Member List Row (Right Pane)**
- Height: 44pt
- Structure: 32pt avatar with presence dot (8pt, position bottom-right) → username in role color → status text 12pt muted below
- Presence dot: bottom-right overlay, 2pt border matching canvas color

### Navigation

**Bottom Tab Bar (Mobile)**
- Height: 50pt + safe area
- Tabs: Servers (default), Messages (DMs), Notifications, You (profile)
- Icon size: 22pt
- Active color: `#F2F3F5` (white)
- Inactive: `#949BA4`
- Labels: gg sans 10pt weight 600
- Background: `#1E1F22` (matches server rail)

**Server Rail Navigation (Mobile)**
On mobile, the "server rail" is a left-edge drawer — swipe right from any chat to reveal the rail + channel list. Swipe left to close.
- Drawer width: ~75% of screen
- Left edge: 72pt server rail with vertical scroll
- Right side of drawer: 250pt channel list
- Tap a server icon: channel list re-populates for that server
- Tap a channel: drawer closes, chat loads

**Chat Top Nav**
- Height: 48pt + safe area
- Leading: hamburger / server-rail toggle + channel icon + channel name
- Trailing: pinned-messages icon + member-list icon + search icon
- Background: `#1E1F22` with 1pt bottom divider `#3F4147`

### Input Fields

**Compose Bar**
- Height: ~52pt min, grows multiline
- Background: `#383A40` (Surface 2) — intentionally 1 step lighter than chat canvas
- Corner radius: 8pt
- Leading + icon (attach file / media — 24pt `#B5BAC1`)
- Trailing: gift icon + GIF picker + sticker picker + emoji picker (each 22pt)
- Send icon: paper-plane 20pt `#5865F2` when text present, gray when empty
- Placeholder: "Message #general", `#949BA4`
- Focus: subtle `#5865F2` glow ring (2pt)

**Search Bar**
- Background: `#1E1F22`
- Corner radius: 8pt
- Height: 36pt
- Leading magnifyingglass `#B5BAC1`
- Placeholder: "Search", `#949BA4`

### Distinctive Components

**Server Rail (The Defining Discord Component)**
The leftmost pane of rounded-square server icons is THE visual signature of Discord.
- Background: `#1E1F22`
- Width: 72pt on mobile drawer (or ~75pt on iPad)
- Structure:
  - Home button at top: 48pt Discord logo icon, `#5865F2` gradient background when active
  - Horizontal divider below Home: 32pt line `#3F4147`
  - Server icons scroll vertically, 48pt × 48pt with 8pt gaps
  - "+" add server button at bottom: 48pt circle with `#23A55A` green plus icon
  - Explore Servers button below +: 48pt circle with compass icon
- Active server: 4pt × 40pt white vertical accent bar on left edge, and icon corner radius morphs from 16pt to 12pt (squircle effect)
- Unread server: shorter 4pt × 8pt white dot
- Mention pill: trailing `#F23F43` circle with number count

**Gradient Avatar (Nitro)**
Nitro subscribers can have animated or gradient borders on their avatar.
- Gradient ring: 2-3pt wide, animating hue rotation over ~8s
- Default Nitro gradient: `#5865F2 → #EB459E → #FBB848` diagonal
- Non-Nitro avatar: solid circular border matching role color (or transparent)

**Presence Dot**
- Size: 10pt diameter
- Position: bottom-right of avatar
- Border: 2pt solid matching canvas color (so it "punches through" avatar edge)
- States: `#23A55A` (online), `#F0B232` (idle), `#F23F43` (dnd), `#80848E` (offline), `#593695` (streaming — purple with a TV glyph inside)

**Role-Colored Username**
Username text in chat and member list inherits the user's highest-priority role color. This is applied per-message, per-server.
- Implementation: tint `.foregroundStyle` based on user's top role
- Fallback: `#F2F3F5` if no role or color is white

**Emoji Reaction Chip**
- Small pill below a message showing which reactions have been added
- Pill background: `#2B2D31` default, `#404249` hover, and `#3B4EAF` when YOU reacted (Blurple tint)
- Pill border: 1pt, same color tint scheme
- Structure: 20pt emoji + count 13pt weight 500
- Tap to add/remove your reaction; long-press to see who reacted
- "+" add-reaction button at end of row when space permits

**Mention Ping Badge**
- Red `#F23F43` circle on channel icons, server icons, and tab bar
- White digit count inside at 11pt weight 700
- Max display: "99+" for counts over 99

**Spoiler Tag**
- Inline `||text||` renders as a dark blurred pill; tap to reveal
- Blurred: `#2B2D31` fill with blur filter
- Revealed: text in `#F2F3F5` with no background

**Code Block**
- Triple-backtick (``` ```) renders full-width block with:
  - Background `#2B2D31`
  - 1pt border `#3F4147`
  - 8pt corner radius
  - Padding 12pt
  - Font gg mono 14pt
  - Language label (optional) top-right in `#949BA4`
  - Syntax highlighting for common languages (JS, Python, etc.)

**Slash Command Picker**
- When user types `/` in compose bar, a popover rises with available commands
- Background: `#2B2D31`, 8pt corner radius, 12pt blur shadow
- Row: command name + description + example
- Keyboard-navigable (arrow up/down)

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 56, 72
- Message row gap: 4pt (grouped) / 16pt (ungrouped)
- Server icon gap: 8pt
- Standard horizontal margin in chat: 16pt

### Grid & Container
- Mobile: single active pane (server rail / channel list / chat) swaps via swipe
- iPad: two or three panes visible simultaneously in landscape; single pane in portrait
- Chat message max-width: full width of pane (no constraint — messages wrap naturally)
- Member list pane: 240pt fixed width

### Whitespace Philosophy
- **Dense by design**: This is a chat app where users read hundreds of messages. Padding is tight — 4pt vertical on grouped messages, 16pt horizontal between panes.
- **Surface siblings**: The three grays (`#1E1F22`, `#2B2D31`, `#313338`) are deliberately close-valued so they read as related surfaces. Do not exaggerate the contrast.
- **Avatar-gutter standard**: The 40pt avatar + 12pt gutter is the message-row alignment grid — all attached content (reply quotes, embeds, reactions) aligns to the 52pt inset.

### Border Radius Scale
- Square (0pt): Occasional divider rails
- Soft (4pt): Inline code, compact buttons
- Standard (8pt): Buttons, input fields, modals, code blocks
- Card (12pt): Active server icons (squircle transition)
- Server Icon Default (16pt): Rounded square server icons
- Full Circle (50%): Avatars, presence dots, send-button circle, voice ring

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (0) | No shadow | Chat messages, channel list rows |
| Subtle Hover (1) | `rgba(0,0,0,0.15)` background tint | Message row hover |
| Modal (2) | `rgba(0,0,0,0.4) 0 8px 24px` | Modals, slash command popover |
| Sheet (3) | `rgba(0,0,0,0.5) 0 -16px 48px` | Bottom sheets, context menus |
| Modal Overlay | `rgba(0,0,0,0.6)` backdrop blur 20 | Modal scrim, full-screen overlay |

**Shadow Philosophy**: Discord is aggressively flat. The three-gray surface system provides visual hierarchy; shadows are reserved for genuinely floating elements (modals, popovers, bottom sheets). Server icons have no shadow. Messages have no shadow. The compose bar has no shadow. Only the context menu and modal popovers lift visibly.

### Motion
- **Server icon tap**: Scale 0.92 → 1.0 spring, corner radius morphs 16pt → 12pt (squircle effect)
- **Active indicator bar**: Slides vertically from previous position to new on server switch (200ms spring)
- **Speaking ring**: 2pt green ring around avatar, scales 1.0 → 1.15 → 1.0 pulse synced to audio amplitude
- **Message arrival**: Slides up 8pt with opacity fade-in (150ms)
- **Reaction add**: Chip scales 1.0 → 1.2 → 1.0 bounce with soft haptic
- **Drawer open (server rail swipe)**: 60fps pan with rubber-band resistance past edge
- **Gradient avatar animation**: 8s hue rotation loop (only for Nitro)
- **Typing indicator**: Three dots animating staggered opacity (300ms cycle each)
- **Spoiler reveal**: 200ms fade from blur to clear

## 7. Do's and Don'ts

### Do
- Use the three-gray surface system (`#1E1F22` rail → `#2B2D31` list → `#313338` chat) — the adjacency is the brand
- Use Blurple `#5865F2` ONLY as primary accent (CTAs, mentions, active states) — not decoratively
- Render server icons as 48pt rounded squares with 16pt corner radius default, 12pt on active
- Show the 4pt × 40pt white active-indicator bar on the leading edge of the selected server
- Use presence dots (green/yellow/red/gray) on every user avatar — always visible
- Apply role colors to usernames in chat — this is core to Discord's community identity
- Support full Markdown inline: bold, italic, underline, strikethrough, code, spoiler, quote
- Use gg sans (or SF Pro fallback) — compact 14-16pt working range
- Implement horizontal-swipe navigation between server rail / channel list / chat on mobile
- Show mention pings as red `#F23F43` badges with count — priority over plain unread dots

### Don't
- Don't use pure black canvas — Discord's dark is a specific gray system, not `#000000`
- Don't exaggerate the contrast between the three grays — they should feel sibling
- Don't add decorative shadows on messages or server icons — the app is flat
- Don't use Blurple as a body text color — it's structural, not typographic
- Don't use SF Symbols for channel icons — Discord has proprietary `#` / `🔊` / `📢` glyphs
- Don't use tab-bar style navigation for channels — Discord uses a side rail + list, not a tab bar
- Don't round server icons to full circles — the SQUARE with rounded corners is the brand
- Don't skip the presence dot — status visibility is core
- Don't use heavy-weight body text — 400 for body, 500 for labels, 600 for active
- Don't forget the squircle morph (16pt → 12pt) when a server icon becomes active

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Server rail drawer takes 85% of width; member list hidden |
| iPhone 13/14/15 | 390pt | Standard drawer at 75% width |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in nav |
| iPhone 15/16 Pro Max | 430pt | Drawer at 70% width |
| iPad | 768pt+ | Three panes visible in landscape: rail 72pt, channel list 240pt, chat flex-1 |

### Dynamic Type
- Message body, username, channel name: respect Dynamic Type scaling
- Timestamps, tab labels, presence dot size: fixed
- Code blocks: respect Dynamic Type but capped at 18pt

### Orientation
- Mobile portrait: single pane visible; swipe to switch
- Mobile landscape: two panes visible (channel list + chat)
- iPad: all orientations supported; three panes in landscape

### Touch Targets
- Server icon: 48pt × 48pt — generous enough
- Channel row: 36pt height
- Send icon: 36pt hit area
- Avatar tap in chat: 40pt + 8pt hitSlop = 48pt effective
- Reaction chip: 36pt hit area

### Safe Area Handling
- Top: safe area honored; Dynamic Island fits into the `#1E1F22` nav bar
- Bottom: tab bar and compose bar respect home indicator
- Side: server-rail drawer starts beyond safe area, slides in over content

## 9. Agent Prompt Guide

### Quick Color Reference
- Blurple: `#5865F2`
- Blurple Pressed: `#4752C4`
- Server Rail: `#1E1F22`
- Channel List: `#2B2D31`
- Chat Canvas: `#313338`
- Compose Bar / Surface 2: `#383A40`
- Divider: `#3F4147`
- Text Primary: `#F2F3F5`
- Text Secondary: `#B5BAC1`
- Text Muted: `#949BA4`
- Text Disabled: `#5D6069`
- Link: `#00A8FC`
- Online Green: `#23A55A`
- Idle Yellow: `#F0B232`
- DND Red: `#F23F43`
- Offline Gray: `#80848E`
- Streaming Purple: `#593695`
- Destructive Red: `#DA373C`
- Boost Pink: `#EB459E`
- Nitro Gradient: `#5865F2 → #EB459E`

### Example Component Prompts
- "Create the Discord server rail: vertical ScrollView, 72pt wide on mobile drawer, background #1E1F22. Top: 48pt home icon in Blurple gradient. 32pt divider below. Then 48pt rounded-square server icons (16pt corner radius default, 12pt when active) with 8pt gaps. Leading edge shows a 4pt × 40pt white active-indicator bar when selected. Mention pill: trailing #F23F43 circle with count. Bottom: 48pt green '+' and compass 'Explore' buttons."
- "Build a Discord message row: 40pt circular avatar with presence dot (10pt, bottom-right, 2pt canvas-colored border) on leading edge. Username in gg sans 16pt weight 600 tinted in role color (#ED4245 for admin, #57F287 for mod, fallback #F2F3F5), with inline 12pt weight 500 #949BA4 timestamp. Message body below in gg sans 16pt weight 400 #F2F3F5 at 1.375 line height. Support inline Markdown: bold, italic, spoiler (||text||), code (`text`). Hover: background #2E3035."
- "Design the Discord compose bar: full-width pill, background #383A40 (one step lighter than chat canvas #313338), 8pt corner radius. Leading: + attach icon 24pt #B5BAC1. Multiline TextField with placeholder 'Message #general' at #949BA4. Trailing cluster: gift, GIF, sticker, emoji icons (22pt each, #B5BAC1). Send paper-plane 20pt: #5865F2 when text present, gray when empty. On focus: 2pt #5865F2 glow ring."
- "Create a voice channel active row: 36pt row with leading 🔊 icon, channel name in gg sans 16pt weight 500, expands below to show participants. Each participant: 24pt avatar + username in role color + 2pt #23A55A speaking ring that pulses scale 1.0 → 1.15 → 1.0 when mic is hot. Trailing 'Join Voice' pill: #23A55A background, white gg sans 14pt weight 500."
- "Build a Discord emoji reaction chip row: horizontal scroll of reaction pills below a message. Each pill: 20pt emoji + 13pt count in gg sans weight 500. Background #2B2D31 default, #3B4EAF Blurple-tinted when YOU reacted, 1pt border matching. Pressed: scale 0.95 + soft haptic. Trailing '+ Add Reaction' button when space permits."
- "Design the Discord mobile bottom tab bar: 4 tabs (Servers, Messages, Notifications, You) with 22pt icons. Active tint #F2F3F5 (white), inactive #949BA4. gg sans 10pt weight 600 labels. Background #1E1F22 (matches server rail). Show #F23F43 red mention pill with white count on Notifications tab when mentions exist."

### Iteration Guide
1. Use the three-gray surface system: server rail `#1E1F22` → channel list `#2B2D31` → chat `#313338`. Compose bar is `#383A40` (one step lighter than chat).
2. Blurple `#5865F2` is the only brand accent — use for CTAs, mentions, active send button. NOT for decorative borders or body text.
3. Server icons are 48pt rounded SQUARES (16pt corner radius), not circles. Active state morphs to 12pt corner radius (the "squircle-er" effect) and shows a 4pt × 40pt white leading indicator bar.
4. Presence dots (10pt green/yellow/red/gray) on every avatar, bottom-right, with a 2pt canvas-colored border so they punch through the avatar edge.
5. Role-colored usernames in chat — each user gets the hex color of their highest-priority role per server. Implement as dynamic foreground tint.
6. Horizontal-swipe navigation on mobile: server rail + channel list as a left drawer; swipe right to open, swipe left to close. NOT a tab bar.
7. Render full Markdown inline: bold, italic, underline, strikethrough, code block (gg mono), inline code, spoiler (blurred), quote (leading bar), link.
8. Mentions `@user` render as Blurple-tinted pill inline in messages; mention pings show red `#F23F43` badges on channels, servers, and tab bar with count.
9. Typography: gg sans proprietary (fallback SF Pro). Compact 14-16pt working range. Weights 400, 500, 600, 700.
10. Discord is FLAT — no shadows on messages or icons. Shadows reserved for popovers and modals only.
