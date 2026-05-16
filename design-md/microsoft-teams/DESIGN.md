# Design System Inspiration of Microsoft Teams (iOS)

## 1. Visual Theme & Atmosphere

Microsoft Teams' iOS app is an organizational nervous system rendered as an app. Its job is to collapse chat, meetings, files, and teams into a single dependable surface that an enterprise can live inside all day. The aesthetic is calm, structured, and Fluent — Microsoft's design language: soft surfaces, generous rounding, restrained color, and a strong sense of hierarchy. It ships with a true dual identity: a light appearance built on a near-white canvas (`#F5F5F5`) with white cards (`#FFFFFF`), and a dark appearance on a deep gray canvas (`#1F1F1F`) with charcoal cards (`#2D2C2C`). Neither is an afterthought — Teams is used in both, and the system is designed so every component reads correctly in each.

The accent is Teams Purple — `#6264A7` in light, lightened to `#5B5FC7` in dark for contrast. It marks the primary action (Join, Send, the active tab), the active selection, and the brand chrome. It is used with Fluent restraint: purple is a structural signal, not decoration. Presence is the one place Teams allows a small, deliberate burst of semantic color — the availability dot (green available, red busy/DND, yellow away, gray offline) is the most chromatically loaded element in the app because it carries real information that people scan for constantly.

Typography is Segoe UI — Microsoft's humanist sans — used at 400 / 600 / 700 across a 12–28pt range, closely approximated by Inter where Segoe is unavailable. The hierarchy is built for scanning long lists of people, channels, and messages: names and channel titles at 16pt semibold, message body at 15pt regular, timestamps and metadata at 12–13pt regular gray. The most structured typographic moment is the Teams → Channels tree, where team names, channel names, and the active-channel highlight form a clear three-level hierarchy.

**Key Characteristics:**
- Full dual identity — light (`#F5F5F5` / `#FFFFFF`) and dark (`#1F1F1F` / `#2D2C2C`), both first-class
- Teams Purple (`#6264A7` light / `#5B5FC7` dark) — primary action, active tab, brand chrome
- Presence dots — green / red / yellow / gray — the one place Teams uses loaded semantic color
- Teams → Channels tree — an expandable two-level list, the app's signature navigation surface
- Message cards with reactions and a reply/thread count — the unit of collaboration
- Meeting join bar — a prominent purple bar at the top of a channel/calendar when a meeting is live
- Bottom tab bar (Activity / Chat / Teams / Calendar / Calls) — Fluent, rounded, restrained
- Fluent rounding & soft surfaces — 8–12pt radii, subtle elevation, calm motion

## 2. Color Palette & Roles

### Primary
- **Teams Purple (Light)** (`#6264A7`): Primary CTA, active tab, send button, brand chrome — light appearance.
- **Teams Purple (Dark)** (`#5B5FC7`): The same role, lightened for contrast on the dark canvas.
- **Purple Pressed** (`#4F52B2`): Active/pressed state for purple buttons in both appearances.
- **Purple Tint** (`rgba(98,100,167,0.12)`): Selected-row / active-channel wash.

### Canvas & Surface (Light)
- **Light Canvas** (`#F5F5F5`): App background in light appearance.
- **Light Surface 1** (`#FFFFFF`): Cards, message bubbles, list rows, sheets.
- **Light Surface 2** (`#F0F0F0`): Pressed rows, grouped section backgrounds, input fill.
- **Light Divider** (`#E1E1E1`): Hairline dividers between rows.
- **Light Text Primary** (`#252423`): Names, titles, message body.
- **Light Text Secondary** (`#616161`): Timestamps, metadata, secondary labels.
- **Light Text Tertiary** (`#8A8886`): Disabled, placeholder, low-emphasis labels.

