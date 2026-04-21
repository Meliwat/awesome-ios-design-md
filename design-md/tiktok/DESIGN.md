# Design System Inspiration of TikTok (iOS)

## 1. Visual Theme & Atmosphere

TikTok's iOS app is a full-bleed black canvas that exists to get out of the way of a single vertical video. From the moment you open the app you are dropped into the For You feed: a 9:16 clip fills the entire screen edge-to-edge, status-bar to home-indicator, and every piece of UI — captions, action icons, music ticker, tab bar — floats as a translucent overlay on top of that content. The design equivalent is a movie theater with the lights off and the usher wearing a black suit: nothing competes with the screen.

The accent system is built around a single, very loud idea: chromatic aberration. The TikTok wordmark splits the letter "d" into a cyan (`#25F4EE`) copy and a red / rose (`#FE2C55`) copy, offset a few pixels from the white original, mimicking the look of a 3D-glasses glitch or a VHS tape tracking error. Those two colors — cyan and rose — are the entire brand. Rose is the verb (the heart, the Follow button, the video scrubber, any "this is the action" moment). Cyan is the accent (the centered Create button, subtle highlights, loading states). White is neutral. Everything else is black or a hair-of-grey elevation. There is no secondary palette. There is no light mode.

Typography is Proxima Nova — a humanist geometric sans that TikTok has used across the product since its re-brand from Musical.ly. It sits slightly warmer than a pure grotesque but more neutral than SF Pro, so captions and usernames read as content rather than chrome. The hierarchy is compact and overlay-friendly: sizes run 11-24pt, weights concentrated at 400 / 600 / 700, and almost everything that sits on a video uses a subtle `rgba(0,0,0,0.4)` drop shadow so the text stays legible over bright or busy frames. The only place display type appears is the Live / profile header; the feed itself never shows a headline larger than 17pt because the video is the headline.

**Key Characteristics:**
- True-black canvas (`#010101`) — effectively dark-only, no light mode
- Chromatic aberration (cyan `#25F4EE` + rose `#FE2C55` stroke offset on white) is the signature visual treatment — logo, create button, loading states
- Rose `#FE2C55` is reserved for the action verb (Like, Follow, primary CTA, scrubber fill)
- Cyan `#25F4EE` is the secondary accent (Create button stripe, highlights)
- Full-bleed 9:16 video feed with all UI overlaid on top — no inset, no margin
- Vertical right-side action rail: avatar, heart, comment, bookmark, share, spinning music disc
- Left-side info overlay: @username, bold-hashtag caption, horizontally scrolling music marquee
- Centered tab bar Create button — white pill with cyan + red chromatic stripes (mini logo)
- Proxima Nova throughout, 400 / 600 / 700 weights, with text-on-video shadow
- Haptics on like, follow, share, double-tap-like; 60fps swipe transitions between videos

## 2. Color Palette & Roles

### Primary
- **TikTok Rose** (`#FE2C55`): Primary CTA, Follow button (unfollowed state), Like heart active, video progress scrubber fill, notification badges, chromatic aberration red-channel.
- **TikTok Cyan** (`#25F4EE`): Create button accent, chromatic aberration cyan-channel, subtle highlights, loading spinners.
- **Pure White** (`#FFFFFF`): Primary text on video, neutral icons, Create button core fill.

### Canvas & Surfaces
- **Canvas Black** (`#010101`): Primary canvas — effectively true black, absorbs letterboxing on non-9:16 content.
- **Elevated Surface** (`#161823`): DMs inbox, profile settings list, sheet backgrounds — a hair warmer than black so elevation reads.
- **Comment Compose** (`#2F2F2F`): Input field on the comments sheet, search bar on Discover.
- **Follower Gray** (`rgba(255,255,255,0.15)`): "Following" / followed-state pill background, secondary CTAs on dark overlays.
- **Overlay Scrim** (`rgba(0,0,0,0.4)`): Drop-shadow under text that sits on video, used to protect legibility.
- **Sheet Dim** (`rgba(0,0,0,0.6)`): Background dim behind bottom sheets (comments, share).

