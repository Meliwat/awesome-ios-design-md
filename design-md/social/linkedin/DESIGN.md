# Design System Inspiration of LinkedIn (iOS)

## 1. Visual Theme & Atmosphere

LinkedIn's iOS app is a content-first professional newsroom. The canvas is a soft, desaturated cream (`#F3F2EF`) — intentionally not white, because a pure-white feed on a phone would feel like a word processor. That muted off-white is the most important single color decision in the app: it lets the stack of white (`#FFFFFF`) post cards float gently above the page, and it keeps the visual temperature warm and paper-like rather than clinical. The reader is looking at a feed of articles, job listings, profile updates — dense information — and the cream ground reduces the eye-strain of hours of scrolling.

The accent is LinkedIn Blue (`#0A66C2`), which the app applies with monastic restraint. Blue is for verbs and links only — "Connect", "Follow", "Apply", "Message", "See more", hyperlinked hashtags, the underline under @mentions, and the active tab indicator on the bottom nav. Everything else is grayscale. There is no secondary blue for accents, no orange for highlights, no purple for Premium (gold stands in for Premium). The restraint is what makes the blue read as authoritative: when a button is blue, you *act* on it. Green is reserved exclusively for the "Open to Work" ring around an avatar (`#057642`), and gold (`#915907`) for the Premium badge and the gold frame around a Premium user's profile photo.

Typography is SF Pro — LinkedIn dropped the older Source Sans Pro webfont in favor of Apple's native system face on iOS, which is faster to render and feels native. Hierarchy is dense and text-first. Feed post names sit at 14pt weight 600, headlines (job title, company) at 12pt weight 400 in gray, post body at 14pt weight 400 with 1.5 line height. There are no hero headlines, no display-size type — LinkedIn is a reading app, and reading needs calm, consistent rhythm. The most expressive type moment is the profile name at 24pt weight 700 above the headline tagline; everywhere else the hierarchy sits in the 12-16pt range with weight variation (400 / 600 / 700) doing the work.

**Key Characteristics:**
- Desaturated cream canvas (`#F3F2EF`) — NOT white — with white post cards floating on top
- LinkedIn Blue (`#0A66C2`) reserved strictly for links, Connect/Follow buttons, and active states
- Professional tone: dense, text-first, information-rich — no decorative illustration
- Feed cards as the core unit — 12pt corner radius, 0pt shadow, just a hairline border
- Avatar rings carry status: green for "Open to Work", gold frame for Premium members
- Six colored custom reaction icons (Like / Celebrate / Support / Love / Insightful / Funny)
- SF Pro at 12-24pt with weight 400 / 600 / 700 — no display type, no flourish
- Dense top nav: profile avatar left, search bar center, messaging top-right
- Bottom tab bar: Home, My Network, Post (+), Notifications, Jobs
- Underline-on-hyperlinks — the old-web convention kept alive as a professional signal

## 2. Color Palette & Roles

### Primary
- **LinkedIn Blue** (`#0A66C2`): Primary CTA, hyperlinks, Connect/Follow buttons, active tab indicator, @mentions.
- **LinkedIn Blue Pressed** (`#004182`): Pressed / hovered state for blue buttons and links.
- **LinkedIn Blue Subtle** (`#E7F3FF`): Selected background for tabs, hover rows on iPad/desktop parity.
- **Deep Navy** (`#00396B`): Section headers on Premium surfaces, Learning subject cards.

### Canvas & Surfaces
- **Canvas Cream** (`#F3F2EF`): Primary canvas — the desaturated off-white page background.
- **Card Surface** (`#FFFFFF`): Feed post cards, profile cards, message rows — white on cream.
- **Elevated Surface** (`#F9F9F9`): Hover rows, nested cards, comment threads.
- **Divider** (`#E0DFDC`): Hairline dividers between feed cards, within cards between sections.
- **Divider Subtle** (`#EDEDED`): Divider within a card (e.g., between a post and its reactions footer).

### Text
- **Text Primary** (`#000000E6`): Primary body text — 90% black, softer than pure `#000`.
- **Text Secondary** (`#00000099`): Headlines, timestamps, metadata — 60% black.
- **Text Tertiary** (`#00000066`): Very low-emphasis — dot separators, helper microcopy — 40% black.
- **Text on Blue** (`#FFFFFF`): White text on blue CTAs.

