# Design System Inspiration of X (Twitter) (iOS)

## 1. Visual Theme & Atmosphere

X's iOS app is a newsroom collapsed into a phone screen. The default canvas is pure black (`#000000`) — a deliberate choice that pairs OLED battery savings with the stark, reportage-style aesthetic X has cultivated since the 2023 rebrand from Twitter. Every post sits on a full-bleed black sheet divided by hairline rules in cool neutral gray (`#2F3336`), and the UI chrome is monochrome except for one surviving accent: the legacy Twitter Blue (`#1D9BF0`) used for links, mentions, the verified checkmark on legacy accounts, and the "Reply" action color. The feeling is dense, serious, and real-time — closer to a Bloomberg terminal than a social app.

The post is the unit of everything. A tweet (or "post") fills the viewport edge-to-edge at 16pt horizontal insets, anchored by a 32pt circular avatar on the leading side and a trailing `...` overflow. Below the text: a horizontal action row of five interactions — reply, repost, like, views, share — each a thin-line icon in neutral gray that animates to its role color on tap (reply to blue, repost to green `#00BA7C`, like to red/pink `#F91880`). The animation choreography on these taps is X's most recognizable micro-interaction: a subtle scale bounce on the icon, a haptic tick, and the count number ticking up beside it. Nothing else in the app has this level of polish — the rest is utilitarian.

Typography is Chirp (proprietary, 2021, commissioned by Twitter from Swiss Typefaces & Grilli Type), a grotesque sans with a deliberately awkward italic and sharp terminals tuned for tight-line reading at 15pt. Body type sits at 15pt weight 400 on a 20pt line height — a conversational density that fits 3-5 posts per viewport on a standard iPhone. Headlines (display names, trending titles, hero moments) step up to 17-20pt weight 700. There is no display-size type anywhere in the core app; the biggest moment is the user-profile header name at 20pt. Everything else is UI-first, information-first, and flat. A dim mode canvas `#15202B` (a desaturated navy) offers an alternative for users who want dark-without-contrast, and a high-contrast light mode on `#FFFFFF` is available but the app's gravity is black.

**Key Characteristics:**
- Pure-black OLED canvas (`#000000`) with dim-mode `#15202B` and light-mode `#FFFFFF` alternates
- Twitter Blue (`#1D9BF0`) as the structural accent — links, replies, active tab indicator, verified badges
- Repost green (`#00BA7C`) and like pink (`#F91880`) as the only other accent colors — one per social action
- Chirp font (2021) — grotesque sans with idiosyncratic italic, weights 400/500/600/700
- Hairline dividers (`#2F3336` dark / `#EFF3F4` light) — not rules, not cards, just 1pt separators
- Dense, info-heavy layout — 3-5 posts per viewport, minimal whitespace between
- 32pt circular avatars anchoring every post row
- Animated action row with per-action color transitions (blue/green/pink) + haptic ticks
- X logo replaces the bird — edge-to-edge glyph, monochrome white/black depending on theme
- Trending sidebar / "For You" algorithmic feed / "Following" chronological feed split at the top
- Community Notes under posts — a gray bordered card with collapsible context

## 2. Color Palette & Roles

### Primary
- **Twitter Blue** (`#1D9BF0`): Legacy accent — links, @mentions, hashtags, active tab icon, reply action, verified (legacy) checkmark. Still present across the UI despite the X rebrand.
- **Blue Pressed** (`#1A8CD8`): Pressed state on blue CTAs and link taps.
- **X White** (`#FFFFFF`): Primary "X" logotype on dark canvas; primary CTA background on dark mode (the "Post" button is white-on-black, a deliberate inversion from the old blue bird button).
- **X Black** (`#000000`): Primary canvas (dark); "Post" button text on dark mode; logotype on light canvas.

### Canvas & Surfaces (Dark / Default)
- **Canvas Black** (`#000000`): Primary OLED canvas.
- **Surface 1** (`#16181C`): Card surfaces, sheet backgrounds, compose modal.
- **Surface 2** (`#1E2024`): Higher elevation — DM bubbles (received), settings list rows.
- **Divider** (`#2F3336`): The signature 1pt hairline between posts, list rows, nav sections.
- **Dim Canvas** (`#15202B`): Dim-mode alternative canvas (navy-tinted dark, not true black).
- **Dim Surface 1** (`#192734`): Cards on dim mode.
- **Dim Divider** (`#38444D`): Dividers on dim mode.

