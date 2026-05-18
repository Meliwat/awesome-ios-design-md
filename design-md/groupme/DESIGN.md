# Design System Inspiration of GroupMe (iOS)

## 1. Visual Theme & Atmosphere

GroupMe's iOS app is a friendly, high-energy group-messaging surface built around one idea: a group chat should feel like a party everyone can drop into, not a serious work tool. The canvas is clean white (`#FFFFFF` light / `#121212` dark) and the entire personality of the app is carried by **GroupMe Blue (`#00AFF0`)** — a bright cyan-leaning blue that paints the chat navigation bar, the outbound message bubbles, the floating send button, and the active bottom tab. Open a group and the top bar is a solid block of that blue with the group's name and member count in white; this colored header is the single most recognizable piece of GroupMe chrome and instantly separates it from the gray-and-white restraint of iMessage or Signal.

The signature interaction is the **heart "like" on a message**. Double-tap any bubble (or tap the heart affordance) and a count badge docks just below the bubble's trailing corner in a small rounded pill — a red heart glyph (`#FF3B5C`) plus the number of likes. This is GroupMe's version of a reaction and it is everywhere: it's how the group cheers, votes, and acknowledges without flooding the thread. The other defining moment is **per-group theming** — every group can pick its own color theme and the app recolors avatar gradients and accent details to match, so the "Roadtrip" group can feel sunset-orange while "Family" stays classic blue, all while the core blue chrome and like interaction stay consistent.

Two screens define the product. The **group list (Chats tab)** is a vertical feed of group rows, each with a circular group avatar (often a generated gradient or a group photo), the group name in semibold, a one-line message preview, a timestamp, and an unread count pill in GroupMe Blue. The **group chat thread** is the heart of the app: inbound bubbles in neutral gray (`#F0F0F0` light / `#2A2A2C` dark) with the sender's avatar and colored name above the first bubble in a run, outbound bubbles in GroupMe Blue, like-count pills docked below bubbles, an inline image gallery grid for shared photos, and a pill-shaped composer with a `+` attachment button and a circular blue send button. The avatars are always circular and frequently colorful — GroupMe assigns each member a generated gradient avatar with their initial, which makes a busy group thread feel like a wall of friendly faces.

Typography is the iOS system face (SF Pro) — GroupMe does not ship a brand typeface; it leans on system text and lets color and the circular avatars carry the brand. Hierarchy is conversational: group names at 22pt bold, message body at 16pt regular, sender names at 12pt semibold in the group's accent color, timestamps and metadata at small gray sizes. The type system stays out of the way so the bubbles, hearts, and faces do the talking.