### Status & Role Accents
- **Open to Work Green** (`#057642`): Ring around avatar when user is #OpenToWork; badge chip fill.
- **Open to Work Green Dark** (`#0B5B4C`): Pressed state and darker accent.
- **Premium Gold** (`#915907`): Badge color for Premium (the small square "in-" gold icon next to names); 2pt gold frame around Premium users' avatars.
- **Premium Gold Highlight** (`#C37D16`): Gold gradient top for the gold avatar frame.
- **Hiring Purple** (`#6A3DAA`): "#Hiring" badge ring around recruiters' avatars (rare).

### Semantic
- **Like Blue** (`#0A66C2`): Default thumbs-up reaction color.
- **Celebrate Yellow** (`#F5BB00`): Celebrate reaction (clapping hands).
- **Support Lavender** (`#B24020`): Support reaction (heart + hug) — a warm brownish-red.
- **Love Red** (`#DF704D`): Love reaction (heart) — coral, not crimson.
- **Insightful Yellow** (`#E7A33E`): Insightful reaction (lightbulb) — amber-yellow.
- **Funny Cyan** (`#00A0DC`): Funny reaction (laughing face).
- **Error Red** (`#CC1016`): Form errors, failed send.
- **Success Green** (`#0B5B4C`): Send-confirmed toast.

### Dark Mode
- **Dark Canvas** (`#1B1F23`): Primary dark background — a charcoal not pure black.
- **Dark Card Surface** (`#1D2226`): Feed post cards on dark.
- **Dark Elevated** (`#282E32`): Hover rows, nested surfaces.
- **Dark Divider** (`#38434F`): Hairline dividers.
- **Dark Text Primary** (`#FFFFFFE6`): 90% white.
- **Dark Text Secondary** (`#FFFFFF99`): 60% white.
- **Dark Blue Accent** (`#70B5F9`): Links and CTAs shift to lighter blue for contrast on dark.

## 3. Typography Rules

### Font Family
- **Primary (2020+)**: `SF Pro` (Apple system) — San Francisco Text below 20pt, San Francisco Display above 20pt, applied automatically by iOS.
- **Legacy (web parity)**: `Source Sans 3` (Adobe) — used on linkedin.com and older app surfaces. Fallback only.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Monospace (code snippets in articles)**: `SF Mono`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Profile Name (Hero) | SF Pro Display | 24pt | 700 | 1.15 | -0.2pt | User profile screen hero name |
| Screen Title | SF Pro Display | 20pt | 700 | 1.2 | -0.2pt | "Messaging", "My Network", "Jobs" |
| Section Header | SF Pro Text | 16pt | 700 | 1.25 | -0.1pt | "People you may know", "Activity" |
| Post Author | SF Pro Text | 14pt | 600 | 1.3 | 0pt | Name at top of feed post |
| Post Headline | SF Pro Text | 12pt | 400 | 1.3 | 0pt | Job title + company, 2-line clamp |
| Post Body | SF Pro Text | 14pt | 400 | 1.5 | 0pt | Main post content |
| Connection Degree | SF Pro Text | 12pt | 400 | 1.2 | 0pt | "1st", "2nd", "3rd+" after name |
| Meta / Timestamp | SF Pro Text | 12pt | 400 | 1.2 | 0pt | "3d •", "1w •", "Edited • Promoted" |
| Button (Primary) | SF Pro Text | 16pt | 600 | 1.0 | 0pt | Blue pill buttons ("Connect", "Follow") |
| Button (Secondary) | SF Pro Text | 14pt | 600 | 1.0 | 0pt | Outline buttons ("Message", "Save") |
| Reaction Count | SF Pro Text | 12pt | 400 | 1.2 | 0pt | "127 reactions" under post |
| Comment Body | SF Pro Text | 14pt | 400 | 1.4 | 0pt | Comment text |
| Tab Label | SF Pro Text | 11pt | 500 | 1.0 | 0.1pt | Bottom tab bar |
| Label (Badge) | SF Pro Text | 11pt | 700 | 1.0 | 0.4pt | "PROMOTED", "FEATURED" small caps |
| Input Placeholder | SF Pro Text | 14pt | 400 | 1.3 | 0pt | Search bar, comment box |

