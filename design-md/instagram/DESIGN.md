# Design System Inspiration of Instagram (iOS)

## 1. Visual Theme & Atmosphere

Instagram's iOS app is a study in chromatic restraint punctuated by one unforgettable gradient. The entire chrome — nav bar, tab bar, icons, typography — is rendered in pure monochrome (black on white, or white on true black), creating a neutral frame that lets photos and videos become the only colorful thing on screen. The content is the product; the interface is the picture frame, and the frame is deliberately invisible. This is the anti-skeuomorphic endgame — a UI so quiet that a user's 1:1 photograph, a friend's selfie, or a Reel's saturated grade commands 100% of the visual attention.

The single chromatic exception is the Instagram logomark gradient — that famous purple-to-pink-to-orange-to-yellow sweep (`#833AB4` → `#FD1D1D` → `#FCAF45`) — which appears in exactly three places: the app icon, the Stories ring for unread stories, and the Reels/Create action. Nowhere else. No buttons, no accents, no badges. This scarcity is what makes the gradient iconic. Everything else defaults to black (`#000000`) or white (`#FFFFFF`), with grays between them for secondary affordances.

Typography is SF Pro — the system face — with zero customization, except for the logotype at the top of the feed which uses a custom Billabong-inspired script. Headlines are absent; instead, small-but-confident UI text (13-17pt, weight 400-600) guides the user. Every control is a glyph or icon — Instagram famously stripped text labels from its tab bar years ago, trusting that users recognize the outline house, magnifying glass, Reels chevron-play, and circular profile without instruction. The interface says little and shows everything.

**Key Characteristics:**
- Pure monochrome chrome (black/white only) with the Instagram gradient as the single chromatic signature
- Content-as-hero layout: photos and videos span edge-to-edge with zero competing UI color
- SF Pro at small-to-medium sizes (11-17pt) — no display-size headlines anywhere
- Iconography dominates text labels — the tab bar has no words
- Signature gradient (`#833AB4` → `#FD1D1D` → `#FCAF45`) reserved for brand moments only
- Edge-to-edge media: feed posts, Reels, and Stories bleed to the device edges
- Full dark mode with true-black (`#000000`) for OLED battery efficiency
- Haptics on double-tap-to-like and tab-bar long-press — subtle but signature

## 2. Color Palette & Roles

### Primary (Monochrome)
- **Pure Black** (`#000000`): Dark mode canvas, light mode text, iconography. True black to max contrast and conserve OLED power.
- **Pure White** (`#FFFFFF`): Light mode canvas, dark mode text, iconography, tab bar background.
- **Secondary Gray** (`#8E8E8E` / `#A8A8A8`): Metadata text ("2h", view counts, placeholder copy, usernames in comments). Light mode uses `#8E8E8E`; dark mode uses `#A8A8A8` for legibility.
- **Separator** (`#DBDBDB` light / `#262626` dark): Hairline dividers between feed posts, between navigation and content, between stacked list rows.

### Interactive
- **Link Blue** (`#00376B` light / `#E0F1FF` dark): @mentions, #hashtags, linked URLs in captions and bios. Note: it's NOT system blue — it's deeper, navy-leaning.
- **Primary Button Blue** (`#0095F6`): The "Follow", "Log in", and "Sign up" pill buttons. The only blue chip in the app and it means "take action."
- **Primary Button Blue Pressed** (`#1877F2`): Pressed state — Meta-flavored blue, slightly deeper.
- **Destructive Red** (`#ED4956`): Heart-liked state, destructive actions ("Delete", "Report"), notification badges. Instagram red is pink-shifted vs. system red.

### Brand Gradient (Reserved)
The official Instagram brand gradient is a **10-stop diagonal sweep** (bottom-left to top-right) used in the app icon:
- `#FFDC80` (Yellow) → `#FCAF45` (Orange-Yellow) → `#F77737` (Orange) → `#F56040` (Red-Orange) → `#FD1D1D` (Red) → `#E1306C` (Rose) → `#C13584` (Purple-Red) → `#833AB4` (Purple) → `#5851DB` (Purple-Blue) → `#405DE6` (Blue)