### Canvas & Surfaces (Light)
- **Light Canvas** (`#FFFFFF`): Primary light canvas.
- **Light Surface 1** (`#F7F9F9`): Card surfaces, filter chip fills, search bar fill.
- **Light Surface 2** (`#EFF3F4`): Higher elevation — compose modal background, selected row.
- **Light Divider** (`#EFF3F4`): Hairlines in light mode.

### Text
- **Text Primary Dark** (`#E7E9EA`): Body text on dark canvas — not pure white, slightly warm off-white for readability.
- **Text Primary Light** (`#0F1419`): Body text on light canvas.
- **Text Secondary Dark** (`#71767B`): @handles, timestamps, metadata on dark.
- **Text Secondary Light** (`#536471`): @handles, timestamps on light.
- **Text Tertiary** (`#71767B`): Disabled labels, very low-emphasis copy (shared across modes at lower contrast).

### Social Action Colors
Each of the five post-action icons has its own activated color. The colors only appear when the action is toggled on — default state is always `#71767B` (secondary gray).
- **Reply Blue** (`#1D9BF0`): Same as Twitter Blue.
- **Repost Green** (`#00BA7C`): Active repost (retweet) state and the "Reposted by…" banner.
- **Like Pink** (`#F91880`): Active like / heart-fill state. Not red — a saturated magenta-pink.
- **Views Gray** (`#71767B`): Views icon stays neutral (it's not an action, it's a count).
- **Bookmark Blue** (`#1D9BF0`): Bookmark active state — same blue as reply.

### Semantic
- **Verified Gold** (`#EAB308`): Government-official account badge.
- **Verified Gray** (`#829AAB`): Business verification badge.
- **Error Red** (`#F4212E`): Post failed, connection lost, destructive confirmations.
- **Community Notes Surface** (`#16181C` dark / `#F7F9F9` light): The bordered context-note card under misleading posts.

## 3. Typography Rules

### Font Family
- **Primary**: `Chirp` (proprietary, 2021, by Swiss Typefaces & Grilli Type for Twitter) — grotesque sans with distinctive italic, shipped in UI and Display optical sizes.
- **Fallback Stack**: `TwitterChirp, -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **CJK / Non-Latin**: X falls back to system fonts for Chinese/Japanese/Korean/Arabic/Hebrew — iOS SF system fonts carry non-Latin scripts; Chirp is Latin-plus-extended only.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Chirp Display | 20pt | 700 | 1.2 | -0.2pt | "Home", "Notifications", profile name on header |
| Section Header | Chirp Display | 17pt | 700 | 1.25 | -0.1pt | "What's happening", "Trends for you" |
| Display Name | Chirp Display | 15pt | 700 | 1.3 | 0pt | Post author's display name in post row |
| Post Body | Chirp UI | 15pt | 400 | 1.33 | 0pt | The tweet text itself — the core unit |
| Quoted Post | Chirp UI | 14pt | 400 | 1.3 | 0pt | Quote-tweet embed body |
| @Handle / Timestamp | Chirp UI | 15pt | 400 | 1.3 | 0pt | `@username · 2h` next to display name — color `#71767B` |
| Action Count | Chirp UI | 13pt | 400 | 1.2 | 0pt | The number next to reply/repost/like icons |
| Trending Topic | Chirp Display | 15pt | 700 | 1.25 | 0pt | Trending sidebar topic title |
| Trending Meta | Chirp UI | 13pt | 400 | 1.2 | 0pt | "Trending in Technology · 24.5K posts" |
| Primary Button | Chirp UI | 15pt | 700 | 1.0 | 0pt | "Post", "Follow", "Subscribe" |
| Tab Label | n/a | — | — | — | — | Tabs are icon-only on X (no labels) |
| DM Body | Chirp UI | 15pt | 400 | 1.3 | 0pt | Direct message bubble text |
| DM Timestamp | Chirp UI | 11pt | 400 | 1.2 | 0pt | Above each DM cluster, in gray |
| Link / @Mention | Chirp UI | inherits | 400 | inherits | 0pt | Blue `#1D9BF0` inline inside post body |