### Principles
- **SF Pro Dynamic Type is first-class**: Body, post titles, and comments respect system sizing; badge labels and timestamps stay fixed.
- **Weight concentrated at 400 / 600 / 700**: Regular for body, semibold for names/buttons, bold for section headers. No light, no ultra, no black.
- **Text-first hierarchy**: No display type. The information itself is the hero.
- **Hyperlinks underlined**: Blue `#0A66C2` with a 1pt underline on @mentions, #hashtags, and inline links — an old-web signal of professional tone.
- **Two-line clamp on headlines**: The job-title headline below names caps at two lines with an ellipsis — keeps feed rhythm consistent.

## 4. Component Stylings

### Buttons

**Primary Pill (Connect / Follow / Apply)**
- Background: `#0A66C2`
- Text: `#FFFFFF`
- Padding: 8pt vertical, 16pt horizontal
- Corner radius: 500pt (full pill)
- Font: SF Pro Text, 16pt, weight 600
- Pressed: background `#004182`, scale 0.98
- Leading SF Symbol: `person.badge.plus` (Connect), `plus` (Follow), `paperplane.fill` (Apply) — 18pt
- Used for: "Connect", "Follow", "Apply", "Accept"

**Outline Pill (Message / Pending / Following)**
- Background: transparent
- Border: 1pt solid `#0A66C2`
- Text: `#0A66C2`
- Padding: 8pt vertical, 16pt horizontal
- Corner radius: 500pt
- Font: SF Pro Text, 14pt, weight 600
- Pressed: background `#E7F3FF`
- Used for: "Message", "Pending", "Following", "Save"

**Icon Reaction Button ("Like")**
- 44pt hit area
- Default: `thumbsup` 20pt + label "Like" in 13pt w600 `#00000099`
- Active (liked): `thumbsup.fill` + "Like" text shifts to `#0A66C2`
- Long-press: expands into the 6-reaction picker menu (see Distinctive Components)

**Text Button ("See more", "Show translation")**
- Font: SF Pro Text, 14pt, weight 600
- Color: `#00000099`
- No underline for UI actions; underline only for hyperlinked content

**Send Button (Message composer)**
- Circular, 32pt diameter
- Background: `#0A66C2` (enabled) / `#0000001A` (disabled)
- Icon: `paperplane.fill` 16pt white
- Pressed: scale 0.94 + soft haptic

### Cards & Containers

**Feed Post Card**
The core unit of LinkedIn. A white card floating on the cream canvas.
- Background: `#FFFFFF`
- Corner radius: 0pt on iPhone (edge-to-edge width with 8pt vertical gap between cards) — OR 8pt radius on iPad/larger widths
- Horizontal margin: 0pt on iPhone (full-width card), 16pt on iPad
- Vertical gap between cards: 8pt (fills with `#F3F2EF` canvas showing through)
- Internal padding: 16pt horizontal, 12pt vertical top, 12pt bottom
- Structure (top to bottom):
  1. Author row: 56pt circular avatar + name/headline column + ellipsis menu + Follow/Connect button
  2. Post body text (14pt w400, "see more" link at clamp)
  3. Media (image/video/PDF/article link — full-width, 0pt radius inside card)
  4. Reaction count row + comment count (12pt w400 `#00000099`, leading reaction stack of 3 overlapping icons)
  5. 1pt `#EDEDED` divider
  6. Action bar: Like | Comment | Repost | Send (equal 4-column split, 44pt tall)

**Author Row**
- Avatar: 56pt circular, 2pt ring if Open-to-Work (green) or Premium (gold)
- Name: 14pt w600 Text Primary
- Connection degree: "• 1st", "• 2nd" after name at 12pt w400 `#00000099`
- Headline: 12pt w400 `#00000099`, 2-line clamp (job title + company)
- Timestamp: "3d • 🌐" at 12pt w400 with globe icon for "public"
- Ellipsis menu: top-right, 20pt glyph, 44pt hit

**Profile Card**
- Top 100pt cover banner image
- Avatar: 128pt circular, `-48pt` offset (overlapping banner), 4pt white ring
- Premium: replace 4pt white ring with 4pt gold (`#915907` → `#C37D16` gradient) frame
- Open to Work: add 4pt green (`#057642`) ring overlaid
- Name: 24pt w700 below
- Headline: 16pt w400 `#000000E6`, unlimited lines
- Location + connection count: 14pt w400 `#00000099`
- Actions row: Connect / Message / More — blue primary + outline secondary

