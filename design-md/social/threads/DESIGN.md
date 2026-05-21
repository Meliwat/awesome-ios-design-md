# Design System Inspiration of Threads (iOS)

## 1. Visual Theme & Atmosphere

Threads is Meta's answer to text-first social networking — a sister-app to Instagram that trades photos for conversation. The canvas is deliberately quiet: true black (`#000000`) in the default dark mode and soft paper white (`#FFFFFF`) in light, chosen to let long-form text breathe without the visual noise of hashtags, trending topics, or a retweet button. Where X is a newsroom, Threads is a notebook. The scroll is slower, the posts are roomier, and the negative space between ideas is part of the voice.

A Thread post is a self-contained text block sitting on a full-bleed canvas, anchored by a 36pt circular profile photo on the leading side and a thin vertical line that runs from the avatar downward to the reply row — a visual "thread" that connects the author's post to its conversation below. Reply nesting renders as an indented stack of smaller avatars with the same thread-line descending to the next reply, creating a branching visual. This thread-line is the brand's structural signature and the element that most distinguishes a Threads post from an X post. Typography carries the conversation feel: body type sits at 15pt weight 400 on generous 1.4 line height, and there are no cards, shadows, or containers around posts — just canvas, avatar, thread-line, body, and a spare 4-icon action row (heart, comment, repost, share).

The accent system is almost non-existent. Text links and @mentions render in Instagram's inherited deep blue (`#2D7FF9`), the like heart fills Instagram-coral (`#FE2C55` — carried over from Instagram's double-tap heart), and that's essentially it. Repost and share icons stay in neutral gray on tap. No badges, no reactions menu, no blue checkmark (Threads accounts rely on Instagram's verification). The app leans heavily into Instagram Sans — Meta's proprietary face since 2022 — with its narrow apertures, tall x-height, and cursive-like ampersand. On the Threads logo in the top-center of the home screen, the "@" symbol is the entire identity; nothing else competes.