### Principles
- **Two optical variants**: Chirp ships UI (tight, optimized for 13-16pt) and Display (more open, for 17pt+). The app switches at the 17pt threshold.
- **Italic is idiosyncratic**: Chirp's italic has distinctive angular lowercase `a` and `g` — use italic only when semantically italic (book titles, emphasis in quoted text), never as a design accent.
- **Weight concentrated at 400 / 500 / 700**: Regular for body, medium for certain labels, bold for names and titles. No thin, no black.
- **Dynamic Type on body**: Post body, DM body, and quoted text all scale with user preference. Display name and timestamp cap at 17pt to preserve post-row layout.
- **Color on text is structural**: Blue for links/mentions, secondary gray for handles/timestamps, primary off-white for body. Never colorize body text for decoration.

## 4. Component Stylings

### Buttons

**Primary "Post" CTA (The Floating Action Button)**
- Shape: Circle, diameter 56pt, floated bottom-right 16pt from edge, 16pt from tab bar
- Background dark mode: `#FFFFFF` (white) — intentional inversion
- Background light mode: `#0F1419` (near-black)
- Icon: feather / pen SF-symbol-like glyph, 24pt, color inverts to match contrast
- Shadow: `rgba(0,0,0,0.4) 0 4px 12px` (dark mode has no shadow effect, so subtle)
- Pressed: scale 0.95 + soft haptic
- Appears on Home, Search, Notifications; hidden on Messages and Profile

**Primary "Post" Button (In-Compose)**
- Shape: Pill, 500pt corner radius
- Background: `#EFF3F4` (dark) / `#0F1419` (light)
- Text: Chirp 15pt w700, `#0F1419` dark / `#FFFFFF` light
- Padding: 8pt vertical, 20pt horizontal
- Disabled (no text entered): opacity 0.5
- Pressed: opacity 0.9

**Primary Follow / Subscribe Pill**
- Shape: Pill, 500pt corner radius
- Not-following state: `#EFF3F4` fill (dark) or `#0F1419` fill (light), contrasting text, "Follow" label
- Following state: transparent fill, `#536471` 1pt border, "Following" label, secondary text color
- Pressed on "Following": opens an action sheet with Unfollow option
- Padding: 8pt vertical, 16pt horizontal, minimum width 80pt
- Font: Chirp 14pt w700

**Action Icons (Reply, Repost, Like, Views, Share)**
- Size: 18.75pt (the precise size X uses) glyph; 44pt hit area
- Default: `#71767B` outlined
- Active Reply: `#1D9BF0` outlined (touched, opens composer)
- Active Repost: `#00BA7C` filled arrows icon
- Active Like: `#F91880` filled heart
- Active Bookmark: `#1D9BF0` filled bookmark
- Tap animation: icon scales 0.85 → 1.1 → 1.0 spring + count ticks up with a micro slide-up transition, 200ms, paired with `.impact(.light)` haptic
- Like tap: bursts 6 small pink particles from the heart center on transition to active

**Text Button ("Show more replies", "Show this thread")**
- Font: Chirp 15pt w400, `#1D9BF0`
- No underline, 44pt hit area
- Sits inline after post body when conversation continues

### Cards & Containers

**Post Row (The Tweet)**
- Full-width, black background, divided by 1pt `#2F3336` hairline at the bottom
- Leading: 32pt circular avatar, 16pt offset from left edge
- Trailing of avatar-row: display name (Chirp 15pt w700) + @handle + `·` + timestamp + `...` overflow (20pt glyph, trailing-aligned)
- Body: post text Chirp 15pt w400 `#E7E9EA`, full-width minus leading avatar + 12pt gutter
- Media (optional): 16:9 or 1:1 rounded (16pt radius) image/video below text, edge-to-edge within the gutter
- Action row: 5 icons evenly distributed — reply, repost, like, views, bookmark — plus trailing share arrow at the far right; icons and counts paired inline
- Padding: 12pt top, 12pt bottom (divider sits exactly on the bottom edge)
- Tap post: opens thread detail; tap avatar: opens author profile