**Job Card**
- Background: `#FFFFFF`, 8pt corner radius, 1pt border `#E0DFDC`
- Logo: 56pt square (company logo), 4pt corner radius
- Title: 16pt w700 `#0A66C2` (hyperlinked blue)
- Company: 14pt w400 Text Primary
- Location: 12pt w400 `#00000099`
- Salary (when public): 12pt w400 `#00000099`
- "Easy Apply" badge: 11pt w700 `#0A66C2` with briefcase icon, or "Apply" outline button

**Message Row**
- Height: 72pt
- Avatar: 56pt circular leading
- Name + preview: name 14pt w600, preview 13pt w400 `#00000099`, 2-line clamp
- Timestamp trailing: 12pt w400 `#00000099`
- Unread: bold name + 8pt blue dot leading the row
- Pressed: background `#F9F9F9`

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` with 1pt top border `#E0DFDC`
- Tabs (5): Home, My Network, Post (+), Notifications, Jobs
- Icon size: 24pt
- Active color: `#000000E6` with 2pt underline bar `#000000E6` just below icon
- Inactive: `#00000099`
- Active "Post" tab is a solid black `+` in a circle — distinctive
- Labels: SF Pro Text 11pt w500, shown always
- Active tab: filled SF Symbol; inactive: outlined

**Top Nav (Home)**
- Height: 52pt + safe area
- Profile avatar (28pt circular) top-left — tapping opens drawer
- Search field (centered, full remaining width, 40pt tall pill, `#EDEDED` fill, magnifying glass icon leading, "Search" placeholder)
- Messaging icon top-right (24pt) — tapping opens Messaging

**Top Nav (Profile / Detail)**
- Height: 44pt + safe area
- Back chevron left (24pt)
- Screen title center (16pt w700)
- Trailing action (share / more icon, 24pt)

### Input Fields

**Search Bar**
- Height: 40pt
- Background: `#EDEDED` (light mode) / `#282E32` (dark mode)
- Corner radius: 500pt (pill)
- Leading SF Symbol `magnifyingglass` 18pt `#00000099`
- Placeholder: "Search", 14pt w400 `#00000099`
- Focus: background `#FFFFFF` + 1pt `#0A66C2` border