A **3-stop shorthand** (`#833AB4` → `#FD1D1D` → `#FCAF45`) is commonly used in UI moments where the full 10-stop would be too busy.

The gradient appears ONLY in: the app icon, the logotype, unread Stories rings (as an angular gradient), the Create (+) tab icon, and occasional brand CTAs. Never on text, regular buttons, or decorative fills.

### Dark Mode Surfaces
- **Background** (`#000000`): Primary dark canvas — true black for OLED.
- **Elevated Surface** (`#121212`): Comment sheet backgrounds, settings rows grouped container.
- **Input Field** (`#1C1C1E` / `#262626`): Search fields, comment input, DM compose bubble background.
- **Incoming DM Bubble** (`#262626`): Received message bubble in dark mode.
- **Outgoing DM Bubble** (`#3797F0` → `#7644FF` gradient): Sent message — the only gradient bubble in messaging UI.

### Light Mode Surfaces
- **Background** (`#FFFFFF`): Primary light canvas.
- **Input Field** (`#EFEFEF`): Search bar fill, comment composer.
- **Incoming DM Bubble** (`#EFEFEF`): Received message bubble in light mode.
- **Pressed Row** (`#F5F5F5`): Tap-down state for list rows and settings items.

### Status & State
- **Online Green** (`#78DE45`): Active-now indicator dot on profile photos in DM and Close Friends.
- **Close Friends Green** (`#2FB825`): The Close Friends star/ring color.
- **Unread Red Dot** (`#ED4956`): Notification badges on the Activity tab and DM rows.

## 3. Typography Rules

### Font Family
- **System**: `SF Pro Text` (iOS default). Instagram respects the user's Dynamic Type preference throughout most of the app.
- **Logotype**: `Instagram Sans` (proprietary, based on Billabong script for the wordmark). Only used for the "Instagram" logo at the top of the Home feed — never body text.
- **Monospace**: `SF Mono` for story-reply typing indicators (rare).

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Logotype | Instagram Sans | 28pt | 400 | 1.0 | normal | Top of Home feed ONLY |
| Screen Title | SF Pro Text | 16pt | 600 | 1.25 | -0.2pt | Nav bar title (e.g. "Profile", "Messages") |
| Username (Post Header) | SF Pro Text | 14pt | 600 | 1.3 | -0.15pt | Above each feed post |
| Username (Large) | SF Pro Text | 20pt | 700 | 1.2 | -0.3pt | Profile screen username |
| Bio | SF Pro Text | 14pt | 400 | 1.4 | normal | Profile bio copy, multi-line |
| Caption | SF Pro Text | 14pt | 400 | 1.35 | -0.1pt | Post caption text under each feed item |
| Caption Truncated | SF Pro Text | 14pt | 400 | 1.35 | -0.1pt | 2-line clamp, "more" button in secondary gray |
| Comment | SF Pro Text | 14pt | 400 | 1.4 | -0.1pt | Comment body; username bold-inline |
| Secondary Meta | SF Pro Text | 12pt | 400 | 1.3 | 0pt | "2h", "View all 24 comments", view counts |
| Button (Primary) | SF Pro Text | 14pt | 600 | 1.0 | -0.15pt | "Follow", "Message", "Log In" |
| Button (Small Pill) | SF Pro Text | 12pt | 600 | 1.0 | -0.1pt | Story ring label, "See All" |
| Tab Label (legacy) | SF Pro Text | 10pt | 500 | 1.0 | 0.1pt | Currently hidden — tab bar is icon-only |
| Counter Large | SF Pro Text | 16pt | 700 | 1.1 | -0.2pt | Profile stats (posts / followers / following) |
| Counter Small | SF Pro Text | 12pt | 400 | 1.2 | 0pt | "followers" / "following" label under the number |
| DM Bubble | SF Pro Text | 16pt | 400 | 1.3 | -0.2pt | Message text inside chat bubbles |
| System Badge | SF Pro Text | 11pt | 700 | 1.0 | 0.2pt | Notification badge counts |