**Quoted Post Embed**
- Appears inside a parent post when quote-tweeting
- Background: same as parent (transparent)
- Border: 1pt `#2F3336` dark / `#CFD9DE` light
- Corner radius: 16pt
- Padding: 12pt all sides
- Contains: mini author row (24pt avatar + name + @handle + timestamp in 14pt) + quoted post body in 14pt

**Community Note Card**
- Appears under misleading posts when a Note has been rated helpful
- Background: same as canvas (transparent)
- Border: 1pt `#2F3336`
- Corner radius: 16pt
- Header: "Readers added context they thought people might want to know" in 13pt w700 `#71767B`
- Body: note text in 15pt w400 `#E7E9EA`
- Footer: "Do you find this helpful?" with thumbs-up / thumbs-down tap options

**Trending Topic Row**
- 1pt hairline divider above
- Left: category label "Trending in Technology" in 13pt w400 `#71767B`
- Center: trending topic in 15pt w700 `#E7E9EA`
- Right: "24.5K posts" in 13pt w400 `#71767B`
- Trailing `...` overflow 16pt
- Tap: opens search results for that trend

**DM Bubble**
- Sent bubble: `#1D9BF0` background, white text
- Received bubble: `#2F3336` dark / `#EFF3F4` light background, primary text color
- Corner radius: 18pt (rounder than posts — DMs feel conversational)
- Max width: 75% of screen
- Padding: 10pt vertical, 14pt horizontal
- Tail: no tail — just the rounded rect

### Navigation

**Bottom Tab Bar**
- Height: 49pt + safe area (iOS default)
- Background: `rgba(0,0,0,0.85)` with `.regularMaterial` blur on dark mode, `rgba(255,255,255,0.85)` light
- Tabs (5): Home (`house`), Search (`magnifyingglass`), Communities (`person.3`), Notifications (`bell`), Messages (`envelope`)
- Icons only — no labels, this is X's signature
- Icon size: 26pt
- Active color: `#E7E9EA` dark / `#0F1419` light (a stark contrast, not blue)
- Inactive: `#71767B`
- Active icon: filled variant; inactive: outlined

**Top Nav (Home / Profile)**
- Height: 44pt + safe area
- Leading: profile avatar 32pt circular — tap opens side drawer with account switcher, Premium, Lists, etc.
- Center: X logo (monochrome) 22pt glyph
- Trailing: Grok sparkles icon (28pt) or settings gear
- Background on scroll: `rgba(0,0,0,0.7)` with blur

**Feed Filter Bar ("For You" / "Following")**
- Sits below top nav, 48pt tall
- Two tappable labels: "For you" and "Following" — font Chirp 15pt w700
- Active tab: primary text color with a 40pt-wide × 4pt-tall `#1D9BF0` underline, 500pt corner radius
- Inactive tab: `#71767B`
- Swipe between the two feeds changes the active indicator

**Side Drawer (Opened from avatar tap)**
- Slides in from the left, 85% of viewport width
- Top: avatar, display name, @handle, following/followers count
- List: Profile, Premium, Lists, Bookmarks, Monetization, Settings
- Background: canvas color
- Bottom: account switcher chevron + logout

### Input Fields

**Compose Box (Full-screen modal)**
- Modal presents up from the bottom, full height
- Background: canvas
- Top nav: "Cancel" text button left, "Post" pill right
- Avatar on left (40pt)
- Text field: multiline, Chirp 18pt w400 (slightly larger than feed), placeholder "What's happening?" in `#71767B`
- Below text field: character counter ring (if near limit), media/poll/GIF/location icon row
- Bottom bar: audience selector ("Everyone", "Circle", "Verified") as a pill button, then reply controls

**Search Bar**
- Height: 38pt
- Corner radius: 500pt (full pill)
- Background: `#202327` dark / `#EFF3F4` light
- Leading: `magnifyingglass` icon 16pt in `#71767B`
- Placeholder: "Search" in 15pt w400 `#71767B`
- Text: primary color when typed
- No border, no focus ring change — cursor only

**DM Compose Field**
- Height: 36pt (grows vertically on long messages)
- Corner radius: 18pt (pill)
- Background: `#202327` dark / `#EFF3F4` light
- Leading: media picker + GIF + sticker icons
- Trailing: send arrow (`#1D9BF0`) when text present, `+` for media when empty

