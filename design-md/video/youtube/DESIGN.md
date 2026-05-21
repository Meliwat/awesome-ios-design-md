# Design System Inspiration of YouTube (iOS)

## 1. Visual Theme & Atmosphere

YouTube's iOS app is a content-forward canvas whose entire job is to surface a 16:9 thumbnail and let you tap it. The feed is a vertical stream of video cards — each one a wide still frame with a red-circled duration tag, a channel avatar, a two-line title, and metadata — arranged edge-to-edge with generous gaps between them. On light mode (the default in recent versions), the app canvas is clinical white (`#FFFFFF`); on dark mode, a near-black `#0F0F0F` that's been the flagship surface since 2017. Whichever mode you're in, the red is the same: YouTube Red (`#FF0000`), an unshifted, saturated, traffic-light red reserved for the Subscribe button, the live indicator, and the play-button logomark. Everything else — backgrounds, text, chrome — is achromatic.

The accent system has one job: mark the verb "Subscribe." When you tap Subscribe, the red pill morphs to a gray "Subscribed" state, the bell icon appears, and a light haptic fires — and that's the only time red ever appears on an action in the UI. Red is otherwise reserved for three static marks: the "LIVE" pill on live-stream thumbnails, the small "•" unread indicator on the subscriptions tab, and the play arrow inside YouTube's logomark. This restraint is what makes YouTube Red instantly iconic — when you see red in this app, you're looking at a brand element, not interactive UI.

Typography is YouTube Sans (proprietary, released in 2017) for display moments — the wordmark, large headers, the video player UI — paired with Roboto for body text (because the app was built by Google and its Material roots still show). On iOS, a fallback stack of SF Pro covers both when the custom fonts are unavailable. The hierarchy is compact: sizes run 11–20pt, weights at 400 / 500 / 700. Video titles sit at 16pt weight 500 with 2-line clamp — the single most common type moment in the app. There are no display-sized hero titles in the feed itself; the thumbnail is the headline, the title is the caption.

**Key Characteristics:**
- White canvas (`#FFFFFF`) in light mode, near-black `#0F0F0F` in dark — both with the same YouTube Red accent
- **YouTube Red** (`#FF0000`) reserved for the Subscribe button and brand marks (logo, LIVE badge, unread dots) — nothing else
- **16:9 video thumbnail** is the atomic unit of the app, with a black duration tag in the bottom-right corner
- 28pt circular channel avatar below the thumbnail, left-aligned, paired with channel name + metadata
- **Shorts tab** ships a TikTok-style vertical video player with right-rail actions
- **Mini-player** — a draggable floating video that persists when navigating away
- Bottom tab bar (5 tabs): Home, Shorts, Create (+), Subscriptions, You
- YouTube Sans (proprietary display) paired with Roboto body; SF Pro fallback
- Haptics on Subscribe, like/dislike, share, and video scrub

## 2. Color Palette & Roles

### Primary
- **YouTube Red** (`#FF0000`): The iconic brand red — Subscribe button (unsubscribed state), LIVE indicator pill, brand logomark (play arrow in the square), the red dot in notification badges, and the progress-fill color on the video scrubber.
- **YouTube Red Pressed** (`#CC0000`): Pressed / active-state variant for red CTAs.
- **Red Hover** (`#E60000`): Slight deepening for web-style hover; matched on iOS as a pressed alt.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas — all main feed surfaces.
- **Surface 1** (`#F9F9F9`): Elevated card backgrounds on some sheets, section backing.
- **Surface 2** (`#F2F2F2`): Pressed-state background on list rows and chips.
- **Divider** (`#E5E5E5`): 1pt hairline dividers between list items and sections.
- **Input Background** (`#F2F2F2`): Search bar fill in light mode.

### Canvas & Surfaces (Dark Mode)
- **Canvas Dark** (`#0F0F0F`): Primary dark canvas — YouTube's signature "not-quite-black."
- **Surface 1 Dark** (`#1F1F1F`): Sheet backgrounds, context menu, comment compose.
- **Surface 2 Dark** (`#272727`): Pressed-state on dark rows, hover overlays.
- **Surface 3 Dark** (`#3F3F3F`): Higher elevation — player chrome, sticky headers.
- **Divider Dark** (`#303030`): Hairline dividers in dark mode.