### Principles
- **No display-size type**: The biggest text in the entire app is the 28pt logotype. No 40pt hero headlines, no 60pt marketing type. The interface is designed for a phone held close to the face, not a billboard.
- **Weight concentrated at 400 / 600 / 700**: Regular, semibold, bold. Light (300) and heavy (800+) are never used. Semibold (600) carries almost all UI emphasis.
- **Dynamic Type support**: SF Pro scales with the user's accessibility setting. Numeric counters and nav titles use semantic styles so they grow; the Instagram logotype stays fixed.
- **Username-first hierarchy**: In every row (feed, comments, DM list), the username is the most emphasized element, always semibold. This reinforces that Instagram is about people, not posts.

## 4. Component Stylings

### Buttons

**Primary Action (Follow / Log In)**
- Background: `#0095F6`
- Text: `#FFFFFF`
- Padding: 7pt vertical, 16pt horizontal (full-width variants go to 12pt)
- Corner radius: 8pt
- Font: SF Pro Text, 14pt, weight 600
- Pressed: background shifts to `#1877F2`, scale 0.97
- Disabled: 50% opacity

**Secondary Action (Following / Message)**
- Background: `#EFEFEF` (light) / `#363636` (dark)
- Text: `#000000` (light) / `#FFFFFF` (dark)
- Padding: 7pt vertical, 16pt horizontal
- Corner radius: 8pt
- Font: SF Pro Text, 14pt, weight 600
- Pressed: background darkens one step

**Destructive (Delete / Report)**
- Background: transparent
- Text: `#ED4956`
- Font: SF Pro Text, 14pt, weight 600 (in sheets) / 17pt weight 400 (in action sheets)
- No border, no background — text only on sheet rows

**Icon Button (Heart, Comment, Share, Save)**
- Size: 24pt glyph, 44x44pt hit target
- Default: SF Pro / custom outlined glyph at current text color
- Liked (heart): fills to `#ED4956` with a 0.9 → 1.15 → 1.0 spring scale animation
- Double-tap-to-like: large heart animates from 0 → 1.4 → 0 over 0.6s in center of image, with success haptic

### Cards & Containers

**Feed Post**
- Background: current canvas color (no elevated fill)
- Border: top/bottom 0.5pt hairline in separator color (`#DBDBDB` / `#262626`)
- Corner radius: 0 (edge-to-edge)
- Photo: 1:1 square or portrait, NO corner radius, full device width
- Carousel dots: 6pt dots, 3pt gap, primary dot is `#0095F6`
- Structure: header (56pt) → media (1:1 square) → action bar (48pt) → caption → comments preview → timestamp

**Grid Tile (Profile / Explore)**
- Size: 1/3 of device width minus 2pt gaps
- Aspect: 1:1 square
- Corner radius: 0
- Gap between tiles: 2pt (feels tighter than most iOS grids — this is intentional)
- Hover/pressed: 85% opacity
- Video indicator: SF Symbol `play.fill` top-right, 14pt, white with subtle shadow
- Multi-image indicator: SF Symbol `square.on.square` top-right

**Story Ring**
- Ring diameter: 66pt (home rail) / 77pt (profile)
- Inner photo: 3pt inset from ring
- Unread ring: 2.5pt stroke with conic gradient (`#833AB4` → `#FD1D1D` → `#FCAF45`)
- Read ring: 1pt stroke in `#DBDBDB` light / `#262626` dark
- Your Story: plus badge overlay (bottom-right), 22pt blue circle with white `+`

**Sheet (Comments, Share, More)**
- Background: `#FFFFFF` (light) / `#121212` (dark)
- Corner radius: 12pt top corners only
- Top grabber: 36pt wide × 4pt tall pill, centered, 8pt from top, color `#C7C7CC` / `#48484A`
- Shadow: `rgba(0,0,0,0.2) 0 -4px 12px`
- Snap points: medium (50%) and large (92%) — never fully covers the status bar

### Navigation

**Bottom Tab Bar**
- Height: 49pt (plus safe area)
- Background: `rgba(255,255,255,0.92)` (light) / `rgba(0,0,0,0.92)` (dark) with `.regularMaterial` blur
- Icons: 5 tabs — Home, Search, Reels (gradient), Shop/Create (replaced by +), Profile photo (your own)
- Icon size: 24pt
- Active tab: icon filled variant; inactive: outlined variant
- No text labels (Instagram removed them in 2020)
- Profile tab uses the user's actual avatar with a 2pt selection ring when active
- Long press on Home tab opens account switcher with haptic feedback