### Distinctive Components

**X Logo Glyph**
- The replacement for the bird logo since 2023
- Monochrome — white on dark, black on light
- Used at 22pt in top nav, 120pt on splash, 16pt in Reposted-by banner
- Never colored, never gradient, never animated on its own

**Reposted-By Banner**
- Appears above a post that someone you follow reposted
- "🔁 @username reposted" in 13pt w700 `#71767B`
- The repost glyph is the green arrows icon but shown in secondary gray
- Tap `@username` to open their profile

**Like Burst Animation**
- On tap, heart fills pink `#F91880`
- 6 small pink particles shoot outward in a hexagonal pattern for 400ms
- Heart scales 0.85 → 1.2 → 1.0 spring
- Haptic: `.impact(.light)`
- Count number slides up: old number swipes up and fades, new number fades in from below

**Verified Checkmark**
- 16pt blue `#1D9BF0` filled checkmark-in-circle glyph
- Sits inline after display name with 2pt leading gap
- Gold version (`#EAB308`) for government officials
- Gray version (`#829AAB`) for business accounts

**Spaces Indicator (Live Audio)**
- When a Space is live in the user's network, a purple `#7856FF` pulsing badge appears at the top of the feed
- "LIVE" label + host avatars in a cluster
- Tap opens the Space player

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 56
- Standard horizontal margin: 16pt — but post content begins at 68pt from the leading edge (16pt margin + 32pt avatar + 12pt gutter), and actions row aligns to that same 68pt indent
- Vertical post spacing: 0pt (posts touch; hairline divider separates)

### Grid & Container
- Content width: full device width with 16pt horizontal safe margins
- Post body column: screen width minus 84pt (accounts for avatar + gutter)
- Media inside post: full post-body column width, 16pt corner radius
- Trending sidebar: not shown on iPhone portrait; only on iPad split view or larger breakpoints

### Whitespace Philosophy
- **High density**: Posts touch — 3-5 fit a typical iPhone viewport. X is built for fast scroll-reading.
- **Hairlines not cards**: Depth is communicated with 1pt dividers, not shadows or card surfaces. The feed is a flat river of posts.
- **Generous tap targets**: Despite visual density, every interactive element (action icons, `...` menu, avatar) has a 44pt minimum hit area.

### Border Radius Scale
- Hairline (0pt): Post rows themselves have no radius — the divider separates them
- Small (4pt): Verified badge, legacy avatars (rarely rectangular)
- Media (16pt): Images, videos, quoted posts, Community Notes
- DM Bubble (18pt): Direct message bubbles
- Full Pill (500pt): Follow button, Post CTA, search bar, filter tabs, audience selector
- Circle (50%): Avatars (all user photos), FAB Post button, action icon hit areas

## 6. Depth & Elevation

X's depth model is flat by design. Content sits on the canvas and is separated by hairlines, not elevated on shadowed cards. The only prominent shadow is the floating Post CTA.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, no elevation | Posts, feed, nav bars, tab bar |
| Hairline (Level 0.5) | 1pt solid `#2F3336` / `#EFF3F4` | Between posts, sections, list rows |
| Floated FAB (Level 1) | `rgba(0,0,0,0.4) 0 4px 12px` | The compose Post circle button |
| Sheet (Level 2) | `rgba(0,0,0,0.6) 0 -8px 24px` | Bottom sheets (menus, audience selector, share) |
| Modal Backdrop | `rgba(0,0,0,0.7)` | Behind full-screen compose, media lightbox |
| Top Nav Blur | `.regularMaterial` over canvas at 70-85% opacity | Floating top nav on scroll |

**Shadow Philosophy**: X is almost entirely flat. The only prominent shadow is the floating Post CTA, which lifts off the canvas to signal "primary action anywhere". Posts themselves never cast shadows — they are content, not surfaces.