### Canvas & Surface (Dark)
- **Dark Canvas** (`#1F1F1F`): App background in dark appearance.
- **Dark Surface 1** (`#2D2C2C`): Cards, message bubbles, list rows, sheets.
- **Dark Surface 2** (`#3D3C3C`): Pressed rows, popovers, input fill.
- **Dark Divider** (`#3D3C3C`): Hairline dividers between rows.
- **Dark Text Primary** (`#FFFFFF`): Names, titles, message body.
- **Dark Text Secondary** (`#ADADAD`): Timestamps, metadata, secondary labels.
- **Dark Text Tertiary** (`#7A7A7A`): Disabled, placeholder, low-emphasis labels.

### Presence (the loaded semantic color)
- **Available** (`#6BB700`): Green dot — user is available.
- **Busy / In a Call** (`#C4314B`): Red dot — busy, in a meeting, or on a call.
- **Do Not Disturb** (`#C4314B`): Red dot with a white bar — notifications suppressed.
- **Away / Be Right Back** (`#FFAA44`): Yellow dot.
- **Offline / Unknown** (`#8A8886`): Hollow gray ring.

### Semantic
- **Mention Accent** (`#6264A7`): @mention highlight pill and "you were mentioned" markers.
- **Urgent Red** (`#C4314B`): Urgent message banner, missed-call indicator.
- **Success Green** (`#6BB700`): Success toast, "sent" confirmation.
- **Warning Amber** (`#FFAA44`): Connection warnings, away states.
- **Unread Dot** (`#6264A7`): Filled purple dot on a chat/channel with unread messages.

## 3. Typography Rules

### Font Family
- **Primary**: `Segoe UI` (Microsoft) — used at 400 / 600 / 700
- **Fallback Stack**: `'Segoe UI', -apple-system, BlinkMacSystemFont, 'Inter', 'SF Pro Text', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures on call duration and timestamps where alignment matters

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Segoe UI | 28pt | 700 | 1.2 | -0.3pt | "Chat", "Teams" nav large title |
| Section Header | Segoe UI | 20pt | 700 | 1.25 | -0.2pt | "Recent", grouped headers |
| Team Name | Segoe UI | 16pt | 700 | 1.3 | -0.1pt | Team title in the Teams tree |
| List / Channel Title | Segoe UI | 16pt | 600 | 1.3 | 0pt | Chat name, channel name, contact |
| Message Author | Segoe UI | 15pt | 600 | 1.3 | 0pt | Sender name above a message |
| Body / Message | Segoe UI | 15pt | 400 | 1.45 | 0pt | Message text, descriptions |
| Button (Primary) | Segoe UI | 16pt | 600 | 1.0 | 0pt | "Join", "Send", "Meet now" |
| Button (Secondary) | Segoe UI | 15pt | 600 | 1.0 | 0pt | Outline / ghost buttons |
| Metadata | Segoe UI | 13pt | 400 | 1.35 | 0pt | Timestamp, "3 replies", presence text |
| Channel Sub | Segoe UI | 13pt | 400 | 1.35 | 0pt | Last-message preview under a channel |
| Reaction Count | Segoe UI | 12pt | 600 | 1.0 | 0pt | Count next to a reaction emoji |
| Tab Label | Segoe UI | 10pt | 600 | 1.0 | 0.1pt | Bottom tab bar labels |
| Tiny Label (UPPER) | Segoe UI | 11pt | 700 | 1.2 | 0.4pt | "URGENT", small caps labels |
| Timestamp / Duration | Segoe UI | 12pt | 400 | 1.0 | 0pt | "12:34" call duration, tabular |

### Principles
- **Weights concentrated at 400 / 600 / 700**: Regular for body, semibold for names/titles, bold for headers — no light, no black
- **Names are semibold, messages are regular**: The eye finds the author first, then reads
- **Three-level tree typography**: Team (16pt bold) → Channel (16pt semibold) → preview (13pt regular gray)
- **Tabular numerics on durations/timestamps** where columns align (call logs, meeting times)
- **Minimal tracking**: Slight negative on titles; body and metadata sit at 0pt

## 4. Component Stylings

### Buttons

**Primary Button ("Join" / "Send" / "Meet now")**
- Background: `#6264A7` (light) / `#5B5FC7` (dark)
- Text: `#FFFFFF`, Segoe UI 16pt weight 600
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: 8pt
- Height: 44pt
- Pressed: background `#4F52B2`, scale 0.98, light haptic
- Disabled: background surface-2, text tertiary