**Top Nav (Home Feed)**
- Height: 44pt (plus safe area)
- Left: Instagram logotype (Instagram Sans, 28pt)
- Right: SF Symbol `heart` (Activity), SF Symbol `paperplane` (DMs), with red dot badges when unread
- Icon size: 24pt
- Background: canvas color (not translucent — feed scrolls under the safe area only)

**Top Nav (Other Screens)**
- Height: 44pt
- Back chevron: SF Symbol `chevron.backward`, 17pt, primary text color, left-aligned with 16pt inset
- Title: centered, 16pt weight 600
- Right action: single icon or short text action, right-aligned
- No large-title style — everything is compact inline

### Image & Media Treatment
- Feed photos: edge-to-edge, 1:1 square default, 4:5 portrait, 16:9 landscape; NEVER rounded corners
- Stories: full-screen (100% viewport), 9:16 aspect, rounded top corners (12pt) on the iPhone device frame only
- Reels: full-bleed 9:16 vertical video, overlay text in white with subtle shadow
- Profile avatar: circular, 2pt border in current background color (makes it "float" against story rings)
- Video thumbnails in grid: play icon top-right, white with 4pt shadow offset 0/2

### Input Fields

**Search Bar (Explore)**
- Background: `#EFEFEF` (light) / `#262626` (dark)
- Corner radius: 10pt
- Height: 36pt
- Leading SF Symbol `magnifyingglass`, 14pt, `#8E8E8E`
- Placeholder: "Search", same gray
- Text: 14pt, primary text color

**Comment Composer**
- Background: current canvas
- Top hairline: 0.5pt separator
- Avatar: 24pt circular on the left
- Text field: inline, no border, placeholder "Add a comment..."
- Right: "Post" button in `#0095F6` weight 600 14pt — disabled/hidden when field is empty

**DM Compose**
- Bubble-shaped input: `#EFEFEF` / `#262626`, corner radius 20pt, min-height 36pt, grows with content
- Leading: camera icon (blue), inline
- Trailing: mic / sticker icons when empty; send arrow when populated (blue)

### Distinctive Components

**Stories Rail**
- Horizontal scroll at top of Home feed
- Your Story first, then unread (gradient rings), then read (gray rings), then muted
- 8pt gap between rings
- Below each ring: username truncated to 10 chars, 11pt weight 400, centered

**Action Bar (under each feed post)**
- Height: 48pt
- Layout: heart, comment, share (left cluster) + save (right)
- Icon size: 24pt
- Gaps: 16pt between left icons, save pinned right with 16pt inset
- Double-tap feedback: large centered heart animation on the image above

**Like Count / Caption Block**
- "Liked by [user] and [n] others" — 13pt weight 600
- Caption: username (bold inline) + caption text, 2-line clamp by default
- "View all X comments" in secondary gray 14pt weight 400
- Up to 2 top comments previewed, username bold inline
- Timestamp: 11pt weight 400 UPPERCASE in secondary gray (e.g., "2 HOURS AGO")

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 2, 4, 6, 8, 12, 16, 20, 24, 32, 44, 56, 77
- Notable characteristic: 2pt micro-gap between grid tiles (tighter than most apps), 16pt inset for nav chrome, 4pt between icon rows in tab bar

### Grid & Container
- Max content width: device width (no content margins on feed posts)
- Safe-area aware: chrome (nav, tab bar) respects safe areas; media content ignores them to bleed to edge
- Profile grid: 3-column, 2pt gaps — content is king, chrome is minimal
- Settings list: standard iOS inset grouped style, 16pt horizontal padding, rows 44pt min-height

### Whitespace Philosophy
- **Edge-to-edge content, inset chrome**: The fundamental rule. Media bleeds; text/UI insets at 16pt.
- **Vertical density over horizontal**: Feed posts stack tightly (48pt action bar + 6pt to caption) but scroll is infinite. You can see ~1.2 posts per phone viewport.
- **No section headers**: Unlike Apple.com's chaptered sections, Instagram has zero visible section dividers beyond a 0.5pt hairline between posts.
- **The tab bar never disappears**: It's translucent, always present, always 49pt — the one persistent anchor.