### Motion
- **Like tap**: heart scales 0.85 → 1.2 → 1.0 spring over 350ms, 6 pink particles burst in hexagonal pattern for 400ms, count slides up 12pt with fade, `.impact(.light)` haptic
- **Repost tap**: icon spins 0 → 360deg over 400ms as it fills green, count ticks up, `.impact(.light)` haptic
- **Reply tap**: icon scales 0.9 → 1.05 → 1.0 spring, navigates to compose screen sliding up from bottom
- **Feed swipe For You ↔ Following**: horizontal page transition 280ms spring + underline indicator slides smoothly between the two label positions
- **Pull-to-refresh**: custom loader — a single line that expands into the X logo glyph as the user pulls, then spins as refresh fires
- **Tab switch**: instant cross-fade between tabs (no slide transition)
- **Compose open**: modal slides up from bottom 320ms spring with backdrop fade

## 7. Do's and Don'ts

### Do
- Use pure black `#000000` as the default dark canvas — X is committed to true-black OLED
- Reserve Twitter Blue `#1D9BF0` for links, replies, verified marks, and the active tab underline
- Use role-specific colors on action icons: blue (reply/bookmark), green (repost), pink (like)
- Keep post rows flat with 1pt hairlines — no shadows, no cards, no elevation between posts
- Use the Chirp font at weights 400 and 700 only (with 500 for select labels) — no lights, no extra-bolds
- Keep the tab bar icon-only (no labels) — it's the recognizable X pattern
- Use 32pt circular avatars leading every post — this is the anchoring element
- Animate action icons with per-action color + scale + count tick — pair with `.impact(.light)` haptic
- Treat the X glyph as monochrome — white on dark, black on light, never tinted
- Use the FAB Post button inverted from canvas (white on black, black on white) — the deliberate contrast is the brand

### Don't
- Don't use shadows to separate posts — hairlines are the brand-correct separator
- Don't colorize post body text — body text is always primary neutral
- Don't add more accent colors beyond blue / green / pink — they map 1:1 to reply / repost / like
- Don't use uppercase on button labels — "Post", "Follow", "Subscribe" are in sentence case
- Don't use thin font weights — Chirp starts at 400 and tops out at 700
- Don't color the X logo — it's monochrome, always
- Don't use cards — posts are full-bleed with hairlines, containers are rare
- Don't default to light mode — the app opens dark for new users
- Don't hide the FAB Post button — the floating compose circle is persistent on feed screens
- Don't animate slowly — X's motion is tight, 200-400ms, no hero transitions

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 30pt avatars; post body column narrows to ~280pt |
| iPhone 13/14/15 | 390pt | Standard 32pt avatars; post body column ~306pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in nav — X logo slides slightly |
| iPhone 15/16 Pro Max | 430pt | 32pt avatars; post body column ~346pt; media scales up |
| iPad | 768pt+ | Split view — 3-column (nav rail | feed | trending sidebar); trending sidebar becomes visible |

### Dynamic Type
- Post body, quoted post body, DM body: full scale
- Display name, @handle, timestamp: cap at xxLarge
- Action counts: fixed 13pt (layout-sensitive)
- Tab icons: fixed (icon-only bar)
- Compose placeholder: scales with prompt size

### Orientation
- Home / Search / Notifications / Messages / Profile: **portrait-locked**
- Media lightbox (photo / video viewer): **rotates freely**
- Video playback (fullscreen): **rotates freely**
- Spaces (audio): **portrait-locked**

### Touch Targets
- Action icons: 18.75pt glyph, 44pt effective hit
- FAB Post button: 56pt
- Avatar tap: 32pt glyph, 44pt hit
- `...` overflow: 20pt glyph, 44pt hit
- Tab icons: 26pt glyph, 49pt hit (full tab bar height)
- Follow pill: min 32pt tall, 80pt wide

### Safe Area Handling
- Top: top nav respects safe area + Dynamic Island; logo shifts around the island
- Bottom: FAB Post floats above tab bar (16pt gap), both respect home indicator
- Sides: 16pt insets on all content; bleed-to-edge only for fullscreen media

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark/default): `#000000`
- Canvas (dim): `#15202B`
- Canvas (light): `#FFFFFF`
- Surface 1 (dark): `#16181C`
- Divider (dark): `#2F3336`
- Divider (light): `#EFF3F4`
- Text primary (dark): `#E7E9EA`
- Text primary (light): `#0F1419`
- Text secondary: `#71767B`
- Twitter Blue: `#1D9BF0`
- Repost Green: `#00BA7C`
- Like Pink: `#F91880`
- Verified Gold: `#EAB308`
- Error Red: `#F4212E`