**Secondary / Outline Button ("Chat" / "Decline")**
- Background: transparent
- Text: `#6264A7` (light) / `#5B5FC7` (dark), Segoe UI 15pt weight 600
- Border: 1pt solid current accent
- Padding: 10pt vertical, 20pt horizontal
- Corner radius: 8pt
- Pressed: background `rgba(98,100,167,0.12)`

**Icon Button (toolbar — search, new chat, filter)**
- Size: 22pt glyph, 44pt hit target
- Default: secondary text color
- Active/pressed: accent purple, surface-2 rounded background, 8pt radius

**Send Button (chat composer)**
- Circular, 32pt, `#6264A7` / `#5B5FC7` when text present; tertiary gray when empty
- White paper-plane glyph 16pt; subtle scale 0.92 on press

### Cards & Containers

**Message Card (the unit of collaboration)**
- Layout: 32pt avatar (with presence dot) → author name (15pt w600) + timestamp (13pt) → body (15pt w400) → reactions row → reply/thread count
- Background: `#FFFFFF` (light) / `#2D2C2C` (dark), corner radius 8pt
- Padding: 12pt; consecutive messages from the same author collapse the avatar
- Hover/long-press: reveals a reaction picker + quick actions toolbar
- @mention pill: inline, `rgba(98,100,167,0.12)` background, accent text, 4pt radius
- Reply count: "💬 3 replies · Last reply 2h ago" in 13pt secondary, tappable to open the thread

**Teams → Channels Tree Row (the signature element)**
- Team header row: 56pt — 32pt rounded team avatar + team name (16pt w700) + expand chevron
- Channel row: 44pt, indented 44pt — `#` glyph + channel name (16pt w600) + optional unread purple dot
- Active channel: background `rgba(98,100,167,0.12)`, name in primary text, a 3pt accent left bar
- Unread channel: name in bold + a trailing 8pt `#6264A7` dot
- Expand/collapse: chevron rotates 90°, children animate height over 200ms

**Chat List Row**
- Height: 72pt
- Layout: 48pt avatar + presence dot → name (16pt w600) + last message preview (13pt secondary, 1-line) → trailing timestamp (12pt) + unread purple dot
- Background: surface-1; pressed surface-2
- Unread: name and preview in primary weight, trailing filled purple dot

**Meeting Join Bar**
- Position: pinned at the top of a channel or calendar day when a meeting is live/upcoming
- Background: `#6264A7` / `#5B5FC7`, full width, 56pt tall, corner radius 12pt with 12pt side margins
- Contents: a video glyph + "Meeting in progress" / meeting title (white 15pt w600) + a white "Join" pill
- Pressed: darken to `#4F52B2`; the Join pill is white with purple text

**Calendar Event Card**
- Background: surface-1 with a 4pt accent left stripe (purple for Teams meetings)
- Time (13pt secondary) + title (16pt w600) + attendees row (overlapping 24pt avatars)
- A "Join" pill appears in the join window

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: surface-1 with a top hairline divider; subtle Fluent elevation
- Tabs: Activity · Chat · Teams · Calendar · Calls
- Icon size: 24pt; label 10pt w600
- Active: icon + label accent purple; a small filled-icon variant
- Inactive: secondary text color
- Activity tab shows a red badge with a count for new activity

**Top Bar**
- Height: 44pt + safe area
- Leading: profile avatar (28pt, with own presence dot) ; Center: screen title (20pt w700) ; Trailing: new-item + search/more (22pt)
- Background matches appearance; subtle blur on scroll in light