### Border Radius Scale
- Square (0pt): Feed photos, Reels, grid tiles, Stories thumbnails
- Hairline (4pt): Comment composer prompts, small badges
- Standard (8pt): Buttons, follow pills, input fields (legacy)
- Comfortable (10pt): Search bars, DM bubbles (partial)
- Bubble (20pt): DM message bubbles, composer input
- Full Circle (50%): Profile avatars, story rings, action buttons, icon hit targets

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, solid background | Feed posts, list rows, tab content |
| Tab Bar Material | `.regularMaterial` blur over 92% white/black | Bottom tab bar — floats above scrolling feed |
| Nav Bar Material | `.regularMaterial` blur (scroll edge) | Top nav on Profile, Explore when content scrolls under |
| Sheet Elevation | `rgba(0,0,0,0.20) 0 -4px 12px` | Comment sheet, share sheet, action sheet |
| Double-tap Heart | Scale 0 → 1.4 → 1.0 + fade-out, spring damping 0.6 | The signature micro-interaction |
| Carousel Shadow | None | Posts do not elevate; carousels use dots not shadows |

**Shadow Philosophy**: Instagram uses almost zero drop shadows. Depth comes from the blur materials on tab/nav bars and the elevation implied by sheet presentations. Feed content lives on a single z-plane — nothing lifts, nothing hovers. The only "elevation" cue is the translucent glass of the tab bar and Comments sheet. This keeps the focus on the 2D content.

### Motion & Haptics
- **Double-tap to like**: Heart springs to 1.4 scale over 250ms, bounces back to 1.0, fades to 0 over 400ms. Paired with `.impactOccurred(.soft)` haptic.
- **Tab bar press**: `.selectionChanged()` haptic on tap
- **Long press on Home tab**: `.impactOccurred(.medium)` haptic → account switcher sheet
- **Pull to refresh on Feed**: Custom Instagram camera glyph rotates; standard iOS elastic with a branded spinner
- **Swipe between tabs in Reels**: 60fps snap with rubber-band resistance at edges
- **Story tap-and-hold**: Pauses timeline; UI chrome fades to 20% opacity over 150ms

## 7. Do's and Don'ts

### Do
- Keep all chrome monochrome — black, white, gray only
- Reserve the Instagram gradient for Stories rings, the Create action, and brand moments
- Use SF Pro — never introduce a custom body typeface
- Bleed media edge-to-edge with zero corner radius on photos and videos
- Use outlined icons by default, filled icons for active/selected state
- Respect the user's Dynamic Type setting on all UI text except the logotype
- Use `#0095F6` for the single "take action" button (Follow, Log in, Sign up)
- Pair every like/save/follow action with subtle haptics
- Use true black (`#000000`) for dark mode — not `#1C1C1E` — to serve OLED screens

### Don't
- Don't introduce new accent colors beyond the defined palette — the monochrome chrome is the brand
- Don't apply the brand gradient to buttons, text, or general UI — it's reserved
- Don't round feed photo corners — edge-to-edge squares and portraits are sacred
- Don't add text labels to the tab bar — the icons do the work
- Don't use large-title nav bars — Instagram is compact-title throughout
- Don't use shadows on feed cards — the app has a single z-plane for content
- Don't animate aggressively — motion is subtle (0.15-0.3s, spring damping 0.7+)
- Don't introduce skeuomorphic textures or gradients on UI chrome
- Don't replace SF Symbols with custom icons where a Symbol exists

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Tighter line-heights, tab bar icon gaps reduce to 12pt |
| iPhone 13 / 14 / 15 | 390pt | Standard layout |
| iPhone 15 / 16 Pro | 393pt | Standard layout, Dynamic Island accommodated |
| iPhone 15 / 16 Pro Max | 430pt | Slightly wider grid tiles, typography unchanged |
| iPad (Instagram is compact) | 768pt+ | App runs in compact-width mode — functionally identical to iPhone |

