# Design System Inspiration of Slack (iOS)

## 1. Visual Theme & Atmosphere

Slack's iOS app is defined by a single unmistakable color: **Slack Aubergine** (`#4A154B`), the deep wine-purple of the default left sidebar. That aubergine is the app's visual anchor — it's the first thing you see when you open Slack on mobile (a screen-filling aubergine pane if the sidebar is open), and it's the color that distinguishes Slack from every other messaging app. The 2019 rebrand simplified Slack's earlier rainbow logo into a more restrained four-color pinwheel (yellow, pink, green, blue), but internally the aubergine remains the workspace's "home" color and is carried over into the brand's custom typography. Critically, workspaces can customize this sidebar color in Preferences — a fuchsia workspace, a blue workspace, a monochrome black workspace — so the aubergine is the *default*, not a hardcoded brand.

The content surfaces outside the sidebar are clean white (`#FFFFFF`) in light mode or a near-black canvas (`#1A1D21`) in dark mode, giving Slack a two-zone visual structure: the saturated workspace sidebar on one side, the neutral conversation canvas on the other. Channel names (`#design`, `#general`) appear in the sidebar in a muted lavender-white (`#CFC3CF`) with full-white on the active channel. Unread channels get the bold treatment — channel name shifts to `#FFFFFF` bold, and a small count badge (for direct mentions) appears in bright red (`#E01E5A`, another Slack signature hue).