### Example Component Prompts
- "Build an X post row in SwiftUI: full-width on `#000000` canvas, 12pt vertical padding, 1pt `#2F3336` divider bottom. Leading 32pt circular avatar, 16pt from left. To its right: display name 'Elon Musk' in Chirp 15pt w700 `#E7E9EA`, then 16pt verified blue checkmark, then `@elonmusk · 2h` in 15pt w400 `#71767B`, trailing `ellipsis` icon 20pt. Below (indented 12pt after avatar): post body Chirp 15pt w400 `#E7E9EA` with 1.33 line height. Bottom action row: reply, repost, like, views, bookmark icons at 18.75pt `#71767B` with 13pt counts beside each."
- "Create the X like tap animation: tap on outlined heart turns it filled `#F91880`, heart scales 0.85 → 1.2 → 1.0 via spring (response 0.35), 6 small pink particles burst outward in hexagonal pattern fading over 400ms, count number slides up 12pt with fade from old value to new, paired with `.impact(.light)` haptic."
- "Design X's floating Post button: 56pt circle, positioned bottom-right 16pt from screen edge and 16pt above tab bar. Dark mode: `#FFFFFF` fill with black `pencil.and.outline` glyph 24pt; light mode: `#0F1419` fill with white glyph. Shadow `rgba(0,0,0,0.4) 0 4px 12px`. Pressed: scale 0.95 + soft haptic. Navigates to full-screen compose modal sliding up from bottom over 320ms spring."
- "Build the X top nav: 44pt tall + safe area, canvas background at 70% opacity with `.regularMaterial` blur on scroll. Leading 32pt circular user avatar (tap opens side drawer), center 22pt monochrome X logo glyph (white on dark, black on light), trailing 28pt sparkles 'Grok' icon. Below the nav, feed filter bar: 'For you' and 'Following' labels in Chirp 15pt w700, active one has a 40pt × 4pt `#1D9BF0` underline pill; swiping horizontally changes active tab."
- "Create the X compose modal: full-screen over canvas, slides up 320ms spring. Top nav: 'Cancel' text button leading (Chirp 15pt w400 `#1D9BF0`), 'Post' pill button trailing (Chirp 15pt w700, `#EFF3F4` fill on dark / `#0F1419` fill on light, 8pt × 20pt padding, 500pt radius, disabled until text entered). Below: 40pt avatar leading, then multiline text field with Chirp 18pt w400 placeholder 'What's happening?' in `#71767B`. Bottom toolbar: media/gif/poll/emoji/location/schedule icons at 22pt `#1D9BF0`, plus audience selector pill ('Everyone')."
- "Build an X quoted-post embed: inside a parent post body, 1pt `#2F3336` border, 16pt corner radius, 12pt padding. Mini author row at top: 24pt circular avatar, Chirp 14pt w700 display name, 16pt verified check, @handle · timestamp in 14pt `#71767B`, trailing `...`. Below: quoted body in Chirp 14pt w400 `#E7E9EA`. Media inside quote: full-width 16pt-rounded image."

### Iteration Guide
1. Canvas is pure black `#000000` by default — dim mode is `#15202B`, light mode is `#FFFFFF`; app opens dark for new users
2. Twitter Blue `#1D9BF0` is structural: links, replies, verified marks, active tab underline — never decorative
3. Three action accent colors ONLY: blue (reply/bookmark), green (repost), pink (like) — mapped 1:1 to each action
4. Posts separate with 1pt `#2F3336` hairlines — NO shadows, NO cards, NO elevation between posts
5. Typography is Chirp at weights 400/500/700 only — no light, no black; sentence case on buttons
6. Tab bar is icon-only (26pt glyphs, no labels) — the signature X pattern
7. Action row animations: scale bounce + per-action color fill + count tick-up + `.impact(.light)` haptic on every tap
8. FAB Post button inverts from canvas (white-on-black, black-on-white) and floats persistently on feed screens
9. Avatars are always 32pt circular — the anchoring element of every post row
10. X logo is monochrome — never colored, never gradient, never animated on its own