**Teams Tree Navigation**
- The Teams tab is a full-screen scrollable tree (teams → channels)
- Tapping a channel pushes the channel conversation; the active channel is highlighted in the tree on iPad's two-pane layout

### Input Fields

**Search Bar**
- Background: surface-2
- Height: 40pt, corner radius 8pt
- Leading `magnifyingglass` 16pt secondary
- Placeholder: "Search", 15pt w400 tertiary
- Focus: 1pt accent border, caret accent purple

**Chat Composer**
- A rounded container, surface-2 / 1pt divider border, 8pt radius, min 44pt height
- Leading: format / attach / emoji icons (22pt secondary)
- Placeholder: "Type a message", 15pt w400 tertiary
- Trailing: circular send button (accent when text present)
- Focus: border accent purple

**Inline Reply Field (in a thread)**
- Indented under the parent message, slightly smaller (40pt min), "Reply" placeholder

### Distinctive Components

**Teams → Channels Tree**
- The defining navigation surface — an expandable two-level list
- Level 1: a team (rounded avatar + bold name + chevron); Level 2: its channels (`#` + name)
- The active channel carries a 3pt purple left bar + tint wash; unread channels bold with a purple dot
- Expand/collapse animates child rows' height; state persists across sessions

**Presence Dot**
- An 10pt status dot anchored to the bottom-right of every avatar
- Available green `#6BB700`, busy/DND red `#C4314B` (DND adds a white bar), away yellow `#FFAA44`, offline a hollow gray ring
- It is the most information-dense pixel in the app — people scan it constantly; it is intentionally the loudest color

**Message Reactions**
- A row of emoji chips below a message: 👍 ❤️ 😂 😮 😢 👏
- Each chip: emoji + count (12pt w600) in a pill; the viewer's own reaction gets an accent-tinted background
- Long-press a message opens the reaction picker with a spring-scale entrance

**Reply / Thread Count**
- Under a message: "💬 3 replies · Last reply 2h ago" in 13pt secondary
- Tapping opens the threaded view; the parent message pins to the top

**Meeting Join Bar**
- A purple bar that appears contextually when a meeting is live; the most prominent CTA on the screen when present
- Pulses subtly (opacity 1.0 ↔ 0.85 on a 2s loop) to draw the eye without being noisy

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 44, 56
- Standard margin: 16pt horizontal; message padding 12pt
- Tree indentation: 44pt per level

### Grid & Container
- Content width: full device width, 16pt horizontal margins
- Chat / Teams / Calendar lists: single column
- iPad: two-pane — Teams tree (or chat list) on the left, conversation on the right
- Message bubbles: full-width cards (Teams uses cards, not chat bubbles, for channel posts)

### Whitespace Philosophy
- **Calm and structured**: Generous 12pt message padding and 8pt radii make a dense workday feel orderly, not cramped
- **Hierarchy over density**: The Teams tree uses indentation and weight, not tight rows, so the structure is always legible
- **The join bar earns its space**: When a meeting is live, the purple bar is allowed to be prominent — it is the moment that matters

### Border Radius Scale
- Sharp (0pt): Dividers
- Standard (8pt): Buttons, message cards, inputs, search
- Comfortable (12pt): Meeting join bar, sheets, modals
- Pill (500pt): Reaction chips, filter chips, the Join pill in the bar
- Circle (50%): Avatars, send button, presence dot

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, tree, canvas |
| Card (Level 1) | Light: `rgba(0,0,0,0.06) 0 1px 2px` · Dark: none, surface lift only | Message cards, calendar cards |
| Raised (Level 2) | Light: `rgba(0,0,0,0.12) 0 4px 12px` · Dark: surface-2 + 1pt divider | Popovers, the reaction picker |
| Sheet (Level 3) | Light: `rgba(0,0,0,0.16) 0 -8px 32px` · Dark: `rgba(0,0,0,0.5) 0 -8px 32px` | Bottom sheets, compose modal |
| Join Bar | Accent fill + `rgba(98,100,167,0.35) 0 4px 16px` | The live-meeting bar |
| Scrim | `rgba(0,0,0,0.4)` | Behind modals and the more menu |