### Text (Light Mode)
- **Text Primary** (`#0F0F0F`): Video titles, channel names, primary headings.
- **Text Secondary** (`#606060`): Metadata — view counts, upload date, channel subscriber count.
- **Text Tertiary** (`#909090`): Timestamps, placeholders, lowest-emphasis labels.
- **Text Disabled** (`#C0C0C0`): Disabled button text.

### Text (Dark Mode)
- **Text Primary Dark** (`#FFFFFF`): Video titles, channel names, headings on dark.
- **Text Secondary Dark** (`#AAAAAA`): Metadata on dark.
- **Text Tertiary Dark** (`#717171`): Lowest-emphasis labels on dark.

### Semantic
- **Live Red** (`#FF0000`): LIVE indicator pill — same token as brand red.
- **Error Red** (`#FF0000`): Playback / network errors, same token.
- **Success Green** (`#3EA6FF` is info, not success — YouTube uses blue): Minimal success states.
- **Info Blue** (`#3EA6FF`): Links inline within descriptions, "Show more," clickable hashtags in comments.
- **Warning Yellow** (`#FFC400`): Rarely used — age-restriction warnings, content advisories.

### Content Accent: Channel & Playlist
- Channel pages accept a custom banner image and an accent color — but this accent never bleeds into the app's chrome. It only appears as a subtle header overlay.

## 3. Typography Rules