### Text
- **Text Primary** (`#FFFFFF`): Captions, usernames, icon glyphs, all text overlaid on video.
- **Text Secondary** (`#E5E5E5`): Hashtag color in captions (slightly softer than white), like / comment counts, music title on the marquee.
- **Text Tertiary** (`rgba(255,255,255,0.6)`): Timestamps, "Suggested for you" labels, placeholder text in comment input.
- **Text Disabled** (`rgba(255,255,255,0.3)`): Inactive tab labels, disabled button states.

### Semantic
- **Live Red** (`#FE2C55`): "LIVE" badge on profile avatars, same token as rose.
- **Unread Badge** (`#FE2C55`): Red dot on inbox tab, same token.
- **Success Green** (`#25F4EE`): Rarely used; cyan covers "success" states semantically.
- **Error Red** (`#FE2C55`): Same token as rose.

### Dark-Only
TikTok has no official light mode. Every screen — feed, profile, inbox, search, settings — is dark. The only "light" surfaces are the composer sheet when selecting effects (a white-background picker) and the camera viewfinder (the camera's own image).

## 3. Typography Rules

### Font Family
- **Primary**: `Proxima Nova` (TikTok uses a lightly modified version of the Mark Simonson family across product UI)
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Inter', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **CJK / Non-Latin**: Platform-native — PingFang SC (Chinese), Hiragino Sans (Japanese), Apple SD Gothic Neo (Korean), system Arabic and Thai stacks
- TikTok does not publish a proprietary typeface; Proxima Nova is used across product UI per multiple industry references

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Profile Display Name | Proxima Nova | 24pt | 700 | 1.2 | -0.2pt | Large name on own-profile header |
| Section / Sheet Title | Proxima Nova | 17pt | 600 | 1.25 | -0.1pt | "Comments (1,234)", "Share to" |
| Caption | Proxima Nova | 15pt | 400 | 1.35 | 0pt | Video caption over the feed, 2-line clamp before "more" |
| Caption Hashtag | Proxima Nova | 15pt | 700 | 1.35 | 0pt | Inline hashtags bold in captions |
| Username (Feed) | Proxima Nova | 16pt | 700 | 1.2 | 0pt | @username on feed overlay, precedes caption |
| Username (List) | Proxima Nova | 15pt | 600 | 1.25 | 0pt | Search results, follower lists, inbox |
| Action Count | Proxima Nova | 13pt | 700 | 1.2 | 0pt | "1.2M" under heart, "34.5K" under comment |
| Music Marquee | Proxima Nova | 13pt | 500 | 1.2 | 0pt | "original sound - @creator" bottom ticker |
| Body | Proxima Nova | 14pt | 400 | 1.4 | 0pt | Comment text, DM text |
| Meta / Timestamp | Proxima Nova | 12pt | 400 | 1.3 | 0pt | "2h ago", "Viewed 5m" |
| Tab Label | Proxima Nova | 10pt | 600 | 1.0 | 0.1pt | Home / Shop / Inbox / Profile |
| Button (Primary) | Proxima Nova | 16pt | 700 | 1.0 | 0pt | Follow / Sign up / Continue |
| Button (Secondary) | Proxima Nova | 14pt | 600 | 1.0 | 0pt | Following / Share / Cancel |
| Hashtag Chip | Proxima Nova | 13pt | 600 | 1.0 | 0pt | "#fyp #foryou" chip buttons |

### Principles
- **Weights concentrated at 400 / 600 / 700**: Regular for body, semibold for UI, bold for titles and counts
- **Overlay shadow is non-negotiable**: Any text rendered over a video frame uses a `rgba(0,0,0,0.4)` drop shadow at 1pt Y offset and 4pt blur
- **Hashtags bold inline**: Inside a caption, `#` tokens are 700 while the surrounding caption text is 400 — this is the TikTok signature for captions
- **Numeric counts abbreviate**: 1.2M, 34.5K, 999 — never raw integers above 1,000
- **Dynamic Type respected on Latin; fixed size on overlays**: Feed captions and action counts do not scale with Dynamic Type (layout-sensitive); profile and sheet text do

## 4. Component Stylings

### Buttons

**Follow Button (the iconic rose pill)**
- Shape: Pill, 28pt height, 56-64pt wide depending on "Follow" vs "Following"
- Unfollowed: background `#FE2C55`, text `#FFFFFF`, 14pt weight 700 ("Follow")
- Followed: background `rgba(255,255,255,0.15)`, text `#FFFFFF` ("Following"), 14pt weight 600
- Corner radius: 4pt (intentionally softer than full-pill — this is a TikTok-specific detail)
- Padding: 6pt vertical, 16pt horizontal
- Pressed: scale 0.95 with success haptic on follow, soft haptic on unfollow

**Primary Pill (Sign up / Continue / Send)**
- Background: `#FE2C55`
- Text: `#FFFFFF`, 16pt weight 700
- Height: 48pt, full-width on sheets, corner radius 4pt
- Pressed: opacity 0.85, scale 0.98, with medium haptic

**Right-Rail Icon Button (Heart / Comment / Bookmark / Share)**
- Size: 32pt glyph, 48pt hit area (stacked vertically at 24pt intervals)
- Default: `#FFFFFF` glyph with `rgba(0,0,0,0.25)` drop shadow (so it stays legible over video)
- Active (heart liked): `#FE2C55` fill with 1.0 → 1.3 → 1.0 spring bounce, heavy haptic
- Count label below each icon: 13pt weight 700 `#FFFFFF` with the same drop shadow
- Tap hit area extends to include the count label

**Double-Tap-to-Like (Center Explosion)**
- Large `#FE2C55` heart appears at the center of the video where the second tap landed
- Scale animation: 0.0 → 1.4 → 1.0 over 400ms with spring, then fade to 0 over 200ms
- Size at peak: 120pt
- Heavy haptic on first tap of the pair; no haptic on second tap

**Create Button (Tab Bar Center)**
- Shape: Rounded rectangle, 44pt wide × 30pt tall, corner radius 8pt
- Construction: a white fill layer, a cyan (`#25F4EE`) layer offset -3pt X / 0pt Y, a rose (`#FE2C55`) layer offset +3pt X / 0pt Y — all clipped to the rounded-rect shape so you see cyan on the left edge, rose on the right edge, white in the middle, and the aberration effect at the seams
- Centered white "+" glyph (SF Symbol `plus`, 18pt weight bold, `#010101` color)
- Pressed: entire button scales 0.94; heavy haptic

### Cards & Containers

**Video Feed Cell (For You)**
- Full viewport: width × height of the screen (safe-area-to-safe-area, but video content itself ignores safe areas and fills edge-to-edge)
- No card chrome — the video IS the cell
- Overlays:
  - Right-rail action stack positioned 12pt from right edge, centered vertically between ~55% and ~90% of the height
  - Bottom-left info block positioned 16pt from left edge, bottom-aligned above the tab bar safe area
  - Progress scrubber sits 1pt tall at the very bottom of the video area (hidden until the user long-presses or scrubs)

**Right-Side Action Rail**
- Stack layout, 24pt gap between items
- Order top-to-bottom:
  1. Creator avatar (48pt circle, 2pt white border, a red `+` badge at bottom-right when not followed — the badge disappears on follow with a spring shrink)
  2. Heart icon (32pt) + count
  3. Comment bubble (32pt) + count
  4. Bookmark icon (32pt) + count
  5. Share arrow (32pt) + count
  6. Spinning music disc (44pt circle, rotating 360° / 6s, shows the cover art of the audio track)

**Bottom-Left Info Overlay**
- Width: ~65% of screen, right edge stops before the action rail
- Top: `@username` (16pt weight 700 white)
- Middle: caption (15pt weight 400 white, hashtags bold) with 2-line clamp and "more" trailing
- Bottom: music marquee — music note icon + "original sound - @creator" in 13pt weight 500, horizontally scrolls right-to-left if truncated

**Comments Bottom Sheet**
- Height: ~75% viewport, corner radius 12pt top-only
- Background: `#161823` (elevated surface)
- Header: 48pt tall, centered 36pt × 4pt grabber, title "Comments (1,234)" at 17pt weight 600 white, close X top-right
- List: comment row with 36pt avatar left, username 14pt weight 700 + comment 14pt weight 400 stacked, like icon 18pt right with count below
- Compose bar (bottom, pinned above keyboard): 44pt tall field, background `#2F2F2F`, corner radius 4pt, placeholder "Add comment..." at 14pt tertiary, emoji shortcuts row above

**Profile Header**
- Centered avatar (96pt circle, 3pt white border, LIVE red ring if currently live-streaming)
- Display name (24pt weight 700) + @handle (15pt weight 400 secondary) stacked below
- Stat row: Following count, Followers count, Likes count — each as a vertical stack (big number 17pt weight 700 white, label 13pt weight 400 secondary) separated by 16pt gap
- Follow button (if not own profile) + Message secondary pill + share icon
- Bio text (14pt weight 400 white, max 3 lines)
- Tab row: grid icon (posts) / lock icon (private) / heart icon (liked) — 32pt, active indicator is a 2pt white bar below

### Navigation

**Bottom Tab Bar**
- Height: 48pt + safe area bottom
- Background: `rgba(1,1,1,0.92)` with `.regularMaterial` blur, 0.5pt top divider at `rgba(255,255,255,0.08)`
- 5 tabs: Home / Discover (magnifying glass) / Create (centered, chromatic) / Inbox / Profile
- Labels: Proxima Nova 10pt weight 600, below icon
- Active: `#FFFFFF`. Inactive: `rgba(255,255,255,0.5)`. Notification badge: 8pt `#FE2C55` dot top-right of icon
- On the feed screen specifically, the tab bar is *transparent* with only the glyphs and labels visible — no material, just text-shadowed icons over the video

**Top Feed Tabs**
- Positioned at top center, below Dynamic Island / notch safe area
- "Following" / "For You" horizontal tabs — only these two labels visible
- Active: white 17pt weight 700. Inactive: `rgba(255,255,255,0.6)` 17pt weight 400
- Hairline animated underline (2pt wide `#FFFFFF`) slides between the labels on tab change
- No container — just text floating on top of the video

**Navigation Bar (Inbox / Profile / Sheets)**
- Height: 44pt + safe area
- Background: matches the screen — `#010101` for inbox, transparent for feed sheets
- Back chevron 24pt white left, title 17pt weight 600 center, primary action or overflow 24pt right

### Input Fields

**Search Bar (Discover)**
- Background: `#2F2F2F`
- Height: 36pt
- Corner radius: 4pt
- Leading SF Symbol `magnifyingglass`, 16pt, `rgba(255,255,255,0.6)`
- Placeholder: "Search" in 14pt weight 400 `rgba(255,255,255,0.6)`
- Focus: background stays, cursor `#FE2C55`

**Comment Input**
- Background: `#2F2F2F`
- Height: 44pt on compose, expands with content
- Corner radius: 4pt
- Placeholder: "Add comment..." in 14pt weight 400 tertiary
- Trailing: send arrow icon, grayed until input is non-empty, turns `#FE2C55` when ready

**Hashtag Chip**
- Background: transparent with 1pt `rgba(255,255,255,0.2)` border
- Text: 13pt weight 600 white
- Padding: 6pt vertical, 12pt horizontal
- Corner radius: 4pt
- Selected (on the compose screen): background `#FE2C55`, border none, text white

### Distinctive Components

**Chromatic Aberration Treatment**
- Reserved for: the TikTok wordmark, the Create tab button, loading spinners
- Construction: a white base layer, a `#25F4EE` copy offset -3pt X, and a `#FE2C55` copy offset +3pt X
- Scale: offset grows proportionally with size — 1pt at 16pt type, 3pt at 44pt icon, 6pt at 96pt logo
- Do NOT use on regular body text, buttons, or icons — it loses meaning

**Video Progress Scrubber**
- Height: 2pt when idle, expands to 4pt on long-press scrub
- Track: `rgba(255,255,255,0.3)`
- Fill: `#FE2C55`
- Position: 1pt above the tab bar on the feed, or at the bottom of an embedded video on profile grid preview
- Thumb: appears only during scrub — 12pt circle white

**Spinning Music Disc (Right Rail)**
- 44pt circle with the audio track's cover art (or a default `#161823` disc with a center dot) centered
- Continuously rotates 360° / 6s linear when video is playing, freezes when video pauses
- On tap: opens the audio page listing other videos using the same sound

**Live Indicator Ring**
- 3pt stroke rose ring around a user's avatar when they're live-streaming
- "LIVE" text badge (10pt weight 700 white, rose background, 3pt corner radius) floats at the bottom-center of the avatar

**Loading Spinner (Chromatic)**
- Three stacked arcs: white base, cyan offset -3pt, rose offset +3pt, all rotating at the same speed (1s / rotation, linear, infinite)
- Used on: feed prefetch, video upload progress, search result loading

**Double-Tap Like Burst**
- See button section: center-screen 120pt heart explosion with spring scale + fade

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 48, 64, 96
- Feed overlay margins: 12pt from screen edges on the right rail, 16pt on the left info block
- Sheet content: 16pt horizontal inset, 12pt row spacing

### Grid & Container
- Feed: edge-to-edge, no grid — video fills the viewport
- Profile grid: 3-column square grid, 1pt hairline gap (almost touching)
- Discover: 2-column masonry (4pt gap) of 9:16 video thumbnails with gradient-overlay text
- Comments / inbox lists: 56pt row height, 16pt leading inset

### Whitespace Philosophy
- **Zero inset on video**: The video is the design; UI chrome respects only the safe area for interactivity, not the content
- **Tight overlay density**: The right rail packs 6 controls in ~280pt of vertical space because the video is behind them, so touch targets can be small without feeling cramped
- **Breathing on sheets**: Comments and inbox use 16pt horizontal margins and 12pt row gaps — this is where the app slows down

### Border Radius Scale
- Square (0pt): Video thumbnails on grids
- Soft (4pt): Buttons (Follow, primary pill), input fields, hashtag chips — the TikTok-signature "almost-square" corner
- Standard (8pt): Create tab button, preview cards
- Comfortable (12pt): Bottom sheets (top corners only)
- Full Pill (500pt): Rarely used; TikTok favors the 4pt corner over the full pill
- Circle (50%): Avatars, spinning music disc

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Video feed, backgrounds |
| Text Shadow | `rgba(0,0,0,0.4) 0 1px 4px` | All text overlaid on video |
| Icon Shadow | `rgba(0,0,0,0.25) 0 1px 3px` | Right-rail icons, top tabs |
| Sheet (Level 1) | `rgba(0,0,0,0.5) 0 -12px 40px` | Comments, share, settings bottom sheets |
| Modal Dim | `rgba(0,0,0,0.6)` + backdrop-blur 20 | Context menus, long-press menus |
| Tab Bar Material | `.regularMaterial` over `rgba(1,1,1,0.92)` | On non-feed screens; transparent on feed |

**Shadow Philosophy**: Shadows are used for *legibility*, not *hierarchy*. Every text overlay and icon on top of a video gets a subtle dark drop shadow so it reads against any frame — bright beach video, dark cave footage, a white studio shot. There are no decorative shadows on cards or buttons; the app's depth is flat. The only "elevation" that matters is the bottom sheet lift, and even that is communicated mainly through the rounded top corners rather than a strong shadow.

### Motion
- **Swipe up for next video**: 60fps vertical paging, 300ms spring, damping 0.85
- **Swipe right for profile, swipe left for For You**: 300ms horizontal paging with parallax on the destination screen
- **Double-tap like**: heart scale 0 → 1.4 → 1.0 over 400ms, then fade to 0 over 200ms, heavy haptic
- **Heart icon tap**: 1.0 → 1.3 → 1.0 spring, heavy haptic, rose fill swap
- **Follow button**: pill morphs from rose "Follow" to translucent "Following" over 200ms, scale 0.95 → 1.0 on success haptic
- **Spinning music disc**: continuous 360° / 6s rotation, linear, pauses with video
- **Comment sheet**: slides up 0.35s spring, damping 0.85; dismiss is a velocity-based drag or tap-outside
- **Create button tap**: whole tab bar dims 0.8s, camera UI fades in over 400ms

## 7. Do's and Don'ts

### Do
- Use `#010101` as the canvas — true black, because video frames need the deepest possible bezel
- Reserve rose `#FE2C55` for Like, Follow, scrubber fill, and any "this is the primary verb" moment
- Use cyan `#25F4EE` sparingly — Create button and chromatic accents only
- Apply the `rgba(0,0,0,0.4)` text shadow on every label sitting on top of a video
- Use chromatic aberration (cyan -3pt X, rose +3pt X offset around a white core) on the wordmark, Create button, and loading spinner
- Bold hashtags inline within captions (weight 700 amid weight 400 caption text)
- Fill the feed edge-to-edge; UI overlays float, they do not contain
- Use 4pt corner radius on buttons and inputs — TikTok's "almost-square" softness
- Abbreviate counts (1.2M, 34.5K) — never raw 6-digit integers
- Double-tap heart = center-screen 120pt burst with heavy haptic
- Spinning music disc rotates continuously while video is playing

### Don't
- Don't use `#000000` — the brand token is `#010101`, a hair warmer
- Don't add secondary accent colors — cyan and rose are the only chromatic moments
- Don't use a light mode — TikTok is dark-only; there is no cream, no off-white canvas
- Don't use a full-pill corner on buttons — 4pt is the signature
- Don't let captions sit on video without a drop shadow — they become unreadable on bright frames
- Don't use chromatic aberration decoratively — it's a logo / brand moment, not a flourish
- Don't inset the video feed — it fills edge-to-edge, UI floats on top
- Don't use white text on a gray surface — text on dark is always pure white
- Don't animate slowly — all feed interactions are 200-400ms springs; longer feels broken
- Don't hide the right-side action rail on the feed — it's load-bearing UX
- Don't use rose for inactive states or borders — it must stay a verb

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Action rail at 12pt spacing instead of 24pt; video letterboxed slightly |
| iPhone 13/14/15 | 390pt | Standard layout, 9:16 video fills perfectly |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated, top tabs shifted 12pt down |
| iPhone 15/16 Pro Max | 430pt | Larger avatar (52pt) on action rail, 15pt caption bumped to 16pt |
| iPad | 768pt+ | Two-column feed (video left, comments inline right) in landscape; single-column portrait with max-width 430pt centered |

### Dynamic Type
- Sheet content (comments, inbox, settings): full scale
- Feed captions, usernames, action counts: fixed size (layout-sensitive)
- Tab bar labels: fixed 10pt
- Profile display name: scales up to 28pt max

### Orientation
- Feed: **portrait-locked** — 9:16 video is the whole thesis
- Live streams: portrait-locked for the viewer
- Video player modal (from profile grid): rotates to landscape; controls shift to one side
- Discover / Inbox / Profile: portrait-locked

### Touch Targets
- Right-rail icons: 32pt glyph, 48pt hit area
- Follow button: 28pt tall, 56-64pt wide, full button tappable
- Tab bar: 48pt tall, icons 24pt with 44pt hit
- Create button: 44 × 30pt, 56pt hit area
- Double-tap zone: the entire video frame (anywhere)

### Safe Area Handling
- Video content: ignores safe area, fills edge-to-edge (video is allowed under the Dynamic Island)
- UI overlays: respect safe area — right rail, info block, tab bar, top tabs all sit inside
- Status bar: always `.lightContent` on feed (white over video); matches surface color on sheets
- Home indicator: respected by tab bar; feed video passes under it

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#010101`
- Elevated surface: `#161823`
- Comment input: `#2F2F2F`
- Follower gray (Following button bg): `rgba(255,255,255,0.15)`
- Rose (action verb): `#FE2C55`
- Cyan (Create accent): `#25F4EE`
- Text primary: `#FFFFFF`
- Text secondary (hashtag count): `#E5E5E5`
- Text tertiary: `rgba(255,255,255,0.6)`
- Text shadow on video: `rgba(0,0,0,0.4) 0 1px 4px`

### Example Component Prompts
- "Create a SwiftUI TikTok Follow button: 28pt tall, 4pt corner radius, background `#FE2C55`, text 'Follow' in Proxima Nova 14pt weight 700 white. 6pt vertical / 16pt horizontal padding. Pressed: scale 0.95 with success haptic. Followed state: background `rgba(255,255,255,0.15)`, text 'Following' 14pt weight 600 white."
- "Build the right-side action rail: vertical stack of 6 items, 24pt gap. Top: 48pt circular avatar with 2pt white border and a small rose `+` badge at bottom-right. Then heart icon (32pt white SF Symbol `heart`, active state `heart.fill` in `#FE2C55`) + count label (13pt weight 700 white). Repeat pattern for comment, bookmark, share. Bottom: 44pt spinning music disc rotating 360° / 6s. All icons carry `rgba(0,0,0,0.25)` drop shadow for legibility on video."
- "Design the TikTok Create tab button: 44pt wide × 30pt tall, 8pt corner radius. Stack three clipped rectangles — white core, cyan `#25F4EE` layer offset -3pt X, rose `#FE2C55` layer offset +3pt X — so the final button shows cyan on the left edge, rose on the right, and white through the middle. Centered white `+` SF Symbol (18pt weight bold) with `#010101` glyph color. Pressed: entire button scales 0.94 with heavy haptic."
- "Create the feed caption overlay: bottom-left of viewport, 16pt from left, right edge stops before the action rail. Top: `@username` Proxima Nova 16pt weight 700 white. Middle: caption 15pt weight 400 white with hashtag tokens bold weight 700, 2-line clamp with 'more' trailing. Bottom: music-note icon + horizontally scrolling 'original sound - @creator' text 13pt weight 500 white. Every text layer has `rgba(0,0,0,0.4)` drop shadow 1pt Y / 4pt blur."
- "Build the double-tap-to-like burst: on second tap of a pair, spawn a large `#FE2C55` heart SF Symbol `heart.fill` at the tap point. Scale from 0 → 1.4 → 1.0 over 400ms spring (damping 0.7), then fade opacity 1 → 0 over 200ms. Size at peak is 120pt. Heavy haptic fires on first tap of the pair. Does not interfere with the normal single-tap play/pause on the video."

### Iteration Guide
1. Canvas is `#010101` — effectively true black; do not lighten it
2. Rose `#FE2C55` is a verb — Like, Follow, scrubber fill; never used for borders, inactive, or decorative accents
3. Cyan `#25F4EE` appears only on the Create button and inside the chromatic aberration treatment
4. Chromatic aberration = cyan offset -3pt X + rose offset +3pt X over a white core; reserved for logo, Create button, loading states
5. Every text layer on video gets a `rgba(0,0,0,0.4)` drop shadow — this is non-negotiable for legibility
6. Hashtags are bold (weight 700) inline within regular (weight 400) caption text
7. Buttons use 4pt corner radius — TikTok's signature "almost-square" softness, not a full pill
8. Counts abbreviate: 1.2M / 34.5K / 999 — never raw integers above 1,000
9. Feed is edge-to-edge; UI overlays float on top and respect safe area for touch, not content
10. Haptics: heavy on double-tap-like, success on follow, medium on share, soft on single heart tap