**Comment Composer**
- Height: 40pt (grows multi-line)
- Background: `#F3F2EF`
- Corner radius: 20pt
- Leading 28pt user avatar (the commenter's), 8pt gap
- Placeholder "Add a comment..." 14pt w400 `#00000099`
- Trailing action: photo icon + GIF icon + send arrow

**Post Composer (bottom sheet)**
- Full-height sheet
- Top: avatar + name + audience chip ("Anyone", "Connections only")
- Massive text area, 18pt w400, auto-growing
- Bottom action bar: photo / video / document / celebrate / poll icons, all 24pt
- Trailing "Post" primary pill button, disabled until content typed

### Distinctive Components

**The 6-Reaction Menu**
LinkedIn's most recognizable UI moment. Long-press on the Like button pops a horizontal row of 6 emoji-style reactions.
- Container: `#FFFFFF` rounded rectangle, 48pt tall, 12pt vertical padding, 8pt horizontal padding per icon, shadow `rgba(0,0,0,0.12) 0 4px 16px`
- Corner radius: 32pt (fully rounded pill)
- 6 circular icon buttons, 40pt diameter each, 12pt gap:
  1. **Like** — Blue thumbsup `#0A66C2`
  2. **Celebrate** — Yellow clapping hands `#F5BB00`
  3. **Support** — Heart-with-hug `#B24020`
  4. **Love** — Red heart `#DF704D`
  5. **Insightful** — Lightbulb `#E7A33E`
  6. **Funny** — Laughing face `#00A0DC`
- Each icon animates in with 40ms stagger (scale 0 → 1.1 → 1.0 spring)
- Hover (slide finger over): the hovered icon scales to 1.3 and floats up 8pt; others dim to 60% opacity
- Release over icon: haptic success + the reaction animates into the post's Like button slot

**Reaction Stack (on post footer)**
- Three overlapping circular icons (24pt each, -8pt horizontal overlap) showing top 3 reaction types on the post
- Followed by count text: "Sarah Chen and 127 others" or "127 reactions"
- Tap opens the reactors sheet

**Open to Work Ring**
- 4pt green (`#057642`) stroke around the avatar circle
- Small "#OpenToWork" green badge overlay on bottom-right of avatar (optional — user toggles)

**Premium Gold Frame**
- 4pt gold gradient stroke (`#915907` bottom → `#C37D16` top) around avatar
- Small gold "in" badge overlay on bottom-right — "LinkedIn Premium"

**"1st / 2nd / 3rd+" Degree Indicator**
- Small inline tag after the name: "• 1st", "• 2nd", "• 3rd+"
- Font 12pt w400 `#00000099`
- "1st" is plain gray; "3rd+" often styled with a circled tag at 11pt w600 `#00000099`

**Job Alert Pill Row**
- Horizontal scrollable row of job category chips: "Product Manager", "Software Engineer", "Designer"
- Each chip 36pt tall, 500pt radius, 1pt border `#00000066`, 14pt w500 text
- Tap: blue-fill selected state `#0A66C2` + white text

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56, 72
- Post card internal padding: 16pt horizontal, 12pt vertical
- Gap between cards in feed: 8pt (canvas cream shows through)
- Avatar-to-text gap in rows: 12pt

### Grid & Container
- iPhone: full-bleed feed cards (no horizontal margin on card, 16pt internal padding)
- iPad: feed cards get 16pt horizontal margin + 8pt corner radius
- Profile screen: hero banner edge-to-edge; content below at 16pt insets
- Messaging list: 16pt horizontal inset on rows

### Whitespace Philosophy
- **Dense feed rhythm**: LinkedIn is a reading app — cards sit 8pt apart with internal padding of 12-16pt. Content breathes internally, not externally.
- **Profile has generous hero space**: 100pt banner + 128pt overlapping avatar = 180pt of hero zone before content begins.
- **Messaging is minimalist**: chat bubbles align left/right with 8pt gap between turns, no decoration.
- **Jobs is card-heavy**: every job is a card with 8pt border radius, 1pt border, stacked with 12pt gaps.

### Border Radius Scale
- Square (0pt): Full-bleed iPhone feed cards, media inside cards
- Soft (4pt): Company logos, media thumbnails
- Standard (8pt): Job cards, iPad feed cards, outline chips
- Comfortable (12pt): Bottom sheets, large surfaces
- Pill (500pt): Primary/outline CTA buttons, search bar, filter chips
- Circle (50%): Avatars, reaction icons, send button

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Feed cards, profile sections |
| Card (Level 1) | `rgba(0,0,0,0.08) 0 1px 2px` | Job cards on Jobs tab, suggested people cards |
| Floating (Level 2) | `rgba(0,0,0,0.12) 0 4px 16px` | Reaction picker menu, tooltip popovers |
| Sheet (Level 3) | `rgba(0,0,0,0.16) 0 -4px 24px` | Bottom sheets (compose, share, reactors list) |
| Modal Overlay | `rgba(0,0,0,0.4)` | Dimmed background behind sheets and dialogs |
| Top Nav Scroll | 1pt `#E0DFDC` border appears on scroll | Border-only elevation for nav |

**Shadow Philosophy**: LinkedIn is flat and newspaper-like. The feed card lives with zero shadow — separation is achieved by the `#F3F2EF` canvas cream showing through the 8pt gap between cards. Shadows are reserved for transient overlays (reaction picker, tooltips, sheets) where the affordance is "this is floating above the page". The visual quiet is deliberate: content is the hero.

### Motion
- **Button press**: scale 0.98 + background shift, 120ms ease-out, no haptic on most buttons (paired haptic only on Connect/Apply/Send)
- **Like tap**: icon scale 1.0 → 1.3 → 1.0 spring 300ms + fill color transition + `.impactOccurred(.soft)`
- **Long-press for reactions**: 400ms hold triggers picker pop-up with cascade of 6 icons spring-in 40ms stagger
- **Card swipe down**: 60fps scroll with elastic rubber-band at top
- **Pull-to-refresh**: linear blue progress bar at top of nav (12pt tall, animated indeterminate then determinate)
- **Message send**: arrow icon rotates -90° + slides up off screen 200ms + bubble flies into chat spring 350ms
- **Connection accepted**: confetti emitter (only for Connections, not Follows) + success haptic
- **Tab switch**: instant, no transition — the underline bar slides between tabs in 200ms ease-out

## 7. Do's and Don'ts

### Do
- Use `#F3F2EF` cream as the canvas — NOT pure white, the desaturation is signature
- Reserve LinkedIn Blue (`#0A66C2`) for links, Connect/Follow, active states — it's the verb
- Use 90% black (`#000000E6`) for body text — softer than pure black
- Float white post cards on cream with 8pt gap between — no shadow, canvas separates them
- Underline hyperlinks (@mentions, #hashtags, inline URLs) at 1pt in LinkedIn Blue
- Show connection degree ("• 1st", "• 2nd", "• 3rd+") inline after every name in the feed
- Use the 6-reaction picker for Like long-press — don't collapse to just thumbs-up/heart
- Use SF Pro with weights 400/600/700 — native iOS rendering
- Layer the green Open-to-Work ring or gold Premium frame around avatars as status indicators
- Keep reactions as colored custom icons — not system emoji

### Don't
- Don't use pure white `#FFFFFF` as the main canvas — it flattens the card structure
- Don't add accent colors beyond LinkedIn Blue — the app's restraint is professional signaling
- Don't use decorative illustrations — text + photos + media is the content language
- Don't show a single generic "Like" — always offer the 6 reactions on long-press
- Don't use display-size headlines — 24pt profile name is the upper bound
- Don't round feed cards on iPhone — full-bleed 0pt radius is the phone layout
- Don't use shadows for feed cards — the cream canvas separates them
- Don't show green anywhere except the Open-to-Work ring and "Active now" dot
- Don't mix Premium gold with regular blue CTAs — Premium gets its own gold-accent surface
- Don't animate heavily — LinkedIn's motion language is minimal and functional

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Avatars drop to 48pt in feed; search bar shrinks |
| iPhone 13/14/15 | 390pt | Standard 56pt feed avatars |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | Feed media images at 16:9 utilize extra width |
| iPad | 768pt+ | 3-column layout: left sidebar (profile+nav), center feed, right sidebar (news/ads). Feed cards get 8pt radius and 16pt margin |

### Dynamic Type
- Post body, comments, body copy: full scale with Dynamic Type
- Post author, headlines: scale to XL
- Timestamps, connection degree, badge labels: FIXED (layout-sensitive)
- Buttons, tab labels: FIXED

### Orientation
- iPhone: **portrait-locked** for Home, Notifications, Messaging
- Profile, articles, job details: rotate to landscape
- Video posts: full rotation
- iPad: free rotation throughout

### Touch Targets
- Feed avatar: 56pt — full-row tap for profile
- Reaction icons: 40pt in picker, 44pt hit areas on action bar
- Post ellipsis menu: 44pt hit
- Tab bar icons: 24pt glyph, 44pt effective hit
- Comment send arrow: 32pt circular, 44pt hit

### Safe Area Handling
- Top: top nav honors safe area and Dynamic Island
- Bottom: tab bar + home indicator respected; post composer sheet rises above keyboard with safe-area padding
- Sides: 16pt insets on non-feed screens (iPhone feed is full-bleed)

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#F3F2EF`
- Canvas (dark): `#1B1F23`
- Card surface: `#FFFFFF`
- Text primary: `#000000E6` (90% black)
- Text secondary: `#00000099` (60% black)
- LinkedIn Blue: `#0A66C2`
- Blue pressed: `#004182`
- Open to Work green: `#057642`
- Premium gold: `#915907`
- Divider: `#E0DFDC`
- Reaction — Like Blue: `#0A66C2`
- Reaction — Celebrate Yellow: `#F5BB00`
- Reaction — Support: `#B24020`
- Reaction — Love: `#DF704D`
- Reaction — Insightful: `#E7A33E`
- Reaction — Funny: `#00A0DC`

### Example Component Prompts
- "Create a LinkedIn feed post card in SwiftUI: white background `#FFFFFF`, full-bleed on iPhone (0pt corner radius), 16pt horizontal internal padding, 12pt vertical. Top row: 56pt circular avatar + name 'Sarah Chen' in SF Pro Text 14pt w600 + '• 1st' in 12pt w400 `#00000099`; below name the headline 'Principal Designer at Figma' in 12pt w400 `#00000099` 2-line clamp. Post body 14pt w400 line-height 1.5. Below body: 1pt `#EDEDED` divider, then action bar with 4 equal-width buttons (Like / Comment / Repost / Send), 44pt tall, labels 13pt w600 `#00000099`. The whole card sits on the `#F3F2EF` canvas with 8pt gap from the next card."

- "Build the LinkedIn 6-reaction picker: horizontal row of 6 circular 40pt icon buttons with 12pt gap, inside a 48pt-tall white pill container with `rgba(0,0,0,0.12) 0 4px 16px` shadow and 32pt corner radius. Icons in order: thumbsup `#0A66C2`, clapping `#F5BB00`, heart-hug `#B24020`, heart `#DF704D`, lightbulb `#E7A33E`, laughing `#00A0DC`. Entrance: each icon scales 0 → 1.1 → 1.0 spring with 40ms stagger. Hover: hovered icon scales 1.3 and translates up 8pt; others dim to 60%. Release: soft haptic + reaction flies into post's Like slot."

- "Design the LinkedIn profile hero: 100pt full-bleed cover banner image, 128pt circular avatar centered-leading with -48pt top offset overlapping the banner, 4pt white stroke around avatar. If Premium: swap white stroke for gold gradient `#915907` → `#C37D16`. If Open-to-Work: add additional 4pt `#057642` ring. Below: name 'Sarah Chen' in SF Pro Display 24pt w700, headline in SF Pro Text 16pt w400 `#000000E6`, location in 14pt w400 `#00000099`. Action row: Connect primary pill (`#0A66C2` bg, white text, 16pt w600) + Message outline pill (`#0A66C2` border, `#0A66C2` text) + More (ellipsis circle)."

- "Build the LinkedIn bottom tab bar: 56pt + safe area, `#FFFFFF` background with 1pt top border `#E0DFDC`. 5 tabs: Home (house.fill), My Network (person.2.fill), Post (plus.circle.fill — this one is a black solid + icon), Notifications (bell.fill), Jobs (briefcase.fill). 24pt icons. Active state: icon `#000000E6` + 2pt underline bar directly below icon, inactive `#00000099`. Labels in SF Pro Text 11pt w500 below each icon."

- "Create the LinkedIn top nav: 52pt + safe area, `#FFFFFF` background. 28pt circular profile avatar leading at 16pt inset. Centered 40pt-tall search pill (`#EDEDED` fill, 500pt radius, leading magnifyingglass 18pt `#00000099`, placeholder 'Search' 14pt w400 `#00000099`, fills remaining width with 12pt gap from both avatar and trailing icon). Messaging icon trailing (24pt chat bubble). Tap avatar opens profile drawer; tap search goes to search screen; tap messaging opens inbox."

- "Build a LinkedIn job card: 8pt corner radius, 1pt border `#E0DFDC`, white background. 56pt square company logo (4pt radius) top-leading. Title 'Senior Product Manager' in 16pt w700 `#0A66C2` (hyperlinked blue). Company name 14pt w400 `#000000E6`. Location '• San Francisco, CA (Hybrid)' 12pt w400 `#00000099`. Salary row (optional) '$180K-$240K/yr' 12pt w400 `#00000099`. Trailing action: 'Easy Apply' badge pill 11pt w700 `#0A66C2` with briefcase icon OR 'Apply' outline button."

### Iteration Guide
1. Canvas is cream (`#F3F2EF`) — NOT white, NOT gray. The desaturation lets white cards pop without shadow.
2. LinkedIn Blue (`#0A66C2`) only for links, CTAs, and active states — never for text body, borders, or decoration.
3. Feed cards are full-bleed with 0pt radius on iPhone; 8pt radius + 16pt margin on iPad. 8pt vertical gap between cards.
4. Text primary is 90% black (`#000000E6`) — not pure black. Secondary is 60% black (`#00000099`).
5. Six reactions on long-press — Like / Celebrate / Support / Love / Insightful / Funny — each a colored custom icon, never system emoji.
6. Avatar rings carry status: green `#057642` for Open-to-Work, gold gradient for Premium. Layer both if user has both.
7. Connection degree ("• 1st", "• 2nd", "• 3rd+") appears inline after every name in the feed — never omit.
8. Shadow is minimal: feed cards get 0pt; reaction picker gets `0 4px 16px`; sheets get `0 -4px 24px`.
9. Typography is SF Pro at 12-24pt with weights 400/600/700 only. No display, no extra-bold.
10. Dark mode shifts canvas to `#1B1F23` (charcoal), blue to `#70B5F9` — preserves the cream-white contrast feeling.