**Shadow Philosophy**: Fluent uses soft, low-contrast shadows in light mode to lift cards a hair off the `#F5F5F5` canvas — never hard or dramatic. In dark mode, shadows are mostly replaced by a surface-color step (`#1F1F1F` → `#2D2C2C` → `#3D3C3C`) plus a 1pt divider, because shadows don't register on a dark canvas. The one element allowed a colored shadow is the meeting join bar — a soft purple glow that signals "this is the thing to tap right now."

### Motion
- **Primary button tap**: scale 0.98 → 1.0 spring (response 0.25, damping 0.85) + `.impactOccurred(.light)`
- **Tree expand/collapse**: chevron rotates 0°→90° while child rows animate height over 200ms ease-in-out
- **Reaction picker**: scale 0.9 → 1.0 + opacity spring (response 0.3, damping 0.8) on long-press
- **Message send**: composer clears, the new message slides up + fades in over 200ms
- **Presence change**: the dot cross-fades color over 250ms — never an abrupt switch
- **Join bar appear**: slide down + fade over 250ms; subtle 2s opacity pulse while live
- **Tab switch**: cross-fade content 150ms; the active icon swaps to its filled variant

## 7. Do's and Don'ts

### Do
- Support both light (`#F5F5F5` / `#FFFFFF`) and dark (`#1F1F1F` / `#2D2C2C`) as first-class
- Use Teams Purple (`#6264A7` light / `#5B5FC7` dark) for primary action, active tab, and brand chrome
- Make presence dots the one place you use loaded semantic color — people scan them constantly
- Render the Teams → Channels tree with clear three-level typography and a purple active-channel bar
- Use message cards (not chat bubbles) for channel posts, with reactions + reply count
- Show the purple meeting join bar prominently when a meeting is live; let it pulse subtly
- Keep Fluent rounding (8–12pt) and soft, low-contrast elevation
- Bold unread chats/channels and add a purple unread dot
- Use Segoe UI 400/600/700 — semibold for names, regular for messages

### Don't
- Don't treat dark mode as secondary — Teams is used heavily in both appearances
- Don't introduce a second brand accent — purple is the structural color
- Don't dilute presence colors or use those greens/reds/yellows for non-presence chrome
- Don't flatten the Teams tree into a single list — the two-level hierarchy is the navigation
- Don't use hard, dramatic shadows — Fluent elevation is soft and low-contrast
- Don't hide the meeting join bar when a meeting is live — it is the priority CTA
- Don't use light font weights — Segoe UI here is 400/600/700 only
- Don't use chat bubbles for channel posts — Teams uses full-width message cards
- Don't animate the presence dot abruptly — cross-fade the color

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 64pt chat rows, channel preview may truncate sooner |
| iPhone 13/14/15 | 390pt | Standard 72pt chat rows, full tree |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the 44pt top bar |
| iPhone 15/16 Pro Max | 430pt | Larger avatars (52pt), more preview text |
| iPad | 768pt+ | Two-pane: Teams tree / chat list left, conversation right; join bar spans the conversation pane |

### Dynamic Type
- Names, message body, channel titles: scale with Dynamic Type
- Presence dot: fixed 10pt (status, not text)
- Timestamps / call duration: fixed tabular 12pt (layout-critical)
- Tab labels: fixed 10pt; hide on very narrow widths rather than scale

### Orientation
- Chat / Teams / Activity: **portrait-primary**
- Calendar: **portrait and landscape**
- In-meeting (Calls): **rotates to landscape** for the video grid
- iPad: landscape unlocks the persistent two-pane layout

### Touch Targets
- Primary buttons & Join pill: 44pt height
- Tree rows: 44–56pt, full-row tappable
- Chat rows: full 72pt height
- Reaction chips: 32pt min hit area
- Tab bar icons: 24pt glyph, 44pt effective hit