**Key Characteristics:**
- Bright GroupMe Blue (`#00AFF0`) chrome — colored chat nav bar, outbound bubbles, send button, active tab
- Clean white canvas (`#FFFFFF`) light / warm charcoal (`#121212`) dark — content-forward, friendly
- Heart "like" on messages — double-tap to like; count docks in a pill below the bubble's trailing corner
- Per-group color themes — each group recolors avatar gradients and accent details
- Circular generated-gradient member avatars with initials — a wall of friendly faces
- Colored sender name above the first bubble in a run (in the group's accent color)
- Inline image gallery grid — multi-photo shares render as a tappable 2×2 (or more) grid in a bubble
- Pill composer with `+` attachment button and circular blue send button
- Group list rows with circular avatar, name, preview, timestamp, unread pill
- Bottom tab bar: Chats / People / Discover / Profile — active tab in GroupMe Blue
- Emoji, GIFs, and a meme/sticker library as first-class message content
- Bubbles use an 18pt radius with the tail corner squared to ~5pt

## 2. Color Palette & Roles

### Primary (Interactive)
- **GroupMe Blue** (`#00AFF0`): The brand. Chat nav bar background, outbound message bubbles, send button, active tab, primary CTAs, unread count pills, links.
- **GroupMe Blue Pressed** (`#0091C7`): Pressed/active state on buttons and the send button.
- **GroupMe Blue Deep** (`#0077B5`): Deeper shade for gradient bottoms, pressed nav elements, splash backgrounds.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas — chat thread background, list background.
- **Surface Gray** (`#F7F7F8`): Grouped settings backdrop, search field fill, elevated rows.
- **Inbound Bubble Light** (`#F0F0F0`): Inbound message bubble fill.
- **Divider Light** (`#E4E4E6`): Hairline dividers between list rows and sections.
- **Row Pressed Light** (`#ECECEE`): Pressed list row.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#121212`): Primary dark canvas — NOT pure black; keeps the app warm.
- **Dark Surface 1** (`#1C1C1E`): Composer bar, like-pill background, grouped settings rows.
- **Dark Surface 2 / Inbound Bubble Dark** (`#2A2A2C`): Inbound message bubble fill, hovered rows.
- **Dark Divider** (`#2C2C2E`): Hairline dividers.

### Text
- **Text Primary Light** (`#1A1A1A`): Primary message and list text on light.
- **Text Secondary Light** (`#6A6A6A`): Message previews, timestamps, metadata.
- **Text Tertiary Light** (`#9A9A9A`): Disabled, very low-emphasis.
- **Text Primary Dark** (`#ECECEC`): Primary text on dark.
- **Text Secondary Dark** (`#A0A0A0`): Secondary text on dark.
- **Text Tertiary Dark** (`#6A6A6A`): Tertiary on dark.
- **Text On Blue** (`#FFFFFF`): Text inside the blue nav bar and outbound bubbles.

### Per-Group Theme Accents
GroupMe lets each group pick a color theme. These recolor avatar gradients, the sender name color, and accent details. The blue chrome stays constant.

| Theme | Color | Gradient Pair |
|-------|-------|---------------|
| Default (Blue) | `#00AFF0` | `#00AFF0` → `#0077B5` |
| Coral | `#FF6B6B` | `#FF6B6B` → `#FF8E53` |
| Green | `#2ECC71` | `#2ECC71` → `#16A085` |
| Purple | `#9B59B6` | `#9B59B6` → `#6C5CE7` |
| Sunset | `#FF8E53` | `#FF6B6B` → `#9B59B6` |
| Teal | `#1ABC9C` | `#1ABC9C` → `#16A085` |

### Avatar Generated Gradients (assigned per member)
- **Av 1**: `#FF6B6B` → `#FF8E53` (warm)
- **Av 2**: `#2ECC71` → `#16A085` (green)
- **Av 3**: `#9B59B6` → `#6C5CE7` (purple)
- **Av 4**: `#4A90D9` → `#1E3A5F` (blue)
- **Av 5**: `#F39C12` → `#B5651D` (amber)

### Semantic
- **Like Heart** (`#FF3B5C`): The heart glyph on liked-message count pills — the signature accent.
- **Error Red** (`#F15E6C`): Failed-to-send, destructive actions (Leave group, Remove member).
- **Success Green** (`#2ECC71`): Message-sent confirmation, success toasts.
- **Warning Amber** (`#F1C40F`): Mute/notification warnings.
- **Online Dot** (`#2ECC71`): Active-now indicator on avatars (where shown).

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro Text` / `SF Pro Display` (Apple system font) — GroupMe ships no custom brand typeface; it relies on the iOS system face and uses color + circular avatars for brand expression.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'SF Pro Display', 'Inter', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Emoji**: Apple Color Emoji (system) — emoji are first-class message content and render at message-body size or larger when a message is emoji-only.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Nav Title | SF Pro Display | 32pt | 800 | 1.15 | -0.4pt | "Chats" large title at top of list |
| Screen Title | SF Pro Display | 26pt | 700 | 1.2 | -0.3pt | "New group chat", modal titles |
| Group Name (nav) | SF Pro Display | 22pt | 700 | 1.2 | -0.2pt | Group name in the blue chat nav bar (white) |
| Section Header | SF Pro Text | 18pt | 700 | 1.3 | -0.1pt | "Members & settings" headers |
| Message Body | SF Pro Text | 16pt | 400 | 1.35 | 0pt | The message text inside bubbles |
| List Row Title | SF Pro Text | 15pt | 600 | 1.3 | 0pt | Group name in a Chats-list row |
| List Preview | SF Pro Text | 14pt | 400 | 1.3 | 0pt | One-line last-message preview |
| Sender / Meta | SF Pro Text | 14pt | 400 | 1.3 | 0pt | Timestamps, "Maya · 9:38 AM" |
| Sender Name | SF Pro Text | 12pt | 600 | 1.2 | 0.1pt | Name above first bubble in a run — group accent color |
| Like Count | SF Pro Text | 10pt | 700 | 1.0 | 0.2pt | Number inside the like pill |
| Tab Label | SF Pro Text | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Unread Count | SF Pro Text | 12pt | 700 | 1.0 | 0pt | White number in the blue unread pill |
| Button | SF Pro Text | 16pt | 700 | 1.0 | 0pt | Primary button label |
| Emoji-only Message | — | 40pt | — | 1.0 | — | A message that is only emoji renders enlarged |

### Principles
- **System face, no brand type**: GroupMe deliberately uses SF Pro. Don't substitute a decorative typeface — the brand lives in blue + circular avatars.
- **Body is 400, names are 600**: Message text is regular weight; sender names are semibold and tinted with the group accent color for fast scanning.
- **Color carries hierarchy in chat**: Inside a thread, weight is nearly flat — color (blue outbound, gray inbound, accent sender names) does the differentiating, not type size.
- **Emoji scale up when alone**: An emoji-only message renders at ~40pt; mixed text+emoji stays at body size.
- **Dynamic Type first-class**: Message body, list titles, previews scale with the system setting; tab labels, like-count numbers, and unread pills stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (Create group / Send invite / Save)**
- Shape: Fully rounded pill (999pt corner radius)
- Background: `#00AFF0` (GroupMe Blue)
- Text: `#FFFFFF`, SF Pro Text 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal
- Pressed: background `#0091C7` + scale 0.98
- Disabled: `#00AFF0` at 40% opacity

**Secondary / Outline Button (Add members / Cancel)**
- Background: transparent
- Border: 1.5pt `#00AFF0`
- Text: `#00AFF0`, SF Pro Text 14pt weight 600
- Padding: 12pt vertical, 20pt horizontal
- Corner radius: 999pt (pill)
- Pressed: 8% blue fill

**Pill / Utility Button (Mute / Pin / Filter)**
- Background: `#F0F0F0` light / `#2A2A2C` dark
- Border: 1pt divider
- Text: primary text color, SF Pro Text 14pt weight 500
- Corner radius: 999pt
- Padding: 10pt vertical, 18pt horizontal

**Text Button (See all members / Edit)**
- Font: SF Pro Text 14pt weight 600
- Color: `#00AFF0`
- No background, no underline
- Pressed: 50% opacity

**Circular Send Button**
- Size: 38pt circle
- Background: `#00AFF0` (active) / `#C8C8CC` (empty input, disabled)
- Icon: paper-plane glyph 18pt `#FFFFFF`
- Pressed: `#0091C7` + scale 0.94

### Message Bubble (the core atom)

**Inbound Bubble**
- Background: `#F0F0F0` light / `#2A2A2C` dark
- Text: primary text color, SF Pro Text 16pt weight 400
- Corner radius: 18pt, with the bottom-leading corner squared to 5pt (tail side)
- Padding: 9pt vertical, 13pt horizontal
- Max width: 80% of thread width
- Leading avatar: 30pt circle, aligned to the bottom of the bubble run
- Sender name: 12pt weight 600 in group accent color, above the first bubble in a run, 4pt leading inset

**Outbound Bubble**
- Background: `#00AFF0` (GroupMe Blue)
- Text: `#FFFFFF`, SF Pro Text 16pt weight 400
- Corner radius: 18pt, with the bottom-trailing corner squared to 5pt
- Aligned trailing; no avatar shown for self
- Timestamp: 10pt `#9A9A9A` below trailing edge when expanded

**Bubble Run Grouping**
- Consecutive messages from the same sender within 60s collapse: avatar + name show only once at the top; bubbles stack with 3pt vertical gap

### Like Pill (signature distinctive component)
- Position: docked at the bubble's trailing-bottom corner, overlapping ~10pt below
- Background: `#FFFFFF` light / `#1C1C1E` dark
- Border: 1pt divider
- Corner radius: 999pt
- Content: red heart glyph (`#FF3B5C`) 11pt + count number 10pt weight 700 `#6A6A6A`
- Padding: 2pt vertical, 7pt trailing / 5pt leading
- Appears with a 150ms pop (scale 0.6 → 1.1 → 1.0) when a like lands
- Tapping the pill opens the "Liked by" list (avatars + names)

### Image Gallery Block
- A multi-photo share renders inside a bubble-shaped container
- Single image: rounded 18pt, max 220pt wide, aspect-preserved
- 2–4 images: 3pt-gap grid (2 columns), each cell 8pt corner radius, square-cropped
- 5+ images: 2×2 grid; the 4th cell shows a `+N` overlay (`rgba(0,0,0,0.5)` scrim, white 16pt bold)
- Container padding: 4pt; container radius 18pt with squared tail corner

### Navigation

**Chat Nav Bar (top of a thread)**
- Height: 56pt + safe area
- Background: `#00AFF0` (solid GroupMe Blue) — the signature colored header
- Leading: back chevron 22pt `#FFFFFF`
- Center-leading: 34pt circular group avatar + group name 22pt weight 700 `#FFFFFF` + sub-line (member count / typing) 11pt `rgba(255,255,255,0.82)`
- Trailing: group info / call action 21pt `#FFFFFF`

**Bottom Tab Bar**
- Height: 68pt + safe area
- Background: `rgba(255,255,255,0.94)` light / `rgba(18,18,18,0.94)` dark with blur + 0.5pt top divider
- Tabs (4): Chats, People, Discover, Profile
- Icon size: 22pt
- Active: `#00AFF0`, icon filled
- Inactive: `#888888`, icon stroked
- Labels: SF Pro Text 10pt weight 500, always shown

**Chats List Row**
- Height: 72pt
- Leading: 52pt circular group avatar (group photo or generated gradient)
- Title: group name 15pt weight 600
- Subtitle: last-message preview 14pt weight 400 `#6A6A6A` (1 line, ellipsized)
- Trailing top: timestamp 12pt `#9A9A9A`
- Trailing bottom: unread count pill — `#00AFF0` fill, white 12pt weight 700, min 20pt circle/pill

### Input Fields

**Composer**
- Layout: `+` attach button (left) · pill text field (center) · circular send button (right)
- Pill field: height 38pt, background `#F0F0F0` light / `#2A2A2C` dark, 999pt radius, 16pt horizontal padding
- Placeholder: "Send a message" 14pt `#9A9A9A`
- `+` button: 26pt glyph `#00AFF0` — opens attachment sheet (camera, photo library, GIF, location, poll, event, calendar)
- Send button: 38pt blue circle (see Buttons)
- Bar background: `#FFFFFF` light / `#1C1C1E` dark, 0.5pt top divider

**Search Field (Chats / People)**
- Height: 38pt
- Background: `#F0F0F0` light / `#2A2A2C` dark
- Corner radius: 999pt (pill)
- Leading icon: magnifyingglass 16pt `#9A9A9A`
- Placeholder: "Search" 15pt `#9A9A9A`

### Distinctive Components

**Heart Like + Count Pill**
- See "Like Pill" above — the single most recognizable GroupMe interaction
- Double-tap a bubble to like; a heart animates from the tap point and the count pill updates

**Generated Gradient Avatar**
- 30–52pt circle, two-stop diagonal gradient assigned per member, member initial in white SF Pro weight 700, centered
- Group avatar can be a photo or a composite of member avatars

**Per-Group Theme Picker**
- In group settings: a row of color swatches; selecting one recolors avatar gradients, sender-name color, and accent details across the thread
- The blue chrome (nav bar, outbound bubbles, send) stays constant

**Inline Polls & Events**
- Poll card: question + option rows with vote bars filling in group accent color; "X votes" footer
- Event card: title, date/time, location, RSVP pill buttons (Going / Maybe / Can't)

**GIF / Meme Library**
- Tapping `+` → GIF opens a searchable grid (2 columns) of GIFs; selecting sends inline
- Meme generator: pick image + top/bottom caption, sends as an image message

**Gallery Tab (per group)**
- A grid of all photos shared in the group, 3 columns, 2pt gap, square-cropped, tappable to full screen

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 6, 8, 12, 14, 16, 20, 24, 28, 32, 40
- Bubble vertical gap (different senders): 14pt
- Bubble vertical gap (same sender run): 3pt
- Thread horizontal inset: 12pt
- List row inset: 16pt leading / 16pt trailing

### Grid & Container
- iPhone: chat thread full-bleed with 12pt horizontal inset; bubbles max 80% width
- iPad: thread max-width 680pt centered; split view shows Chats list (340pt) + thread
- Image gallery grid: 2 columns inside bubble; Gallery tab uses 3 columns full-width
- Bottom tab bar pinned; chat nav bar pinned

### Whitespace Philosophy
- **Conversational density**: bubbles breathe with 14pt between speakers but tighten to 3pt within a run — the eye groups by sender
- **The header is loud, the body is calm**: a full-color blue nav bar up top, then a clean light/dark thread so messages and faces dominate
- **Avatars anchor the left rail**: inbound rows reserve a consistent 30pt avatar gutter so names and bubbles align
- **Like pills overlap intentionally**: the pill hangs ~10pt below the bubble corner so it reads as attached to that message, not a new row

### Border Radius Scale
- Square (0pt): nav bar, full-width dividers, photo full-screen
- Subtle (5pt): the squared tail corner of a bubble; gallery image cells (8pt)
- Image cell (8pt): gallery grid cells
- Standard (12pt): cards (poll, event), modals
- Bubble (18pt): message bubbles, image-gallery container
- Comfortable (24pt): bottom sheets
- Pill (999pt): buttons, composer field, search field, unread/like pills
- Circle (50%): avatars, send button

## 6. Depth & Elevation

GroupMe is mostly flat with soft, low shadows; depth signals "this floats above the thread."

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Thread background, list rows, bubbles |
| Raised (Level 1) | `rgba(0,0,0,0.08) 0 1px 3px` | Like pill, composer bar separation |
| Floating (Level 2) | `rgba(0,0,0,0.16) 0 6px 16px` | Attachment sheet, GIF picker, popovers |
| Sheet (Level 3) | `rgba(0,0,0,0.24) 0 -4px 24px` | Bottom sheets (group settings, theme picker, poll/event create) |
| Modal Scrim | `rgba(0,0,0,0.4)` | Dim behind modals and sheets |

**Shadow Philosophy**: GroupMe keeps the thread flat so color does the work. Shadows are reserved for the like pill (a subtle 1px lift so it reads as attached but distinct) and floating UI (attachment sheet, GIF grid). On dark mode, shadows nearly vanish, so floating panels gain a 1pt `#2C2C2E` border as the elevation cue and the like pill switches to a `#1C1C1E` fill that separates it from the `#121212` canvas.

### Motion
- **Like pop**: heart scales 0.6 → 1.1 → 1.0 over 220ms spring (damping 0.7); count pill cross-fades the new number; soft haptic on like
- **Message send**: outbound bubble slides up + fades in from the composer over 200ms ease-out; subtle haptic tick on successful send
- **New inbound message**: bubble fades + 6pt slide-up in 180ms ease-out; thread auto-scrolls if pinned to bottom
- **Attachment sheet**: slides up from bottom 300ms ease-out with scrim fade
- **Tab switch**: instant content swap; active icon cross-fades fill in 120ms
- **Typing indicator**: three dots bounce in a 1.2s loop inside an inbound-style bubble
- **Theme change**: avatar gradients and accent colors cross-fade over 250ms across the visible thread
- **Pull-to-refresh (Chats)**: GroupMe-blue spinner; 250ms settle
- **Haptic feedback**: soft impact on like, light tick on send, selection tick on tab change

## 7. Do's and Don'ts

### Do
- Paint the chat nav bar solid GroupMe Blue (`#00AFF0`) — the colored header is the signature chrome
- Use GroupMe Blue for outbound bubbles, send button, active tab, and unread pills
- Dock the heart-like count in a small rounded pill below the bubble's trailing-bottom corner
- Show the sender's colored name (group accent) above the first bubble in a run
- Use circular generated-gradient avatars with the member's initial
- Collapse consecutive same-sender messages into a run (avatar + name once)
- Render multi-photo shares as a gap grid inside a bubble with a `+N` overflow tile
- Use a pill composer with a `+` attach button and a circular blue send button
- Respect per-group theme accents on avatars and sender names — keep blue chrome constant
- Use SF Pro (system) — no decorative brand typeface
- Keep the thread flat; reserve soft shadows for the like pill and floating sheets

### Don't
- Don't make the chat nav bar white/gray — losing the blue header loses the brand
- Don't use a non-circular avatar shape — GroupMe avatars are always circles
- Don't render likes as inline emoji in the text — they are a docked count pill below the bubble
- Don't color outbound bubbles with the per-group accent — outbound stays GroupMe Blue
- Don't substitute a custom brand font — GroupMe is system-font on purpose
- Don't show the sender name/avatar on every bubble in a run — only the first
- Don't use heavy drop shadows on bubbles — the thread is flat
- Don't pure-black the dark canvas — use `#121212` to keep it warm and friendly
- Don't squarify bubbles — they're 18pt rounded with only the tail corner clipped to ~5pt
- Don't hide the `+` attachment affordance — GIFs/polls/events/memes are core to GroupMe
- Don't animate the like aggressively beyond a quick spring pop — it should feel light and fun

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Bubble max-width 82%; avatar 28pt; gallery grid stays 2-col |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; blue nav bar extends under island |
| iPhone 15/16 Pro Max | 430pt | Bubble max-width 78%; avatar 32pt; larger gallery cells |
| iPad (portrait) | 768pt | Split: Chats list (340pt) + thread (centered max 680pt) |
| iPad (landscape) | 1024pt+ | Persistent Chats list + thread; gallery tab 4 columns |

### Dynamic Type
- Scales: message body, list row title, list preview, screen titles
- Fixed: tab labels, like-count number, unread pill number, sender name (layout-sensitive in run grouping)
- Image gallery cells scale proportionally with thread width, not font scale

### Orientation
- All screens rotate; iPad gains persistent split view (Chats + thread)
- Image full-screen viewer supports rotation and pinch-zoom
- Composer stays pinned above the keyboard in both orientations

### Touch Targets
- Bubble (for double-tap like): full bubble area, min 44pt tall
- Like pill (tap to see who liked): 28pt hit area around the pill
- Tab bar icons: 22pt glyph, 44pt hit
- Send button: 38pt visual, 44pt hit
- `+` attach: 26pt glyph, 44pt hit
- List row: full 72pt row tappable

### Safe Area Handling
- Top: blue chat nav bar extends to the top safe area and under the Dynamic Island (content within safe area)
- Bottom: tab bar + composer respect the home indicator
- Keyboard: thread scrolls above the keyboard; composer docks directly on the keyboard
- Sides: 12pt thread inset, 16pt list inset

## 9. Agent Prompt Guide

### Quick Color Reference
- GroupMe Blue: `#00AFF0`
- Blue pressed: `#0091C7`
- Blue deep: `#0077B5`
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#121212`
- Inbound bubble: `#F0F0F0` light / `#2A2A2C` dark
- Outbound bubble: `#00AFF0` (white text)
- Text primary: `#1A1A1A` light / `#ECECEC` dark
- Text secondary: `#6A6A6A` light / `#A0A0A0` dark
- Like heart: `#FF3B5C`
- Like pill bg: `#FFFFFF` light / `#1C1C1E` dark
- Sender name: group accent (default `#00AFF0`)
- Theme coral / green / purple: `#FF6B6B` / `#2ECC71` / `#9B59B6`
- Error red: `#F15E6C`

### Example Component Prompts
- "Build the GroupMe chat nav bar in SwiftUI: 56pt tall + safe area, solid `#00AFF0` background. Leading: back chevron 22pt `#FFFFFF`. Then a 34pt circular group avatar (two-stop diagonal gradient, group initials white weight 700). Then a VStack: group name SF Pro Display 22pt weight 700 `#FFFFFF`, sub-line ('8 members · Maya is typing…') 11pt `rgba(255,255,255,0.82)`. Trailing: group-info circle-i icon 21pt `#FFFFFF`."

- "Create a GroupMe inbound message bubble: `#F0F0F0` (light) / `#2A2A2C` (dark) fill, SF Pro Text 16pt weight 400 primary text, 18pt corner radius with the bottom-leading corner clipped to 5pt, 9pt vertical / 13pt horizontal padding, max-width 80% of thread. Above the first bubble in a run show the sender name SF Pro Text 12pt weight 600 in the group accent color (default `#00AFF0`), 4pt leading inset, with a 30pt circular gradient avatar aligned to the bottom of the run on the leading side."

- "Build the GroupMe like pill: a small rounded-pill badge docked at the trailing-bottom corner of a message bubble, overlapping ~10pt below it. Background `#FFFFFF` light / `#1C1C1E` dark, 1pt divider border, 999pt radius, 2pt vertical / 7pt trailing / 5pt leading padding. Content: a `#FF3B5C` heart glyph 11pt + the like count in SF Pro Text 10pt weight 700 `#6A6A6A`. On a new like, pop it scale 0.6 → 1.1 → 1.0 over 220ms spring and cross-fade the number; soft haptic."

- "Render a GroupMe image gallery block: a bubble-shaped container, 4pt padding, 18pt radius with squared tail corner, holding a 2-column grid with 3pt gaps. Each cell is square-cropped with an 8pt corner radius. For 5+ images, show a 2×2 grid where the 4th cell has a `+N` overlay: `rgba(0,0,0,0.5)` scrim with white SF Pro 16pt weight 700 centered."

- "Build the GroupMe composer: a bottom bar `#FFFFFF` light / `#1C1C1E` dark with a 0.5pt top divider. Layout: a `+` attach button (26pt glyph `#00AFF0`) on the left, a pill text field in the center (38pt tall, `#F0F0F0` / `#2A2A2C` fill, 999pt radius, 16pt horizontal padding, placeholder 'Send a message' 14pt `#9A9A9A`), and a 38pt circular send button on the right (`#00AFF0` when input has text, `#C8C8CC` when empty, white paper-plane glyph 18pt). Pressed send: `#0091C7` + scale 0.94."

- "Create the GroupMe Chats list row: 72pt tall, leading 52pt circular group avatar (photo or generated gradient), title = group name SF Pro Text 15pt weight 600, subtitle = last-message preview 14pt weight 400 `#6A6A6A` (1 line ellipsized), trailing-top timestamp 12pt `#9A9A9A`, trailing-bottom unread count pill (`#00AFF0` fill, white 12pt weight 700, min 20pt)."

- "Build the GroupMe per-group theme picker row in settings: a horizontal row of 44pt color-swatch circles (Default `#00AFF0`, Coral `#FF6B6B`, Green `#2ECC71`, Purple `#9B59B6`, Sunset gradient `#FF6B6B`→`#9B59B6`). Selecting one cross-fades all visible avatar gradients and sender-name colors over 250ms while keeping the blue nav bar / outbound bubbles / send button constant."

### Iteration Guide
1. The chat nav bar is solid GroupMe Blue (`#00AFF0`) with white text — this colored header is the #1 brand signal
2. Outbound bubbles are GroupMe Blue; inbound are neutral gray (`#F0F0F0` / `#2A2A2C`) — never the per-group accent
3. The heart-like docks as a count pill below the bubble's trailing-bottom corner — not inline emoji
4. Sender names appear once above the first bubble in a run, tinted in the group accent color
5. Avatars are always circular generated gradients with the member's initial in white
6. Canvas is white light / `#121212` dark (warm, not pure black); the thread stays flat
7. Use SF Pro (system) — no custom brand typeface; color + avatars carry the brand
8. Bubbles are 18pt rounded with only the tail corner clipped to ~5pt
9. Multi-photo shares render as a 2-col gap grid in a bubble with a `+N` overflow tile
10. Per-group themes recolor avatars + sender names only; blue chrome and the like interaction stay constant