### Dynamic Type
- Usernames, captions, comments, DMs: scale with user setting (caption → body → larger variants up to accessibility XXXL)
- Counter numbers (followers, following, posts): scale 100% → 140% (xxxLarge cap)
- Tab bar icons: do NOT grow (fixed 24pt)
- Instagram logotype: does NOT grow (fixed 28pt)
- Nav titles: scale to a max of 20pt to stay within the 44pt nav bar

### Orientation
- Feed, Profile, Explore, Activity: **portrait-locked**
- Reels: **portrait-locked**
- Stories camera: **portrait-locked**
- Video player in full-screen: **rotates to landscape**
- DM: **portrait-locked**

### Touch Targets
- Minimum hit area: 44x44pt on all icons (even 24pt glyphs)
- Story rings: 66pt diameter gives a generous tap zone
- Grid tiles: full tile is tappable (125pt+ wide)
- Double-tap-to-like: full image area (~390pt wide × 390pt tall)

### Safe Area Handling
- Top: status bar + notch/Dynamic Island — content scrolls under a translucent nav on Profile/Explore
- Bottom: tab bar extends into the home indicator area with blur material
- Edge: media bleeds to left and right edges; text insets 16pt from safe area

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#000000`
- Text (light): `#000000`
- Text (dark): `#FFFFFF`
- Secondary gray: `#8E8E8E` (light) / `#A8A8A8` (dark)
- Separator: `#DBDBDB` (light) / `#262626` (dark)
- Primary action: `#0095F6`
- Destructive / heart-liked: `#ED4956`
- Link blue: `#00376B` (light) / `#E0F1FF` (dark)
- Brand gradient stops: `#833AB4` → `#FD1D1D` → `#FCAF45`
- Input field: `#EFEFEF` (light) / `#262626` (dark)

### Example Component Prompts
- "Build a SwiftUI Instagram feed post view: username row (28pt avatar + 14pt semibold username + `ellipsis` trailing icon), 1:1 square photo edge-to-edge with no corner radius, action bar with heart/comment/paperplane/bookmark (24pt SF Symbols, 16pt gap), like count at 13pt weight 600, caption with inline-bold username at 14pt weight 400, timestamp at 11pt uppercase in #8E8E8E."
- "Create a Story ring: 66pt outer circle with a 2.5pt conic gradient stroke (#833AB4, #FD1D1D, #FCAF45) for unread state, or 1pt gray stroke (#DBDBDB) for read state. Inner 57pt circular image with a 3pt inset. Below: username 11pt weight 400 centered, truncated to 10 chars."
- "Design an Instagram tab bar: 49pt height + safe area, .regularMaterial background at 92% opacity. Five equally-spaced icons at 24pt: house, magnifyingglass, play.rectangle (with brand gradient fill when active), plus.app (gradient), and a circular profile image. No text labels. Active icons use filled SF Symbol variants; inactive use outlined."
- "Build the Follow button: background #0095F6, corner radius 8pt, padding 7pt×16pt, text 'Follow' at 14pt weight 600 in white. Pressed state: background shifts to #1877F2 with 0.97 scale spring. Following state: background #EFEFEF, text #000000."
- "Create a comment composer: 48pt height bar pinned above keyboard, 24pt circular avatar on the left with 16pt padding, inline 14pt text field with placeholder 'Add a comment...', right-aligned 'Post' text button in #0095F6 weight 600 that disables when the field is empty."

### Iteration Guide
1. Chrome is ALWAYS monochrome — if you're adding color to an icon or button that isn't `#0095F6` or `#ED4956`, reconsider
2. The brand gradient is precious — reserve it for Stories rings, the Create action, and the logotype
3. Photos and videos are edge-to-edge with corner radius 0 — no exceptions in feed
4. Typography stays under 20pt everywhere except the 28pt logotype and the 77pt story ring on Profile
5. Tab bar is always translucent blur, always 5 icons, never labeled
6. Haptics on every meaningful interaction (like, save, follow, tab long-press)
7. Use SF Symbols first; fall back to custom only when Apple hasn't shipped the glyph
8. Dark mode is `#000000` pure — not the iOS `.systemBackground` default gray-black