### Safe Area Handling
- Top: top bar respects Dynamic Island / notch
- Bottom: composer and tab bar respect the home indicator; the join bar sits below the top safe area
- Sides: 16pt content insets; the join bar uses 12pt side margins

## 9. Agent Prompt Guide

### Quick Color Reference
- Light canvas / surface: `#F5F5F5` / `#FFFFFF`
- Dark canvas / surface: `#1F1F1F` / `#2D2C2C`
- Light text primary / secondary: `#252423` / `#616161`
- Dark text primary / secondary: `#FFFFFF` / `#ADADAD`
- Teams Purple (light / dark): `#6264A7` / `#5B5FC7`
- Purple pressed: `#4F52B2`
- Presence available: `#6BB700`
- Presence busy / DND: `#C4314B`
- Presence away: `#FFAA44`
- Presence offline: `#8A8886`

### Example Component Prompts
- "Create a SwiftUI Teams → Channels tree: a 56pt team header row with a 32pt rounded team avatar, team name 'Design Guild' in Segoe UI 16pt weight 700, and a rotating expand chevron. Below it, 44pt channel rows indented 44pt: a '#' glyph + channel name 'general' in 16pt weight 600. The active channel has a rgba(98,100,167,0.12) background, a 3pt #6264A7 left bar, and primary-color text; unread channels are bold with a trailing 8pt #6264A7 dot. Expanding rotates the chevron 90° and animates child height over 200ms."
- "Build the Teams message card: a #FFFFFF (light) / #2D2C2C (dark) card, 8pt corner radius, 12pt padding. Top row: 32pt avatar with a 10pt presence dot bottom-right, author 'Priya Anand' in Segoe UI 15pt weight 600, timestamp '10:42 AM' in 13pt secondary. Body 'Pushed the build, can someone review?' in 15pt weight 400. Below: a reactions row of emoji+count pills, then '💬 3 replies · Last reply 2h ago' in 13pt secondary."
- "Design the Teams meeting join bar: a full-width bar with 12pt side margins, 56pt tall, 12pt corner radius, background #6264A7 (light) / #5B5FC7 (dark), soft purple shadow rgba(98,100,167,0.35) 0 4px 16px. A video glyph + 'Weekly Sync · in progress' in white Segoe UI 15pt weight 600, and a trailing white 'Join' pill with purple text. It pulses opacity 1.0 ↔ 0.85 on a 2s loop while the meeting is live."
- "Create the Teams presence dot: a 10pt circle anchored bottom-right of a 32pt avatar. Available = #6BB700 solid, Busy/DND = #C4314B (DND adds a white horizontal bar), Away = #FFAA44 solid, Offline = a hollow #8A8886 ring. Changing state cross-fades the color over 250ms."
- "Build the Teams primary button: background #6264A7 (light) / #5B5FC7 (dark), text 'Join' in Segoe UI 16pt weight 600 #FFFFFF, 12pt vertical / 24pt horizontal padding, 8pt corner radius, 44pt tall. Pressed: background #4F52B2, scale 0.98, light haptic."

### Iteration Guide
1. Teams is dual-identity — design light (`#F5F5F5`/`#FFFFFF`) and dark (`#1F1F1F`/`#2D2C2C`) together, both first-class
2. Teams Purple is the structural accent — `#6264A7` light, `#5B5FC7` dark; primary action, active tab, brand chrome
3. Presence dots are the only loaded semantic color — green/red/yellow/gray — never reuse those hues for chrome
4. The Teams → Channels tree is the signature navigation — two levels, purple active-channel bar, expand/collapse
5. Channel posts are message cards (not bubbles) with reactions and a reply/thread count
6. The purple meeting join bar is the priority CTA when a meeting is live — prominent, softly pulsing
7. Fluent rounding (8–12pt) and soft, low-contrast elevation; surface steps replace shadows in dark mode
8. Typography is Segoe UI 400/600/700 — semibold names, regular messages, bold headers