**Key Characteristics:**
- True-black canvas (`#000000`) default with paper-white light mode (`#FFFFFF`)
- The "thread line" — a 1pt vertical rule from avatar to reply row, visually connecting author to conversation
- No hashtags, no trending panel, no repost badge — intentional minimalism
- 36pt circular avatar (larger than X's 32pt) — rounder, more photo-forward
- Instagram-coral heart (`#FE2C55`) — the only saturated color in the default feed
- Instagram Sans font (proprietary, 2022) — narrow, tall-x-height, warm
- Four-icon action row: heart, comment, repost, share — minimal compared to X's five
- Meta connection — Instagram account login, carry-over verified status, profile-pic linkage
- Activity feed replaces a notifications icon with a generic bell; "Follows", "Replies", "Mentions", "Quotes" filter chips above it
- Compose modal slides up full-screen with the thread-line persisting inside the compose view
- No in-app search by hashtag — search is people-first (username or display name)

## 2. Color Palette & Roles

### Primary
- **Meta Link Blue** (`#2D7FF9`): @mentions, URLs, inline links inside post body — inherited from Instagram's link color.
- **Link Blue Pressed** (`#1A5FD9`): Tapped state on links.
- **Primary White** (`#FFFFFF`): Primary CTA on dark mode ("Post", "Follow"), primary text on dark canvas.
- **Primary Black** (`#000000`): Primary canvas dark, primary CTA on light mode, primary text on light.

### Canvas & Surfaces (Dark / Default)
- **Canvas Black** (`#000000`): Primary OLED canvas.
- **Surface 1** (`#101010`): Card surfaces (rare — mostly on sheet modals), compose background.
- **Surface 2** (`#181818`): Input field background, higher elevation surfaces.
- **Divider** (`#222222`): Hairline between posts (lighter than X's `#2F3336` — Threads divider is subtler).
- **Thread Line** (`#333333`): The signature vertical rule from avatar to reply row.

### Canvas & Surfaces (Light)
- **Light Canvas** (`#FFFFFF`): Primary light canvas.
- **Light Surface 1** (`#FAFAFA`): Card surfaces, input field fills.
- **Light Surface 2** (`#F5F5F5`): Higher elevation (compose modal on light).
- **Light Divider** (`#DBDBDB`): Hairline in light mode.
- **Light Thread Line** (`#D9D9D9`): The thread-line in light mode.

### Text
- **Text Primary Dark** (`#F5F5F5`): Body on dark canvas — off-white, warm.
- **Text Primary Light** (`#000000`): Body on light canvas — pure black.
- **Text Secondary** (`#777777`): @handles, timestamps, metadata — shared across modes at the same contrast ratio.
- **Text Tertiary Dark** (`#4D4D4D`): Disabled text dark mode.
- **Text Tertiary Light** (`#999999`): Disabled text light mode.

### Social Action Colors
- **Like Coral** (`#FE2C55`): Active like / heart-fill state — inherited from Instagram's double-tap heart. NOT red, a pink-leaning coral.
- **Action Gray Default** (`#777777`): Comment, repost, and share icons in idle state.
- **Repost Active** (`#777777` with filled glyph variant): Repost stays gray — no role color — but switches from outlined to filled icon.

### Semantic
- **Error Red** (`#ED4956`): Post failed, delete confirmation, destructive actions (slightly less saturated than Instagram's error).
- **Success Green** (`#58C322`): Rare — used on "Posted" toast confirmations.
- **Instagram Verified Blue** (`#0095F6`): Inherited verified badge from Instagram (Threads doesn't issue its own).

### Meta Connection Accent
- **Instagram Gradient** (pink `#F56040` → purple `#A020F0` → blue `#405DE6`): Used only on the "Connect with Instagram" or Instagram-login flows — never in the feed.

## 3. Typography Rules

### Font Family
- **Primary**: `Instagram Sans` (proprietary, 2022, by Meta's type team) — a narrow, tall-x-height sans with a warm humanist quality and a distinctive cursive-like ampersand.
- **Fallback Stack**: `InstagramSans, -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **CJK / Non-Latin**: Instagram Sans ships with Arabic, Hebrew, and a Devanagari companion; CJK falls back to Apple's system fonts.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Instagram Sans | 17pt | 700 | 1.25 | -0.1pt | "Threads" logo top nav, "Activity", profile name header |
| Display Name | Instagram Sans | 15pt | 600 | 1.3 | 0pt | Post author's display name in post row |
| Post Body | Instagram Sans | 15pt | 400 | 1.4 | 0pt | The thread post text — primary reading |
| Quoted Post | Instagram Sans | 14pt | 400 | 1.35 | 0pt | Quoted post embed body |
| @Handle / Timestamp | Instagram Sans | 14pt | 400 | 1.3 | 0pt | `@username · 2h` — color `#777777` |
| Action Count | Instagram Sans | 13pt | 400 | 1.2 | 0pt | Count beside heart/comment/repost |
| Profile Follower Count | Instagram Sans | 14pt | 400 | 1.3 | 0pt | "1,234 followers" on profile |
| Profile Bio | Instagram Sans | 15pt | 400 | 1.4 | 0pt | Multi-line bio on profile header |
| Primary Button | Instagram Sans | 15pt | 600 | 1.0 | 0pt | "Post", "Follow", "Connect" |
| Secondary Button | Instagram Sans | 14pt | 600 | 1.0 | 0pt | "Following", "Message" |
| Compose Placeholder | Instagram Sans | 17pt | 400 | 1.3 | 0pt | "Start a thread..." in compose modal (larger than feed body) |
| DM Body | Instagram Sans | 15pt | 400 | 1.3 | 0pt | Direct message bubble text |
| Filter Chip | Instagram Sans | 14pt | 600 | 1.0 | 0pt | Activity filter pills ("Replies", "Follows") |
| Tab Label | n/a | — | — | — | — | Tab bar is icon-only — no labels |

### Principles
- **One optical size**: Instagram Sans doesn't ship with Display/Text optical variants in the Threads deploy — a single family handles all sizes.
- **Weight concentrated at 400 / 600 / 700**: Regular for body, semibold for names and buttons, bold for headlines. No light, no medium, no black.
- **Generous line height**: 1.4 on post body (vs 1.33 on X) is the difference that gives Threads its breathing room.
- **Dynamic Type respected**: Post body, bio, DM body, and compose placeholder all scale with user preference. Display name and timestamp cap at xxLarge to preserve the threaded layout.
- **Text color is structural, not decorative**: Primary text is off-white or pure black; secondary is `#777777`. Blue `#2D7FF9` only on links/mentions. Body text is never colored for emphasis.

## 4. Component Stylings

### Buttons

**Primary "Post" CTA (Compose Submit)**
- Shape: Pill, 500pt corner radius
- Background dark: `#F5F5F5` (off-white) | light: `#000000` (intentional inversion)
- Text: `#000000` dark mode | `#FFFFFF` light mode
- Padding: 8pt vertical, 20pt horizontal
- Disabled (no content): opacity 0.3
- Pressed: opacity 0.8 + soft haptic
- Font: Instagram Sans 15pt w600

**Follow Pill**
- Shape: Pill, 500pt corner radius
- Not-following: filled primary inverted (white on dark, black on light), "Follow" label
- Following: transparent with 1pt `#777777` border, secondary text color, "Following" label
- Padding: 8pt vertical, 20pt horizontal
- Minimum width: 92pt
- Font: Instagram Sans 14pt w600
- Pressed "Following": opens a confirmation action sheet ("Unfollow @user?")

**Action Icons (Heart, Comment, Repost, Share)**
- Four icons only — no bookmark or views icon
- Size: 22pt glyph, 44pt hit area
- Default: `#777777` outlined
- Active Like: `#FE2C55` filled heart (coral, not red)
- Active Repost: `#777777` but icon switches to filled two-arrows variant — no color change
- Comment / Share: always outlined, never activate with color
- Tap animation: icon scales 0.85 → 1.15 → 1.0 spring over 300ms + soft haptic
- Like tap: paired with `.impact(.light)` + count tick-up

**"Connect with Instagram" CTA (Onboarding / Migration)**
- Shape: Pill, 500pt corner radius
- Background: Instagram gradient (pink → purple → blue linear 135deg)
- Text: `#FFFFFF` Instagram Sans 15pt w600
- Padding: 12pt vertical, 24pt horizontal
- Only appears on onboarding and on Meta-connection prompts — never in the feed

### Cards & Containers

**Thread Post Row (The Post)**
The signature component.
- Full-width, canvas background, divided by 1pt `#222222` hairline at bottom
- Leading: 36pt circular avatar, 16pt inset from left
- **Thread line**: starts 4pt below the avatar's bottom edge, 1pt wide, `#333333`, extends down to just above the reply row (only shown if the post has replies nested below OR is the root of a visible thread)
- Display name row: Instagram Sans 15pt w600, primary text; trailing `@handle · 2h` in 14pt w400 `#777777`; overflow `...` 18pt far trailing
- Post body: Instagram Sans 15pt w400 with 1.4 line height, primary text, aligned to avatar gutter (68pt from left)
- Media (optional): 16:9 or 1:1 rounded-16pt media below body
- Action row: 4 icons evenly distributed at 22pt with counts beside heart/comment — no views icon, no bookmark
- Padding: 16pt top, 12pt bottom
- Tap: opens thread detail view; tap avatar: opens author profile

**Nested Reply Row**
- Same structure as a Thread post but smaller avatar (28pt) and indented 48pt
- The thread line from the parent post continues downward and connects to this reply's avatar
- This creates the visual "branching" of a conversation

**Profile Header**
- Background: canvas
- Layout: 80pt avatar on leading side + display name + @handle + bio + follower count on trailing
- "Instagram account" badge shown as small glyph + link to Instagram profile
- Below: "Follow" pill (if not own profile) + "Mention" or "Profile" button
- Tab bar underneath: "Threads" / "Replies" / "Reposts" as text labels with an animated underline indicator

**Quoted Post Embed**
- Appears inside a parent post when quote-reposting
- 1pt `#333333` border, 12pt corner radius, 12pt padding
- Mini author row: 24pt avatar, 14pt w600 name, handle, timestamp
- Body: 14pt w400 quoted content
- Media scales down to fit embed width with 8pt radius

**Compose Modal**
- Full-screen, slides up from bottom
- Background: canvas (dark or light based on mode)
- Thread line appears inside the compose view — running from the user's avatar at the top down to a "+ Add to thread" button at the bottom (enabling multi-post threads)
- Placeholder: "Start a thread..." in Instagram Sans 17pt w400 `#777777`
- Top: "Cancel" text button leading, "Post" pill trailing
- Bottom toolbar: media picker, GIF, poll, location, tag-people icons at 22pt
- "Your followers can reply" audience selector below toolbar — pill button

**Activity Filter Chip Row**
- Horizontal scroll of 5 chips: "All", "Follows", "Replies", "Mentions", "Quotes", "Reposts", "Verified"
- Chip: 36pt tall, 500pt radius pill
- Unselected: transparent fill, 1pt `#333333` border, `#F5F5F5` text
- Selected: `#F5F5F5` fill, `#000000` text
- Font: Instagram Sans 14pt w600
- Padding: 8pt vertical, 16pt horizontal

### Navigation

**Bottom Tab Bar**
- Height: 49pt + safe area (iOS default)
- Background: `rgba(0,0,0,0.9)` with `.regularMaterial` blur on dark; `rgba(255,255,255,0.9)` light
- Tabs (5): Home (`house`), Search (`magnifyingglass`), Compose (`plus.square`), Activity (`heart`), Profile (user avatar)
- Icon size: 26pt
- Active color: `#F5F5F5` dark / `#000000` light (stark contrast, no tinted color)
- Inactive: `#777777`
- Icon-only (no labels)
- Active icon uses filled variant; inactive uses outlined

**Top Nav**
- Height: 44pt + safe area
- Center: Threads logo (a stylized `@` glyph, 28pt, monochrome primary text color)
- Leading: profile icon (tap opens side menu) — only on Home screen
- Trailing: filter/sort toggle on Home (open a sheet to switch between "For you" and "Following")
- Background on scroll: `rgba(0,0,0,0.8)` blur or `rgba(255,255,255,0.8)` light

**Profile Tab Strip ("Threads" / "Replies" / "Reposts")**
- Three text labels evenly distributed, 44pt tall
- Active: primary text color with a full-width 2pt underline `#F5F5F5` / `#000000`
- Inactive: `#777777`
- Swipe between tabs changes content

### Input Fields

**Compose Text Area (inside compose modal)**
- Multi-line, grows vertically
- Font: Instagram Sans 17pt w400 — larger than feed body for comfortable typing
- Placeholder: "Start a thread..." in `#777777`
- No visible border or underline — just text on canvas
- Cursor: primary text color

**Search Bar**
- Height: 40pt
- Corner radius: 10pt (rounded-rect, not pill — distinct from X's pill)
- Background: `#181818` dark / `#F5F5F5` light
- Leading: `magnifyingglass` 16pt in `#777777`
- Placeholder: "Search" in 15pt w400 `#777777`
- Focus: no border change — cursor only

**DM Compose**
- Height: 40pt minimum, grows vertically
- Corner radius: 20pt (pill)
- Background: `#181818` dark / `#F5F5F5` light
- Leading: camera / media picker 20pt
- Trailing: send arrow (`#2D7FF9`) when text present

### Distinctive Components

**The Thread Line**
The vertical 1pt rule that connects a post's avatar to its reply conversation below. Present on any post that is root of a thread or part of a multi-post chain.
- Color: `#333333` dark / `#D9D9D9` light
- Width: 1pt
- Start: 4pt below the avatar's bottom edge
- End: 4pt above the action row (on root posts) or continues into the next nested reply's avatar
- On compose screen: runs from the user's avatar down to a "+ Add to thread" interactive dot at the bottom

**"@" Logo Glyph**
- Stylized ampersand-style at-sign, custom for Threads
- Monochrome — primary text color
- Used at 28pt in top nav, 96pt on splash, 14pt in activity badges
- Never colored, never gradient, never animated

**Multi-Post Thread Indicator**
- When the user composes multiple posts in a single thread, each post sits on canvas with the thread line running between them
- A small "+ Add to thread" dot button sits below the last compose box — tap to add another post
- Reorder handle (`line.3.horizontal`) appears between posts when hovering

**Quoted Repost Banner**
- When someone reposts or quote-reposts a post, a "Reposted by @username" 13pt w400 `#777777` banner sits above the post row
- Leading glyph: small 12pt repost arrows icon in `#777777`
- Tap banner: opens reposter's profile

**Activity Post Row**
- Variant of the Thread post row tuned for activity feed
- Leading: 36pt avatar + small 14pt glyph overlay (heart for likes, quote marks for mentions, arrows for reposts)
- Body: "@user liked your post" in 15pt w400 primary text
- Snippet: first line of the related post in 14pt w400 `#777777`, 1-line clamp
- Trailing: timestamp + action (Follow-back pill if relevant)

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 56
- Standard horizontal margin: 16pt
- Post content indent (aligned to avatar gutter): 68pt from left edge (16pt margin + 36pt avatar + 16pt gutter)
- Reply nesting indent: 48pt additional per level

### Grid & Container
- Content width: full device width with 16pt horizontal safe margins
- Post body column: screen width minus ~84pt (margin + avatar + gutter)
- Media inside post: full post-body column width, 16pt corner radius
- Profile grid (Replies / Reposts tabs): same full-width rows — Threads doesn't use a grid for posts

### Whitespace Philosophy
- **Generous vertical breathing**: Post rows at 120-180pt depending on body length — more than X's 80-120pt — reinforcing the "slower conversation" feel
- **Thread line as structural whitespace**: the 1pt rule acts as a visual guide through the negative space between avatar and reply
- **No cards, no shadows**: posts sit directly on canvas separated by hairlines
- **Sacred compose**: compose modal takes the full screen — no mini-compose, no quick-tweet inline

### Border Radius Scale
- Hairline (0pt): Divider between posts
- Small (8pt): Quoted post media inside embeds
- Search Bar (10pt): Rounded-rect search field
- Media (16pt): Post images, videos, quoted post embeds
- DM Bubble (20pt): Direct message bubbles — rounder than posts for conversational feel
- Full Pill (500pt): Follow button, Post CTA, filter chips, audience selector
- Circle (50%): Avatars (all), tab-bar icons

## 6. Depth & Elevation

Threads is completely flat. The only spatial hierarchy comes from the thread-line and hairlines. Shadows appear only on floating modals and the compose sheet backdrop.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, no elevation | Posts, feed, nav bars, tab bar |
| Hairline (Level 0.5) | 1pt `#222222` dark / `#DBDBDB` light | Between posts, list rows, sections |
| Thread Line (structural) | 1pt vertical rule `#333333` / `#D9D9D9` | From avatar to reply row (not a shadow, a rule) |
| Sheet (Level 1) | `rgba(0,0,0,0.6) 0 -4px 20px` | Bottom sheet menus (overflow actions, share sheet) |
| Modal Backdrop | `rgba(0,0,0,0.7)` | Behind compose modal on light mode, media lightbox |
| Top Nav Blur | `.regularMaterial` over canvas at 80% opacity | Floating top nav on scroll |

**Shadow Philosophy**: Threads intentionally has no shadow-based depth in the feed. The thread line provides the only vertical visual cue between a post and its reply — a rule, not a shadow. Bottom sheets get a subtle upward shadow only to separate from the canvas.

### Motion
- **Like tap**: heart scales 0.85 → 1.15 → 1.0 spring over 300ms, coral fills from outline, `.impact(.light)` haptic, count ticks up beside icon
- **Comment tap**: icon scales 0.9 → 1.05 → 1.0, navigates to compose-reply view sliding up from bottom
- **Repost tap**: icon switches from outline to filled variant via cross-fade 200ms, soft haptic
- **Share tap**: opens iOS native share sheet
- **Feed pull-to-refresh**: the "@" logo at the top of the feed pulses and subtly rotates as the user pulls
- **Compose open**: modal slides up from bottom 320ms spring, thread line animates from 0 → full height over 400ms
- **Add-to-thread**: when user taps "+ Add to thread" in compose, a new compose box slides in with thread-line extending to it
- **Tab switch**: instant cross-fade between tabs with no slide direction
- **Profile tab strip switch**: underline indicator animates horizontally 250ms ease-out; content cross-fades

## 7. Do's and Don'ts

### Do
- Use pure black `#000000` as the default dark canvas — Threads opens dark like X
- Use the thread line (1pt vertical rule from avatar to reply row) — it's the structural signature of the app
- Use 36pt circular avatars — larger than X's 32pt, reflecting the more photo-forward Instagram heritage
- Use Instagram-coral (`#FE2C55`) for the active like heart — NOT red, a pink-leaning coral
- Keep the action row to FOUR icons: heart, comment, repost, share — no bookmark, no views
- Use Instagram Sans at weights 400/600/700 only — no light, no thin
- Keep the tab bar icon-only (no labels) — matches X's minimalism
- Use 1.4 line height on post body — the generous breathing room is part of the voice
- Cross-fade repost icon from outline to filled (no color change) — repost stays neutral
- Keep hashtags OUT of the feed — Threads has no trending panel, no hashtag search
- Use Instagram gradient only for "Connect with Instagram" flows — never in the feed

### Don't
- Don't use shadows between posts — hairlines are the brand-correct separator
- Don't render trending topics, hashtag panels, or a "Discover" sidebar — Threads is intentionally minimalist
- Don't colorize the repost icon green (that's X's pattern) — repost on Threads stays gray, icon fills as the active indicator
- Don't add a bookmark icon to the action row — Threads omits it
- Don't use pure red `#FF0000` for the like heart — it's Instagram-coral `#FE2C55`
- Don't use verified checkmark in Threads' own blue — verification inherits from Instagram (`#0095F6`)
- Don't use cards or containers for posts — they sit directly on canvas
- Don't use medium font weights (500) — Instagram Sans weights are 400 / 600 / 700
- Don't remove the thread line on multi-post threads — it IS the thread
- Don't animate aggressively — Threads motion is calm, 250-400ms, matching the slower scroll feel
- Don't use uppercase button labels — "Post", "Follow" are sentence case

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 32pt avatars; post body column narrows to ~276pt |
| iPhone 13/14/15 | 390pt | Standard 36pt avatars; post body column ~302pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in nav — `@` logo centers away from the island |
| iPhone 15/16 Pro Max | 430pt | 36pt avatars; post body column ~342pt; media scales up |
| iPad | 768pt+ | Centered single-column feed at 600pt max width with surrounding canvas — Threads does not expand into multi-column on iPad |

### Dynamic Type
- Post body, bio, DM body, compose placeholder: full scale
- Display name, @handle, timestamp: cap at xxLarge
- Action counts: fixed 13pt (layout-sensitive)
- Tab icons: fixed (icon-only bar)
- Quoted post body: scales with post body but at relative 93% size

### Orientation
- Home / Activity / Search / Profile / DMs: **portrait-locked**
- Media lightbox (photo / video viewer): **rotates freely**
- Video playback fullscreen: **rotates freely**

### Touch Targets
- Action icons: 22pt glyph, 44pt hit area
- Tab icons: 26pt glyph, 49pt hit (full tab bar height)
- Avatar tap: 36pt glyph, 44pt hit
- Follow pill: 32pt tall, 92pt wide minimum
- Thread line: not interactive (purely structural)
- Compose FAB equivalent: the Compose tab in the bottom bar is the entry point — no floating circle

### Safe Area Handling
- Top: top nav respects safe area + Dynamic Island; `@` logo shifts around the island
- Bottom: tab bar respects home indicator
- Sides: 16pt insets on all content; bleed-to-edge only for fullscreen media and profile header backgrounds

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark/default): `#000000`
- Canvas (light): `#FFFFFF`
- Surface 1 (dark): `#101010`
- Divider (dark): `#222222`
- Thread Line (dark): `#333333`
- Text primary (dark): `#F5F5F5`
- Text primary (light): `#000000`
- Text secondary: `#777777`
- Link Blue: `#2D7FF9`
- Like Coral: `#FE2C55`
- Error Red: `#ED4956`
- Instagram Verified: `#0095F6`

### Example Component Prompts
- "Build a Threads post row in SwiftUI: full-width on `#000000` canvas, 16pt top / 12pt bottom padding, 1pt `#222222` divider bottom. Leading 36pt circular avatar 16pt from left. Below avatar: 1pt vertical thread-line in `#333333` starting 4pt below avatar bottom and extending to 4pt above the action row. Right of avatar: display name 'jane_doe' in Instagram Sans 15pt w600 `#F5F5F5`, then `@janedoe · 2h` in 14pt w400 `#777777`, trailing `ellipsis` 18pt. Body indented to 68pt: post body in Instagram Sans 15pt w400 with 1.4 line height. Action row below body (4 icons only): heart-outline, comment, repost-outline, share-outline at 22pt `#777777` with optional count beside first two."
- "Create the Threads like tap animation: outlined heart turns filled in `#FE2C55` (Instagram coral, NOT red), heart scales 0.85 → 1.15 → 1.0 spring over 300ms, paired with `.impact(.light)` haptic. Count number ticks up beside the heart with a 120ms fade-slide. No particle burst — Threads keeps it calmer than X."
- "Design the Threads compose modal: full-screen slide-up, canvas background. Top bar: 'Cancel' text button leading (Instagram Sans 15pt w400 `#F5F5F5`), 'Post' pill trailing (Instagram Sans 15pt w600, off-white fill `#F5F5F5` with black text on dark mode, padding 8pt×20pt, 500pt radius, disabled at 0.3 opacity when empty). Below: 36pt avatar leading, thread-line running down the compose box, multiline text field with Instagram Sans 17pt w400 placeholder 'Start a thread...' in `#777777`. Bottom toolbar: media / GIF / poll / location / tag-people icons at 22pt `#777777`, plus '+ Add to thread' dot button below current compose box for multi-post threads."
- "Build the Threads tab bar: 49pt + safe area, icon-only (no labels). Tabs: home, search, plus-square (compose), heart (activity), and user avatar (profile). Icons at 26pt, active tint `#F5F5F5` dark / `#000000` light, inactive `#777777`. Background: `rgba(0,0,0,0.9)` with `.regularMaterial` blur dark, `rgba(255,255,255,0.9)` blur light. Active icon uses filled SF Symbol variant; inactive uses outlined."
- "Build the Activity filter chip row: horizontal scroll of pill chips at 36pt tall, 500pt radius, Instagram Sans 14pt w600. Unselected: transparent fill + 1pt `#333333` border + `#F5F5F5` text. Selected: `#F5F5F5` fill + `#000000` text + no border. Chips: 'All', 'Follows', 'Replies', 'Mentions', 'Quotes', 'Reposts', 'Verified'. Tap a chip to animate fill in 200ms + soft haptic."
- "Design a nested reply row: indented 48pt from parent, 28pt circular avatar, thread-line continuing from parent above connecting to this reply's avatar top. Display name 15pt w600, handle/timestamp 14pt w400 `#777777`, body 15pt w400 with 1.4 line height. Same 4-icon action row at 22pt. Nested replies can branch further — each level indents another 48pt."

### Iteration Guide
1. Canvas is pure black `#000000` dark / pure white `#FFFFFF` light — app opens dark
2. The thread line (1pt vertical rule from avatar to reply row) is the structural signature — never omit it
3. Avatars are 36pt circular — larger than X's 32pt, reflecting Instagram heritage
4. Action row has FOUR icons only: heart, comment, repost, share — no bookmark, no views
5. Like heart is Instagram-coral `#FE2C55`, NOT red — and it's the only saturated color in the default feed
6. Typography is Instagram Sans at weights 400/600/700 — generous 1.4 line height on body
7. Tab bar is icon-only at 26pt — no labels, matches X's minimalism
8. No hashtags, no trending panel, no bookmark — intentional minimalism
9. Compose modal is full-screen with the thread-line carrying through — signature interaction
10. Repost icon swaps outline-to-filled without color change — only heart activates with color