### Font Family
- **Primary Display**: `YouTube Sans` (proprietary, released 2017 — rounded geometric sans, close to Nunito / Rubik in feel)
- **Primary Body**: `Roboto` (Google's signature sans — used for video titles, metadata, body copy)
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Roboto', 'Segoe UI', Helvetica, Arial, sans-serif`
- **CJK / Non-Latin**: Platform-native — PingFang SC, Hiragino Sans, Apple SD Gothic Neo, Noto Sans extended
- YouTube Sans is used for the wordmark, the large "Shorts" / "Live" labels, and display moments; Roboto handles everything else

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | YouTube Sans | 20pt | 700 | 1.2 | -0.1pt | "Home", "Subscriptions" top nav |
| Video Detail Title | Roboto | 18pt | 600 | 1.3 | -0.1pt | Below the full-player on the video detail page |
| Video Title (Feed) | Roboto | 16pt | 500 | 1.3 | 0pt | Video card title — 2-line clamp — the most common type moment |
| Channel Name | Roboto | 14pt | 500 | 1.3 | 0pt | Below video title, next to channel avatar |
| Metadata | Roboto | 13pt | 400 | 1.3 | 0pt | "1.2M views · 3 days ago" |
| Comment Text | Roboto | 14pt | 400 | 1.4 | 0pt | Comment body on video detail |
| Comment Author | Roboto | 13pt | 500 | 1.3 | 0pt | Username + "• 2 hours ago" |
| Body | Roboto | 14pt | 400 | 1.45 | 0pt | Description, About sections |
| Button (Primary — Subscribe) | Roboto | 14pt | 500 | 1.0 | 0pt | Red Subscribe / gray Subscribed pill |
| Button (Secondary) | Roboto | 14pt | 500 | 1.0 | 0pt | Share, Download, Join, etc. |
| Chip | Roboto | 14pt | 400 | 1.0 | 0pt | Filter chips on Home ("All", "Music", "Gaming") |
| Tab Label | Roboto | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Duration Tag | Roboto | 11pt | 500 | 1.0 | 0pt | "12:34" in bottom-right of thumbnail |
| Timestamp | Roboto | 12pt | 400 | 1.0 | 0pt | "•1:23 / 4:56" on player scrubber |
| Like Count | Roboto | 13pt | 500 | 1.0 | 0pt | "1.2K" under the like icon |
| Shorts Overlay Caption | YouTube Sans | 15pt | 500 | 1.35 | 0pt | Caption on Shorts, shadow-backed |

### Principles
- **Weight concentration at 400 / 500 / 700**: Regular for body, medium for titles and buttons, bold for display
- **No thin, light, or black weights**: The fonts ship with them but the product does not use them
- **Two font families**: YouTube Sans for display, Roboto for body — keep them in their lanes
- **Video titles at 16pt medium**: This is the most common type moment; its weight 500 gives visual weight without being bold
- **View-count metadata compresses**: "1.2M views", "234K views", "1,234 views" — abbreviate above 10K
- **Dynamic Type supported**: Titles, descriptions, and comments scale with user preference; tab labels and duration tags stay fixed
- **Shorts captions need shadow**: Any text over Shorts video gets a subtle `rgba(0,0,0,0.5)` drop shadow for legibility

## 4. Component Stylings

### Buttons

**Subscribe Button (The Iconic Red → Gray Morph)**
- Unsubscribed state:
  - Shape: Rounded rectangle with 18pt corner radius (NOT full pill — YouTube's signature is the rounded rect)
  - Background: `#FF0000`
  - Text: `#FFFFFF`, Roboto 14pt weight 500 ("Subscribe")
  - Padding: 8pt vertical, 16pt horizontal
  - Height: 36pt
- Subscribed state:
  - Background: `#F2F2F2` light / `#272727` dark
  - Text: `#0F0F0F` light / `#FFFFFF` dark ("Subscribed")
  - Often paired with a trailing bell icon (notification toggle) — 16pt SF Symbol bell
- Pressed: opacity 0.85, scale 0.98, light haptic on state change
- Transition: 200ms spring morph between red and gray

**Primary Pill (Sign in / Continue)**
- Used on sign-in flows, creator tools
- Background: `#0F0F0F` (dark text button in light mode) or `#3EA6FF` (blue info button)
- Padding: 10pt vertical, 24pt horizontal
- Corner radius: 18pt (matches Subscribe)
- Text: `#FFFFFF` 14pt weight 500

**Action Button Row (Like / Dislike / Share / Download / Save / Remix)**
- A horizontal pill-group below the video title on the video detail page
- Each button: 36pt tall capsule with light gray background (`#F2F2F2` light / `#272727` dark)
- Leading 20pt SF Symbol glyph + trailing count label (13pt weight 500)
- Corner radius: 18pt, padding 8pt vertical / 12pt horizontal
- Gap between pills: 8pt
- Like: thumbs-up SF Symbol with count ("1.2K"); tap toggles fill and turns `#0F0F0F` solid; no color change (YouTube removed the blue-highlight treatment)
- Dislike: thumbs-down SF Symbol; count is hidden (per 2021 product decision); only the user sees their own dislikes
- Pressed: scale 0.97, light haptic

**Icon Button (Player Controls — Play / Pause / Skip)**
- Glyph size: 32pt (main play/pause), 20pt (skip forward/back, captions, fullscreen)
- Color: `#FFFFFF` over video — always white since the player is on a dark canvas
- Hit area: 44pt
- Pressed: scale 0.9, soft haptic

**Text Button ("Show more", "Sort by")**
- Font: Roboto 14pt weight 500
- Color: `#0F0F0F` light / `#FFFFFF` dark
- No underline, no background

### Cards & Containers

**Video Card (Feed) — The Atomic Unit**
- Width: full screen width
- Structure (top to bottom):
  1. **16:9 thumbnail** — full-width, image fills with no letterbox, no corner rounding (0pt, hard edges) on the feed card itself (some experiments use 12pt; the signature is hard edge)
  2. **Duration tag** — positioned absolute bottom-right of thumbnail, 6pt from corners; background `rgba(0,0,0,0.75)`, text `#FFFFFF` 11pt weight 500, 4pt corner radius, 4pt vertical / 6pt horizontal padding
  3. **LIVE tag** (if live) — top-left of thumbnail; background `#FF0000`, text "LIVE" white 11pt weight 700, same 4pt radius
  4. Metadata row below thumbnail: 12pt top padding, 16pt horizontal padding
     - Left: 28pt circular channel avatar
     - Right stack:
       - Video title: Roboto 16pt weight 500, 2-line clamp with ellipsis, `#0F0F0F` light / `#FFFFFF` dark
       - Channel name + metadata on one line: Roboto 13pt weight 400 `#606060` light / `#AAAAAA` dark — format "Channel Name · 1.2M views · 3 days ago"
     - Trailing: 24pt `ellipsis` overflow icon, 44pt hit
  5. Bottom padding: 16pt (gap before next card)
- Background: transparent (canvas shows through)
- No corner radius on the card itself — cards are flush to screen edges; the thumbnail's hard edges are the defining aesthetic

**Shorts Card (Vertical Video)**
- Full-viewport 9:16 video (width × height of screen)
- Overlays (over the video):
  - Right-rail action stack, 12pt from right, centered vertically 50–90%
    - Creator avatar (44pt circle, 2pt white border) with `+` Subscribe badge
    - Like (thumbs-up) 32pt white + count below (13pt weight 500 white with shadow)
    - Dislike (thumbs-down) 32pt white + count hidden
    - Comment bubble 32pt white + count
    - Share arrow 32pt white
    - Remix icon (music note + play) 32pt
    - Audio disc — 44pt rotating circle showing the audio source cover
  - Bottom-left info: 16pt from left, bottom-align above tab bar
    - `@channel` 15pt weight 500 white
    - Caption YouTube Sans 15pt weight 500 white, 2-line clamp, shadow `rgba(0,0,0,0.5)` 0 1px 3px
    - Audio marquee — music note + "Original audio" or song title, 13pt weight 400 white
  - Progress scrubber at bottom — 2pt tall, track `rgba(255,255,255,0.3)`, fill `#FFFFFF` (not red on Shorts — kept white for minimalism)

**Video Detail (Full Player + Info)**
- Top: 16:9 player, full width — black letterbox if video is not 16:9
- Below player:
  - Title: Roboto 18pt weight 600, can be 2–3 lines, 16pt horizontal / 12pt top padding
  - View count + upload date row: 13pt weight 400 secondary
  - Channel row: 40pt circle avatar + channel name (14pt weight 500) + subscriber count (13pt weight 400 secondary) + **Subscribe pill** trailing right
  - Action button row: horizontal scroll of pills (Like, Dislike, Share, Remix, Download, Save, Report)
  - Description card: 16pt padding, collapsible with "Show more" — contains description, hashtags, chapters, playlist links
  - Comments preview: tap opens comment sheet
  - Up-next recommendations — vertical list of related video cards

**Mini-Player**
- Sits at the bottom of the screen, above the tab bar, when the user swipes down from a full player
- Height: 72pt
- Full width, background `#FFFFFF` light / `#1F1F1F` dark
- Left: 72pt-tall 16:9 thumbnail (128pt wide approx)
- Center: video title 14pt weight 500 (1 line), channel name 12pt weight 400 (1 line)
- Right: Play/pause 20pt SF Symbol + close X 20pt
- Drag up: expands back to full player with shared-element animation
- Drag down past 40pt from bottom: dismisses

**Comment Row**
- 32pt circular avatar leading
- Author + "• 2 hours ago" header row (13pt weight 500 + secondary)
- Comment body (14pt weight 400) with inline link styling
- Action row: thumbs-up + count, thumbs-down, reply button, heart-from-creator badge if applicable (20pt)
- If the creator hearted the comment: a small red heart with their avatar appears below the comment
- Nested replies: indented 40pt, hidden behind a "View replies" button

**Filter Chip Row (Home)**
- Horizontal scroll of chips at the top of Home
- Each chip: 34pt tall pill
- Default: background `#F2F2F2` light / `#272727` dark, text `#0F0F0F` / `#FFFFFF`
- Active: background `#0F0F0F` light / `#FFFFFF` dark, text white / black (full color inversion for the selected chip — YouTube's signature)
- Padding: 8pt vertical, 12pt horizontal
- Corner radius: 500pt (full pill)

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` light / `#0F0F0F` dark with 1pt top hairline `#E5E5E5` / `#303030`
- 5 tabs: **Home** (house), **Shorts** (short play icon — unique custom glyph), **Create** (centered `+`), **Subscriptions** (subscription-box icon), **You** (user circle or avatar)
- Icon size: 24pt glyph, 44pt hit area
- Active: `#0F0F0F` light / `#FFFFFF` dark (filled variant)
- Inactive: `#606060` light / `#AAAAAA` dark (outlined variant)
- Labels: Roboto 10pt weight 500, shown always
- Create tab: center button is slightly different — on some variants it's a `+` inside a circle outline; when tapped, it opens a modal sheet ("Create a Short", "Upload video", "Go live")
- Unread badge: 8pt `#FF0000` dot at the top-right of the tab icon (shows on Subscriptions and You)

**Top App Bar (Home)**
- Height: 48pt + safe area
- Background: matches canvas
- Leading: YouTube logomark (red play-button in a rounded square + "YouTube" wordmark in YouTube Sans) 24pt tall
- Trailing icons (from right): Cast (TV + wave), Notifications (bell), Search (magnifying glass), Profile avatar (28pt circle) — all 24pt, 16pt gap
- On scroll down: bar compresses / hides with subtle fade
- Filter chip row sits below the app bar — sticky to top when scrolling

**Top Nav (Video Detail / Settings)**
- Height: 44pt + safe area
- Back chevron 24pt leading, title 17pt weight 500 center, trailing overflow 24pt
- Background matches canvas

### Input Fields

**Search Bar (tapped from Home)**
- After tapping the search icon on Home, a full-screen search view takes over
- The top becomes an input field: 44pt tall, 500pt corner radius on the pill side on newer versions (rounded rect 4pt on classic)
- Leading back chevron 24pt; input field takes the full remaining width
- Placeholder: "Search YouTube" 16pt weight 400 `#606060` light / `#AAAAAA` dark
- Trailing: microphone (voice search) + camera (visual search) 24pt icons
- Background: `#F2F2F2` light / `#272727` dark

**Comment Compose**
- Pinned above the keyboard when comments sheet is open
- Background: `#FFFFFF` light / `#0F0F0F` dark with 1pt top border
- Height: 44pt collapsed, expands with content
- Leading: user avatar 32pt circle
- Center: input "Add a comment..." 14pt weight 400 tertiary
- Trailing: send arrow (grayed until non-empty, then `#3EA6FF`)

### Distinctive Components

**The Duration Tag**
- Positioned absolute bottom-right on every thumbnail
- Background `rgba(0,0,0,0.75)` (an opaque dark chip that reads as "official" YouTube style)
- Text `#FFFFFF` 11pt weight 500, 4pt radius, 4pt vertical / 6pt horizontal padding
- Format: "M:SS" for under an hour, "H:MM:SS" for longer
- If a Short: the tag is replaced with a small Shorts glyph or is hidden (Shorts thumbnails are 9:16 and handled differently)

**The Live Indicator**
- Top-left on thumbnails: red pill "LIVE" + small white dot pulsing
- On the video detail page, a rotating live viewer count sits below the channel name
- Red `#FF0000` chip, white text, 11pt weight 700

**The LIVE Pulse**
- Subtle 2-second opacity pulse on the LIVE dot (opacity 1 → 0.6 → 1)
- Indicates active real-time stream

**Subscribe Button Morph**
- Red "Subscribe" pill → gray "Subscribed" pill in 200ms
- Bell icon fades in trailing after subscription
- Success haptic fires on the red-to-gray transition

**Mini-Player Drag-Down**
- Full player + info can be swiped down with a two-finger drag to collapse into the mini-player
- The mini-player persists while the user browses other content — this is YouTube's most distinctive navigation pattern
- Drag mini-player down past threshold: dismisses (video stops)

**Chapters**
- Longer videos show chapter markers on the scrubber — hairline vertical notches on the 1pt track
- Tap chapter: jumps to that timestamp; current chapter title sits above the scrubber

**Heart-from-Creator**
- When a video creator hearts a comment, a small red heart icon overlays the comment, showing the creator's avatar inside
- 20pt heart, creator's 16pt circular avatar offset bottom-right

**Playback Speed Menu**
- A modal sheet from the player's overflow menu
- Options: 0.25x, 0.5x, 0.75x, Normal (1x), 1.25x, 1.5x, 1.75x, 2x
- Selected option has a checkmark and is `#3EA6FF` or bold

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 48, 56
- Video card vertical rhythm: 0pt between thumbnail and metadata, 16pt bottom padding between cards
- Content margin: 0pt on thumbnails (full-width), 16pt on metadata text
- Comment row: 12pt vertical padding, 16pt leading

### Grid & Container
- Feed: single-column full-width on iPhone (cards stack vertically)
- iPad portrait: 2-column grid with 16pt gap
- iPad landscape: 3-column grid
- Shorts: always single-column, 9:16 full-viewport
- Filter chip row: horizontal scroll, no wrap

### Whitespace Philosophy
- **No outer padding on thumbnails**: Thumbnails bleed edge-to-edge for maximum visual weight
- **Generous between-card gaps**: 16pt vertical rhythm gives feed a breathable flow
- **Tight metadata density**: Channel row + title + metadata pack 3 pieces of info in ~56pt vertical space below the thumbnail
- **Shorts are immersive**: Zero chrome; the video IS the UI

### Border Radius Scale
- Square (0pt): Feed thumbnails — YouTube's signature hard edge
- Soft (4pt): Duration tag, LIVE pill, some legacy cards
- Standard (8pt): Some modal content, image previews
- Pronounced (12pt): Bottom sheet top corners
- Rounded Button (18pt): Subscribe, pill buttons in the action row, filter chips
- Full Pill (500pt): Search bar (modern), notification pills
- Circle (50%): Channel avatars, audio discs, close buttons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Feed cards, most UI |
| Card Lift | `rgba(0,0,0,0.08) 0 2px 8px` | Mini-player, elevated sheets |
| Player Chrome | `rgba(0,0,0,0.3) 0 0 40px` inset-ish gradient | Over video, darkens edges for control visibility |
| Sheet | `rgba(0,0,0,0.2) 0 -8px 24px` | Bottom sheets (share, comments, chapters) |
| Modal Dim | `rgba(0,0,0,0.6)` + no blur | Modal dimming behind sheets |
| Tab Bar Border | 1pt hairline top border `#E5E5E5` / `#303030` | Instead of shadow, YouTube uses a hairline |

**Shadow Philosophy**: YouTube is a mostly flat app. The only components that carry noticeable shadow are the mini-player (which needs to read as floating above content) and bottom sheets. Feed cards have no shadow — they're laid out flat on the canvas, separated only by whitespace. On Shorts, text overlays carry text shadow for legibility but UI buttons do not.

### Motion
- **Subscribe morph**: 200ms spring (damping 0.8) between red and gray states; success haptic on transition
- **Mini-player expand**: Shared-element transition on the 16:9 thumbnail, 0.35s spring, damping 0.8
- **Video card tap**: Light haptic; dim the card 0.95 opacity while loading
- **Like tap**: Thumbs-up fills, 1.0 → 1.15 → 1.0 bounce over 300ms, light haptic
- **Scrubber drag**: Haptic tick every 10% of progress; thumb scales 1.0 → 1.3 during drag
- **Shorts swipe**: 60fps vertical paging, 300ms spring (damping 0.85)
- **Double-tap seek**: Left side -10s, right side +10s, with a semi-transparent arc ripple overlay and "−10" / "+10" label
- **Comment heart-from-creator**: Appear with spring bounce + heart scale 0 → 1.2 → 1.0

## 7. Do's and Don'ts

### Do
- Use `#FFFFFF` as light canvas and `#0F0F0F` as dark — YouTube's signature "near-black," never pure black
- Reserve YouTube Red (`#FF0000`) for the Subscribe button, LIVE indicator, and logomark — nothing else
- Render video thumbnails at full screen width with NO corner radius — hard edges are the signature
- Place the duration tag in the bottom-right of every thumbnail, dark chip, white text
- Pair YouTube Sans (display) with Roboto (body) — keep the two in their lanes
- Use the gray "Subscribed" state after a user subscribes — never leave the red pill active
- Hide the dislike count (reflects 2021+ product decision)
- Use 18pt corner radius on Subscribe and action pills — YouTube's rounded-rect button shape
- Support full color inversion on selected filter chips (black bg / white text when active)
- Animate the mini-player with a shared-element transition from the full player
- Use heart-from-creator overlay for creator-highlighted comments — 20pt red heart with creator avatar
- Use haptics: light on Subscribe, light on like, success on share confirm

### Don't
- Don't use pure black `#000000` for dark canvas — `#0F0F0F` is the brand token (slightly warmer)
- Don't round thumbnail corners on the feed — hard edges are load-bearing aesthetic
- Don't use red on non-Subscribe actions — red is a brand element, not an action accent
- Don't display dislike counts publicly (post-2021 product decision)
- Don't use a full-pill corner on Subscribe — it's 18pt, not 500pt
- Don't use thin or light font weights — 400 / 500 / 700 only
- Don't forget the shadow on Shorts captions — text over video needs `rgba(0,0,0,0.5)` drop shadow
- Don't omit the duration tag — every thumbnail must show runtime
- Don't hide the mini-player when the user navigates away during playback — it's load-bearing UX
- Don't animate the Subscribe morph with a slide — it's an in-place color cross-fade

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Title clamp tighter, chip row shows 4 instead of 5 |
| iPhone 13/14/15 | 390pt | Standard card layout — 16:9 thumbnail ~219pt tall |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | Thumbnail ~242pt tall, more metadata fits inline |
| iPad | 768pt+ | 2–3 column grid, mini-player pins to bottom-right corner |

### Dynamic Type
- Video titles, descriptions, comments: full scale
- Duration tag: **fixed** at 11pt (never scales — layout-sensitive)
- Subscribe button text: fixed at 14pt
- Tab labels: fixed at 10pt
- Shorts caption: fixed size (tight layout)
- Metadata row: scales to ~20% larger maximum to avoid wrapping

### Orientation
- Feed / Subscriptions / You: **portrait-locked**
- Video Detail: **rotates to landscape** — video goes fullscreen, controls overlay
- Shorts: **portrait-locked**
- Comments sheet: portrait-locked (would be weird in landscape)

### Touch Targets
- Subscribe: 36pt pill (hit area expanded to 44pt vertically)
- Video card: full card tappable (thumbnail + metadata as one touch target)
- Overflow icon: 44pt hit
- Player controls: 44pt each
- Shorts action icons: 32pt glyph with 48pt hit area
- Tab bar: 44pt hit

### Safe Area Handling
- Top: status bar / Dynamic Island respected by top app bar
- Bottom: tab bar respects home indicator; mini-player sits above both
- Video player (fullscreen landscape): ignores safe area for video content, respects for controls
- Shorts: respects safe area only for overlays; video extends edge-to-edge

## 9. Agent Prompt Guide

### Quick Color Reference
- Light canvas: `#FFFFFF`
- Dark canvas: `#0F0F0F`
- Surface 1 (light): `#F9F9F9`
- Surface 1 (dark): `#1F1F1F`
- Surface 2 (light): `#F2F2F2`
- Surface 2 (dark): `#272727`
- Divider (light): `#E5E5E5`
- Divider (dark): `#303030`
- Text primary (light): `#0F0F0F`
- Text primary (dark): `#FFFFFF`
- Text secondary (light): `#606060`
- Text secondary (dark): `#AAAAAA`
- YouTube Red: `#FF0000`
- Red pressed: `#CC0000`
- Info blue: `#3EA6FF`

### Example Component Prompts
- "Create a SwiftUI YouTube Subscribe button: 36pt tall rounded rectangle with 18pt corner radius, background `#FF0000`, text 'Subscribe' in Roboto 14pt weight 500 white. 8pt vertical / 16pt horizontal padding. On subscribe: morph background to `#F2F2F2` (light) or `#272727` (dark), text becomes 'Subscribed' in text-primary color, 200ms spring, fire light haptic. Add a trailing bell SF Symbol 16pt when subscribed."
- "Build a YouTube video card: full-width 16:9 thumbnail with NO corner radius and hard edges. Bottom-right of thumbnail: 'M:SS' duration tag on `rgba(0,0,0,0.75)` background, 4pt radius, white text Roboto 11pt weight 500. Below thumbnail: 12pt top padding, 16pt horizontal. 28pt channel avatar leading, then stack of title (Roboto 16pt weight 500, 2-line clamp) and metadata line ('Channel · 1.2M views · 3 days ago' in 13pt weight 400 `#606060`). Trailing: 24pt ellipsis icon."
- "Design the YouTube Shorts player: full-viewport 9:16 vertical video, overlays on top. Right rail (12pt from right): stacked creator avatar with `+` subscribe badge, thumbs-up 32pt white + count, thumbs-down 32pt (count hidden), comment 32pt + count, share 32pt, remix 32pt, and a 44pt rotating audio disc. Bottom-left (16pt from left): `@channel` 15pt weight 500 white, caption YouTube Sans 15pt weight 500 white with `rgba(0,0,0,0.5)` drop shadow, music marquee 13pt weight 400. Bottom progress bar: 2pt tall, track `rgba(255,255,255,0.3)`, fill white."
- "Build the YouTube mini-player: 72pt tall horizontal bar above the tab bar. 128×72pt 16:9 thumbnail leading, center column with video title 14pt weight 500 (1 line) and channel name 12pt weight 400 (1 line), trailing play/pause 20pt SF Symbol and close X 20pt. Background `#FFFFFF` light / `#1F1F1F` dark. Drag up: animate shared-element expansion into full player. Drag down 40pt+: dismiss."
- "Create the YouTube action pill row: horizontal scroll below video title on detail page. Each pill: 36pt tall capsule, background `#F2F2F2` light / `#272727` dark, 18pt radius, leading 20pt icon + trailing count label 13pt weight 500. Pills in order: Like (thumbs-up + '1.2K'), Dislike (thumbs-down, no count), Share, Remix, Download, Save. 8pt gap between pills."
- "Build the YouTube filter chip row on Home: horizontal scroll of 34pt-tall pills. Default chip: background `#F2F2F2` light / `#272727` dark, text `#0F0F0F` / `#FFFFFF` 14pt weight 400, 8pt vertical / 12pt horizontal padding, 500pt corner radius (full pill). Active chip: full color inversion — background becomes `#0F0F0F` / `#FFFFFF`, text becomes white / black. Tap: crossfade 200ms with selection haptic."

### Iteration Guide
1. Canvas is `#FFFFFF` in light and `#0F0F0F` in dark — never pure black
2. YouTube Red `#FF0000` is for the Subscribe button and brand marks (LIVE, logo, unread dots) — not a generic accent
3. Thumbnails bleed edge-to-edge with NO corner radius — hard edges are the signature
4. Duration tag is non-negotiable — every thumbnail shows its runtime in the bottom-right
5. Subscribe button uses 18pt corner radius (rounded rectangle, not full pill)
6. Typography: YouTube Sans (display) + Roboto (body), weights 400 / 500 / 700
7. After subscribing, the red morphs to gray — 200ms spring with haptic
8. Filter chips invert fully when selected (black bg / white text)
9. Dislike count is hidden (post-2021 decision)
10. Mini-player persists above the tab bar when user navigates during playback — load-bearing UX
11. Shorts is portrait-only 9:16 with floating overlays; Subscribe button appears as `+` on the avatar
12. Haptics: light on Subscribe / Like, success on share confirm, selection on filter chip