Messages are typographic and chunky — Slack uses **Slack Lato** (a proprietary face that evolved from Lato → Larsseit → today's Slack Lato / "Slack Sans" custom cut). Usernames appear in bold 15pt just above the message body, with timestamp in 12pt muted gray beside them. Messages breathe — generous vertical padding between turns, and generous internal 16pt side padding. Emoji reactions appear as inline chips below the message with emoji + count + blue outline when the current user has reacted. Threaded replies live in a pane that slides in from the right (or takes over the full screen on iPhone) — threads are as important as the main channel in Slack's model.

**Key Characteristics:**
- Slack Aubergine (`#4A154B`) as the signature sidebar color — workspace-customizable
- Two-zone layout: saturated workspace sidebar + clean content canvas
- 4-color brand pinwheel (yellow, pink, green, blue) — appears in the Slack logo and Huddle banners, rarely in UI chrome
- Unread channels: bold white + optional red mention badge (`#E01E5A`)
- Emoji reactions as inline pill chips below every message — with count and the current user's reaction highlighted blue
- Threads slide in from the right — threads are first-class, not secondary
- Huddles banner — blue (`#1264A3`) top strip when an audio huddle is active
- Status with custom emoji — every user can set a 30-character status + emoji next to their name
- Bottom tab bar: Home, DMs, Activity, Later, Search (5 tabs; Home tab = channel list + DMs)
- Mention colors: @channel = green pill, @here = yellow pill, @you = red pill
- Slack Lato typography — friendly geometric, 400 / 700 weights
- Online presence: small green dot (`#007A5A`) on avatar bottom-right

## 2. Color Palette & Roles

### Primary (Sidebar Default / Brand Anchor)
- **Slack Aubergine** (`#4A154B`): Default sidebar background — the signature color.
- **Aubergine Dark** (`#3F0E40`): Pressed state, darker sidebar tints on hover rows.
- **Aubergine Channel Text** (`#CFC3CF`): Channel name color on aubergine sidebar (muted lavender-white).
- **Aubergine Active** (`#FFFFFF`): Active channel name in sidebar — full white.
- **Aubergine Active BG** (`#1164A3` or workspace-custom): The highlighted row when a channel is selected on the sidebar. Default is a blue accent (`#1164A3`).

### Slack 4-Color Logo Palette (Used in Logo + Some Chrome)
- **Logo Yellow** (`#ECB22E`): Logo pinwheel yellow — also used in Huddle call notice and "NEW" flags.
- **Logo Pink** (`#E01E5A`): Logo pinwheel pink — also used for mention badges and urgent states.
- **Logo Green** (`#2EB67D`): Logo pinwheel green — also used for online presence dot.
- **Logo Blue** (`#36C5F0`): Logo pinwheel blue — also used for link color on aubergine surfaces.

### Canvas & Surfaces (Light Content Area)
- **Canvas Light** (`#FFFFFF`): Main conversation canvas — channel body.
- **Surface Light** (`#F8F8F8`): Search result rows, hovered list items, input field backgrounds.
- **Pressed Light** (`#E6E6E6`): Pressed states, selected rows.
- **Divider Light** (`#DDDDDD`): Hairline separators.
- **Thread Pane Background** (`#FFFFFF`): Same as main canvas when open.

### Canvas & Surfaces (Dark Content Area)
- **Canvas Dark** (`#1A1D21`): Main conversation canvas on dark mode — charcoal-gray, not black.
- **Dark Surface 1** (`#222529`): Elevated rows, input backgrounds.
- **Dark Surface 2** (`#2C2D30`): Thread pane, sheet backgrounds.
- **Dark Divider** (`#35373B`): Hairline separators on dark.

### Text
- **Text Primary Light** (`#1D1C1D`): Primary message body, user names.
- **Text Secondary Light** (`#616061`): Timestamps, metadata, channel topic.
- **Text Tertiary Light** (`#868686`): Placeholder, disabled.
- **Text on Aubergine** (`#FFFFFF`): Active channel in sidebar, workspace name.
- **Text on Aubergine Secondary** (`#CFC3CF`): Inactive channel names.

### Dark Mode Text
- **Text Primary Dark** (`#D1D2D3`): Primary message body on dark.
- **Text Secondary Dark** (`#ABABAD`): Timestamps.
- **Text Tertiary Dark** (`#6C6D6E`): Placeholder.

### Semantic & Status
- **Online Presence Green** (`#007A5A`): Small green dot on avatar when user online (a darker, more saturated green than the logo green — intentional for dot legibility).
- **Mention Pink** (`#E01E5A`): Mention badge (red pill with white count next to channel name if user is @-mentioned in that channel).
- **Typing Indicator Blue** (`#1264A3`): "Alice is typing..." indicator color + Huddles header strip.
- **Link Blue** (`#1264A3`): Inline links in messages.
- **Urgent Red** (`#E01E5A`): Destructive actions, error toasts.
- **Success Green** (`#007A5A`): Send-success flash.
- **@Channel Green BG** (`#F5E3BE` light / `#4A3D0D` dark): Yellow-green pill background for `@channel` mentions inline.
- **@Here Yellow BG** (`#FEF9E7` light / `#4A420D` dark): Yellow pill for `@here`.
- **@You Red BG** (`#F9D5DB` light / `#5A2731` dark): Red pill for `@yourname` mentions.
- **Reaction Chip (you reacted)** outline `#1264A3` with `#E3F2FC` background light.

### Workspace Sidebar Custom Colors (Examples of Preset Themes)
Slack lets users pick alternate sidebar themes. Common ones include:
- **Aubergine** (default): `#4A154B` / active `#1164A3`
- **Monument**: `#2C2D30` (dark gray) / active `#481349`
- **Tangerine**: `#CA4400` (orange) / active `#6B140C`
- **Ochre**: `#8B6A05` (mustard) / active `#481349`

Workspace admins can set a custom hex, so any sidebar color is possible.

## 3. Typography Rules

### Font Family
- **Primary (2022+)**: `Slack Lato` (proprietary; formerly `Lato` → `Larsseit` → today's custom cut, also marketed as "Slack Sans"). A rounded geometric sans with warm curves, humanist apertures.
- **Code / Mono**: `Operator Mono` (for some code blocks) or `Menlo` / `SF Mono` on iOS.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Helvetica Neue', Helvetica, Arial, sans-serif`
- **Non-Latin**: Slack ships script-specific fallbacks — Noto family for CJK/Devanagari/Arabic/Hebrew.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Workspace Name (Sidebar header) | Slack Lato | 18pt | 700 | 1.2 | -0.2pt | Top of sidebar |
| Channel Name (Sidebar, active) | Slack Lato | 15pt | 700 | 1.3 | 0pt | White bold for active |
| Channel Name (Sidebar, default) | Slack Lato | 15pt | 400 | 1.3 | 0pt | Muted lavender-white |
| Channel Name (Sidebar, unread) | Slack Lato | 15pt | 700 | 1.3 | 0pt | Full white bold for unread |
| Channel Header Title | Slack Lato | 17pt | 700 | 1.2 | 0pt | `# design` at top of channel |
| Channel Topic | Slack Lato | 13pt | 400 | 1.3 | 0pt | Gray subtitle below channel name |
| Username (Message) | Slack Lato | 15pt | 700 | 1.3 | 0pt | Bold sender name at top of message turn |
| Timestamp | Slack Lato | 12pt | 400 | 1.2 | 0pt | Muted gray beside username |
| Message Body | Slack Lato | 15pt | 400 | 1.5 | 0pt | Main message text |
| Thread Count | Slack Lato | 13pt | 600 | 1.0 | 0pt | "5 replies, last reply 3h ago" |
| Reaction Count | Slack Lato | 12pt | 600 | 1.0 | 0pt | Number next to emoji on reaction chip |
| Mention Text | Slack Lato | 15pt | 700 | 1.5 | 0pt | `@sarah` bold blue |
| Button (Primary) | Slack Lato | 15pt | 700 | 1.0 | 0pt | Green "Create channel" etc. |
| Button (Secondary) | Slack Lato | 14pt | 600 | 1.0 | 0pt | Outline buttons |
| Code Inline | Menlo | 13pt | 400 | 1.4 | 0pt | Code in message |
| Tab Label | Slack Lato | 11pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Meta / Small | Slack Lato | 11pt | 400 | 1.2 | 0pt | "Edited", "Sent from mobile" |
| Status (User) | Slack Lato | 13pt | 400 | 1.2 | 0pt | Emoji + 30-char status next to name |

### Principles
- **Two weights dominate**: 400 and 700 — Slack's type hierarchy is driven primarily by weight contrast, not size variation
- **Weight concentrated at 400 / 600 / 700**: Regular body, semibold for reactions/threads, bold for names/headings
- **Warm geometric feel**: Slack Lato has rounder terminals than pure grotesque — signals friendly, human tone
- **Uppercase only on a few UI labels**: "UNREADS", "MENTIONS & REACTIONS" section headers in sidebar — tracked 1.0pt
- **Dynamic Type first-class**: Body, message text, channel names scale; timestamps, reaction counts, tab labels fixed

## 4. Component Stylings

### Buttons

**Primary Button (Create channel / Submit)**
- Background: `#007A5A` (Slack Green — for send actions) OR `#4A154B` (aubergine — for workspace-context actions)
- Text: `#FFFFFF`
- Padding: 12pt vertical, 16pt horizontal
- Corner radius: 4pt (Slack buttons are modestly rounded, not full pills)
- Font: Slack Lato 15pt weight 700
- Pressed: darken 10% + scale 0.98 + medium haptic

**Secondary / Ghost Button**
- Background: transparent
- Border: 1pt solid `#1D1C1D` light / `#D1D2D3` dark
- Text: `#1D1C1D` light / `#D1D2D3` dark
- Padding: 10pt vertical, 16pt horizontal
- Corner radius: 4pt
- Font: Slack Lato 14pt weight 600
- Pressed: background `#E6E6E6`

**Icon Button (Header action / React / More)**
- 40pt hit area, 22pt SF Symbol
- Default color: `#616061` light / `#ABABAD` dark
- Pressed: background `#E6E6E6` light / `#35373B` dark (circular 36pt)
- Active (e.g., bell when notifications on): `#007A5A`

**Send Button (Message Composer)**
- Shape: square, 32pt, 4pt corner radius
- Background: `#007A5A` (green) when text entered, `#868686` (gray) when disabled
- Icon: SF Symbol `paperplane.fill` 16pt white
- Trailing position in composer
- Pressed: darken + medium haptic

### Messages & Reactions

**Message Row**
The core content atom of Slack.
- Background: `#FFFFFF` light / `#1A1D21` dark
- Horizontal padding: 16pt (larger on iPad)
- Vertical padding: 4pt top, 4pt bottom (but 12pt top margin before a new sender's first message — "collapsed" consecutive messages from same sender)
- Structure:
  1. Leading: 36pt avatar (rounded square 4pt corner, NOT circular — Slack's signature distinction from most chat apps)
  2. Content column:
     - Top line: username (bold 15pt) + "• status emoji + status" + timestamp (12pt `#616061`)
     - Message body (15pt w400, 1.5 line-height)
     - Inline attachments (images, file cards) full-width
     - Reactions row (emoji pill chips)
     - Thread indicator ("5 replies, last reply 3h ago" — blue text link)

- On hover (desktop) / long-press (mobile): reaction bar appears with quick-react emojis + "Add reaction" + "Reply in thread" + more

**Reaction Chip (Pill)**
- Shape: pill, 500pt corner radius
- Background: `#F1F1F1` default / `#E3F2FC` when you reacted light
- Border: 1pt `transparent` default / 1pt `#1264A3` when you reacted
- Padding: 4pt vertical, 8pt horizontal
- Content: 14pt emoji + 12pt w600 count text (separator 4pt gap)
- Font: Slack Lato 12pt weight 600 `#1D1C1D`
- Inline row below message body
- Tap: toggle your reaction + soft haptic

**Thread Indicator**
- Appears below message when thread exists
- Leading 16pt small avatar(s) of repliers (up to 3 overlapped)
- Text: "5 replies, last reply 3h ago" in Slack Lato 13pt w600 `#1264A3`
- Trailing chevron
- Tap: opens thread pane

**Mention Pill (inline in message)**
- Inline element within text flow
- Background: role-specific (green for @channel, yellow for @here, red for @user)
- Text: role-matched dark color
- Font: Slack Lato 15pt w700
- Padding: 1pt vertical, 4pt horizontal
- Corner radius: 4pt

**Huddles Banner (when audio huddle active)**
- Full-width banner at top of channel when huddle is live
- Background: `#1264A3` (Typing Indicator Blue)
- Height: 56pt
- Content: 4-color pinwheel icon + "Huddle in progress" + participant avatars overlap + "Join" button
- Haptic + sound on entry into a huddle

### Sidebar

**Workspace Header (top of sidebar)**
- Height: 56pt + safe area
- Background: aubergine `#4A154B` (or workspace-custom color)
- Leading: workspace icon — 32pt rounded square, 4pt radius (the workspace's custom logo or initial)
- Center: workspace name in Slack Lato 18pt w700 white + drop-down chevron
- Trailing: edit pencil icon (for workspace settings)

**Channel List Row (in sidebar)**
- Height: 32pt
- Padding: 4pt vertical, 16pt horizontal
- Background: transparent (aubergine visible); pressed = 10% black overlay; active = blue highlight `#1164A3`
- Structure:
  - Leading: channel-type icon — `#` for public, `🔒` for private, `@` for DM avatar (20pt), `mute` for muted
  - Name: Slack Lato 15pt — muted lavender `#CFC3CF` default, white bold if unread, full white if active
  - Trailing: mention badge (small red pill with count) if current user has mentions, OR muted icon if muted
- Sections:
  - "Starred" (if any starred channels)
  - "Channels" (# public)
  - "Direct messages" (avatars of DM partners)
  - "Apps"

**Sidebar Section Header**
- Uppercase label in Slack Lato 11pt w700 tracked 1.0pt
- Color: `#CFC3CF` at 50% opacity (subtle)
- Padding: 16pt top, 4pt bottom, 16pt horizontal
- Trailing: expand/collapse chevron + "+" add icon
- Examples: "CHANNELS", "DIRECT MESSAGES", "APPS"

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` / `#1A1D21` dark, with 0.5pt top border `#DDDDDD` / `#35373B`
- Tabs (5): Home, DMs, Activity, Later, Search
- Icon size: 24pt
- Active color: `#1D1C1D` light / `#D1D2D3` dark (active = bold icon, NOT a color shift)
- Inactive: `#616061` / `#ABABAD`
- Labels: Slack Lato 11pt w600, shown always
- Badges: red pill with white count on Activity and DMs tabs when unread

**Top Nav (Channel View)**
- Height: 52pt + safe area
- Leading: back chevron (24pt) or workspace avatar (32pt, taps opens sidebar)
- Center: channel name `# design` with leading `#` or `🔒` icon, Slack Lato 17pt w700 + topic/member count in 12pt w400 `#616061` below
- Trailing: headphone icon (start huddle) + "i" info icon + search icon

**Thread Pane (slides from right)**
- Full-screen on iPhone (takes over), slide-in pane on iPad (50% screen)
- Top: "Thread" label + back chevron + "Also send to #channel" checkbox
- Body: original parent message + replies below
- Slides in from right with 250ms ease-out; slides out on back swipe

### Input Fields

**Message Composer**
- Docked at bottom of channel / thread
- Background: `#FFFFFF` / `#1A1D21` dark
- Border: 1pt `#DDDDDD` / `#35373B` top
- Padding: 8pt horizontal, 8pt vertical
- Auto-growing text area: min 40pt height, max 200pt then scrolls
- Formatting toolbar above input: Bold / Italic / Strikethrough / Code / Link / Bulleted / Numbered / Quote / Code block (small icons, hidden by default, expand via "Aa" button)
- Leading "+" button (attach file / upload photo / mention)
- Trailing: emoji picker icon + "@" mention icon + microphone (voice note) + Send (green square when text present)

**Search Bar (Search tab)**
- Height: 44pt
- Background: `#F8F8F8` / `#222529` dark
- Border radius: 8pt
- Leading icon `magnifyingglass` 18pt `#616061`
- Placeholder: "Search workspace" Slack Lato 15pt w400 `#616061`
- Focus: 1pt `#1264A3` border
- Below search bar: recent searches, trending topics, people

**Channel Picker (long-press "+" in composer)**
- Bottom sheet listing channels to switch to, DM users, or "Create new channel"

### Distinctive Components

**Aubergine Sidebar**
The signature Slack component. Full-height left sidebar in `#4A154B` (or custom) with workspace header + channel list + DMs + apps. Swipe-right from anywhere in the app to open; swipe-left to close. Users who switch workspaces (shift down on the sidebar top) see a vertical strip of workspace icons to the left of the aubergine.

**Huddles Banner**
A blue (`#1264A3`) strip across the top of the channel when a huddle is active, showing participants and a Join button. Tap to join.

**Emoji Reaction Chip**
Below every message, a horizontal row of pill chips: emoji glyph + count. The chip you've reacted with has a blue outline (`#1264A3`). Tap to toggle your reaction; tap "+" to add a new reaction from the picker.

**Thread Indicator**
"5 replies, last reply 3h ago" link below a message with a thread — tap to open thread pane. Leading up to 3 avatars of repliers.

**Status with Emoji**
Next to a user's name in Slack (everywhere: sidebar, messages, mentions, profile), users can set a 30-character status + emoji — e.g., "🌴 On vacation until Mon" — that appears inline.

**Mention Pills (Inline)**
`@channel` (green), `@here` (yellow), `@you` (red) appear as colored pill backgrounds with bold text in the message body — bold and unmissable.

**Online Presence Dot**
A small 8pt green dot (`#007A5A`) on the bottom-right of a user's avatar when they're online. The avatar is a rounded square (4pt) — Slack's signature deviation from the chat-app circle.

**Saved Items ("Later" tab)**
Messages and items you've bookmarked — distinguished by a small bookmark icon on the message.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56, 72
- Message vertical gap (same sender, consecutive): 4pt
- Message vertical gap (new sender): 12pt
- Sidebar row: 32pt, 4pt vertical padding
- Bottom tab: 56pt

### Grid & Container
- iPhone: full-width messages, 16pt horizontal inset
- iPad: split-view — sidebar (280pt) + channel (center ~500pt) + thread pane (right 380pt when open)
- Sidebar width: 280pt on iPhone (full-width when swiped open), 260pt on iPad

### Whitespace Philosophy
- **Dense messages, breathing rhythm**: messages from same sender collapse closer together (4pt gap); new sender gets 12pt top margin — creating conversational "turns"
- **Aubergine is confident**: sidebar uses full color, no whitespace margins — it's meant to feel "owned" by the workspace
- **Thread pane is breathing room**: threads get same padding as main channel — equal first-class status

### Border Radius Scale
- Square (0pt): Huddles banner, full-bleed media
- Subtle (4pt): Avatars (rounded square — Slack's signature), buttons, message attachment cards
- Standard (8pt): Search bar, input fields, settings rows
- Comfortable (12pt): Bottom sheets, modals
- Pill (500pt): Reaction chips, mention pills, tab active pill
- Circle (50%): Online presence dot

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Sidebar, messages, channel body |
| Subtle (Level 1) | `rgba(0,0,0,0.08) 0 1px 4px` | Attachment cards, link preview cards |
| Floating (Level 2) | `rgba(0,0,0,0.12) 0 4px 16px` | Reaction picker, emoji picker, mention autocomplete |
| Sheet (Level 3) | `rgba(0,0,0,0.16) 0 -4px 24px` | Bottom sheets (compose, channel picker, status) |
| Modal Overlay | `rgba(0,0,0,0.4)` | Dim behind sheets and dialogs |
| Thread Pane | 1pt left border `#DDDDDD` | Divider between channel and thread (no shadow) |

**Shadow Philosophy**: Slack is flat by design — most surfaces have no shadow. Shadows appear only on ephemeral UI (reaction picker, emoji picker, bottom sheets) where the "this is floating" signal matters. The thread pane uses a 1pt border instead of a shadow because it's a structural divider, not an overlay. On dark mode, shadow opacity is reduced and a subtle border is added as a secondary signal.

### Motion
- **Sidebar open/close**: edge-swipe drags 1:1; threshold 50% commits with spring 250ms damping 0.8 + soft haptic
- **Message send**: send button shrinks briefly (scale 0.9 → 1.0 over 200ms) + text field clears + message "flies into" channel from bottom — scale 0.96 → 1.0 over 250ms
- **Reaction add**: emoji chip scales 0 → 1.1 → 1.0 spring 300ms + soft haptic
- **Thread pane open**: slide-in from right 250ms ease-out
- **Huddle join**: fullscreen fade from current context + haptic success + bell sound
- **Long-press message**: reaction picker + action menu appears with spring scale-in (250ms)
- **Typing indicator**: "Alice is typing..." text pulses opacity 1.0 ↔ 0.6 over 1.2s loop
- **Mention autocomplete**: dropdown appears below cursor with fade-in 150ms
- **Push to refresh**: standard iOS spinner (Slack doesn't customize)
- **Haptics**: soft on message send, light on reaction add, medium on huddle join, success on workspace switch

## 7. Do's and Don'ts

### Do
- Use Slack Aubergine (`#4A154B`) as the default sidebar color — it's the signature brand anchor
- Make sidebar color workspace-customizable (admin setting) — allow any hex
- Use rounded-square (4pt corner) avatars — NOT circles — Slack's distinct choice
- Surface reactions below every message as inline pill chips with emoji + count
- Show threads as first-class: "5 replies, last reply 3h ago" link + slide-in pane on tap
- Use mention pills inline — green `@channel`, yellow `@here`, red `@user`
- Use Slack Lato (or Lato fallback) at weights 400 / 600 / 700 only
- Place the Huddles banner at top of channel when active — blue `#1264A3`
- Support status + emoji next to every user name — 30-char status is part of the identity
- Green dot (`#007A5A`) on avatar bottom-right when online
- Make the thread pane full-screen on iPhone, side-pane on iPad

### Don't
- Don't use circle avatars — rounded-square (4pt) is Slack's signature
- Don't hardcode the sidebar color — it's workspace-customizable
- Don't use Slack aubergine anywhere except the sidebar (and login screens) — it's the sidebar's color
- Don't put mentions without the colored pill background — the pill IS the mention
- Don't ignore threads — they need a sliding pane, not a tap-through replacement of the main view
- Don't use Helvetica or SF Pro Display — Slack Lato's warmth is the voice
- Don't use Lato weights 300/500/900 — 400/600/700 only
- Don't animate reactions elaborately — a gentle spring scale is enough
- Don't use heavy shadows on messages — Slack is flat; shadows only on floating ephemeral UI
- Don't cram the channel name with decorations — leading `#` / `🔒` + name + bottom topic line is the canonical layout
- Don't mix @mention styles — each color is semantic and fixed

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Sidebar at 80% width when open |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | Larger avatar (40pt) and reaction row |
| iPad (portrait) | 768pt | 2-column: sidebar (260pt) + channel |
| iPad (landscape) | 1024pt+ | 3-column: sidebar + channel + thread pane (optional) |

### Dynamic Type
- Message body, usernames, channel names: full scale
- Timestamps, reaction counts, tab labels, small meta: FIXED (layout-sensitive)
- Username bold: scales
- Code inline: scales in monospace family

### Orientation
- iPhone: portrait-preferred; rotates for media + huddles
- Thread pane on iPhone = full takeover (rotates as main content)
- iPad: fully rotates with layout adapting

### Touch Targets
- Message row: full-width tap (opens thread or action sheet on long-press)
- Reaction chip: 28pt tall, 44pt effective hit
- Send button: 32pt square, 44pt hit
- Sidebar row: 32pt tall, full-row tap
- Tab bar: 24pt icon, 56pt effective

### Safe Area Handling
- Top: top nav respects safe area + Dynamic Island
- Bottom: tab bar + home indicator respected; composer rises above keyboard
- Sides: 16pt content inset on channel body; sidebar is edge-to-edge
- Huddle banner: sits directly under top nav, above channel scroll area

## 9. Agent Prompt Guide

### Quick Color Reference
- Sidebar (default): `#4A154B` (Aubergine)
- Sidebar pressed: `#3F0E40`
- Sidebar active row: `#1164A3`
- Channel text on sidebar (default): `#CFC3CF`
- Channel text on sidebar (active/unread): `#FFFFFF`
- Canvas light: `#FFFFFF`
- Canvas dark: `#1A1D21`
- Text primary: `#1D1C1D` light / `#D1D2D3` dark
- Text secondary: `#616061` light / `#ABABAD` dark
- Divider: `#DDDDDD` light / `#35373B` dark
- Online green: `#007A5A`
- Mention red: `#E01E5A`
- Huddle / typing blue: `#1264A3`
- Logo yellow: `#ECB22E`
- Logo pink: `#E01E5A`
- Logo green: `#2EB67D`
- Logo blue: `#36C5F0`
- Send button green: `#007A5A`

### Example Component Prompts
- "Build the Slack sidebar in SwiftUI: full-height left pane, 280pt wide, background `#4A154B` (aubergine). Workspace header at top: 56pt tall, leading 32pt rounded-square workspace icon (4pt radius), workspace name 'Acme Design' in Slack Lato 18pt w700 white, trailing chevron + edit pencil icon. Section 'CHANNELS' label in Slack Lato 11pt w700 tracked 1.0pt `#CFC3CF` 50% opacity, 16pt top padding. Channel rows (32pt tall): leading `#` icon 16pt `#CFC3CF`, channel name 'design' Slack Lato 15pt (w400 default `#CFC3CF`, w700 white for unread, w700 white for active on `#1164A3` row bg). Trailing mention badge (red pill `#E01E5A` with white count) if mentions present."

- "Create a Slack message row: 16pt horizontal padding, 4pt vertical padding (12pt top margin if new sender). Leading 36pt rounded-square avatar (4pt corner radius). Content column: username in Slack Lato 15pt w700 `#1D1C1D` + status emoji + 30-char status + timestamp '10:42 AM' in Slack Lato 12pt w400 `#616061`. Message body in Slack Lato 15pt w400 line-height 1.5. Below body: reaction chips row — each chip is pill 500pt radius, `#F1F1F1` bg (or `#E3F2FC` with 1pt `#1264A3` border if you've reacted), 14pt emoji + 12pt w600 count, 4pt vertical 8pt horizontal padding. Thread indicator below: 'View thread' as Slack Lato 13pt w600 `#1264A3` with leading 16pt overlapping avatars of repliers."

- "Design the Slack emoji reaction chip: pill 500pt radius, 4pt vertical + 8pt horizontal padding, 14pt emoji + 4pt gap + 12pt w600 count text. Default bg `#F1F1F1` with no border. You-reacted state: bg `#E3F2FC` with 1pt `#1264A3` border. Tap: toggle + soft haptic + chip scale 1.0 → 1.15 → 1.0 spring 300ms. Tap '+' button at end of chip row to open full emoji picker."

- "Build the Slack mention pill inline in a message: `@sarah` chip with bg `#F9D5DB` + text `#5A2731` (red for @user), OR `@channel` with bg `#D4EBD8` + text `#1C5A3D` (green), OR `@here` with bg `#FEF9E7` + text `#5D4A0D` (yellow). Font Slack Lato 15pt w700. Padding 1pt vertical 4pt horizontal, 4pt corner radius. Tap: opens user profile or shows channel context."

- "Create the Slack bottom tab bar: 56pt + safe area, `#FFFFFF` / `#1A1D21` dark, 0.5pt top border `#DDDDDD`. 5 tabs: Home (house icon for channels+DMs), DMs (chat bubbles), Activity (bell — with red badge if unread), Later (bookmark/clock), Search (magnifyingglass). 24pt icons. Active: `#1D1C1D` bold icon; inactive `#616061`. Slack Lato 11pt w600 labels. Red badge pill `#E01E5A` with white count on Activity tab when unread mentions exist."

- "Render the Slack Huddles banner at top of channel: full-width strip under top nav, 56pt tall, bg `#1264A3`. Leading: 4-color pinwheel logo icon 24pt (yellow/pink/green/blue quadrants). Center: 'Huddle in progress' in Slack Lato 15pt w700 white + 3 overlapping participant avatars (24pt rounded-square). Trailing: 'Join' button — white bg, 4pt radius, Slack Lato 14pt w700 `#1264A3`, 8pt vertical + 16pt horizontal padding. Tap: enters huddle full-screen."

### Iteration Guide
1. Slack Aubergine (`#4A154B`) is the default sidebar — NOT the canvas, NOT the accent. Workspaces can customize.
2. Avatars are rounded-square (4pt radius), NOT circles — Slack's unmistakable choice
3. Canvas is white (`#FFFFFF`) / dark charcoal (`#1A1D21`) — NOT aubergine on the conversation area
4. Channel text on sidebar: muted `#CFC3CF` default, bold white for unread, bold white for active (with blue `#1164A3` row bg)
5. Reactions are inline pill chips with emoji + count — blue outline `#1264A3` when you've reacted
6. Threads are first-class: "5 replies" link opens a slide-in pane (full-screen on iPhone, side on iPad)
7. Mentions have colored pill backgrounds inline: green for @channel, yellow for @here, red for @user
8. Typography is Slack Lato at weights 400 / 600 / 700 — NO light, NO black
9. Huddles banner is blue `#1264A3` across the top of the channel when active — 4-color pinwheel icon
10. Online presence = 8pt `#007A5A` green dot on avatar bottom-right (not a ring, a dot)
