# Design System Inspiration of Facebook (iOS)

## 1. Visual Theme & Atmosphere

Facebook's iOS app is a utility dashboard — dense, function-first, colorful, and recognizable from 25 paces by its one signature element: the Facebook Blue bar and `f` logo at the top of the screen. The canvas is a soft light gray (`#F0F2F5`) on light mode, specifically chosen so that the white post cards and stories strip float above it with just enough contrast to register as separate surfaces. Where X and Threads sit on flat, edge-to-edge black canvases, Facebook uses cards — each post, each ad, each suggested group — lives in its own white rectangle with a subtle shadow underneath. This is the most "card-based" of the social apps, and the visual rhythm is a vertical stack of discrete content units, not an unbroken river.

Color does three jobs on Facebook: Facebook Blue (`#1877F2`) is the brand — it paints the top nav bar, the "Like" thumbs-up icon when active, the primary CTA (Login, Post, Send), and any "Facebook" self-reference. The reactions system is its own palette — Love is a coral-pink heart (`#F3425F`), Haha/Wow/Sad share an amber yellow (`#F7B928`), Angry is an orange-red (`#E9710F`), Care is a warm yellow-hug (`#F7B928`). And the informational green (`#42B72A`, sometimes the "Create Post" green) tips users toward creation actions. Everything else is neutral gray — post metadata, timestamps, comment bubbles, dividers. The result is a chromatic app that uses color to signal role, not to decorate.

Facebook switched from its historical Helvetica / Klavika branding to the SF Pro platform default on iOS several years ago — the app now honors Apple's system font for all body UI. Large-format type is rare: the biggest moment is a 24-28pt post-detail title or a 32pt "f" glyph on splash. The majority of reading happens at 15pt w400 — the post body text — a size that must work for a very wide demographic. Facebook's type hierarchy is flatter and less expressive than X or Threads because the app is built for information density: a user's feed regularly contains 10+ content types (photo posts, text posts, shared links, Marketplace listings, event invites, Memory cards, Reels previews, ads) and the type system must accommodate all of them without shouting. The Reactions popover on a long-press of the Like button remains the app's most unmistakable micro-interaction: seven emoji reactions pop up in a horizontal row on a floating white pill, each scaling up with a spring as the user drags across them — the only place in the app where illustration-style icons live.

**Key Characteristics:**
- Soft-gray canvas (`#F0F2F5` light / `#18191A` dark) with white cards floating above
- Facebook Blue (`#1877F2`) on the top nav bar, `f` logo, Like icon active state, primary CTAs
- Card-based feed — every post, ad, suggestion is its own white rounded rectangle
- 5-tab bottom bar: Home, Video, Marketplace, Notifications, Menu
- Stories ring row at the top of Home — Facebook's Instagram-style stories imported
- Reactions popover on long-press — 7 emoji (Like, Love, Care, Haha, Wow, Sad, Angry) scale up in a horizontal floating pill
- Profile cover photo hero at top of profile — full-width banner with rounded avatar overlapping
- Comment threading — nested replies with a 1pt vertical rule connecting parent to children
- Reels tab in the bottom nav opens a TikTok-like fullscreen vertical video feed
- Live indicators — red `LIVE` badge on streaming content
- Memory cards — occasional full-width color cards (green, purple, blue gradient) with "On This Day" memories

## 2. Color Palette & Roles

### Primary
- **Facebook Blue** (`#1877F2`): The brand — top nav bar background, `f` logo, active Like thumb, primary CTAs ("Log In", "Post", "Send"), link color in post body.
- **Blue Pressed** (`#0A5FC8`): Pressed state on blue buttons.
- **Blue Light** (`#E7F3FF`): Background tint for Liked button when user has tapped it (a pale blue pill).

### Canvas & Surfaces (Light)
- **Canvas Gray** (`#F0F2F5`): Primary light canvas — the space between cards.
- **Card White** (`#FFFFFF`): Post cards, story thumbnails, profile cover, modals.
- **Surface Tint** (`#F7F8FA`): Slightly off-white for suggested content (grouped suggestions, Marketplace hero).
- **Divider** (`#E4E6EB`): Hairlines between comments, action rows.
- **Separator Bar** (`#CED0D4`): Thicker separator between feed sections (rare).

### Canvas & Surfaces (Dark)
- **Dark Canvas** (`#18191A`): Primary dark canvas.
- **Dark Card** (`#242526`): Post cards, modals, story thumbnails.
- **Dark Surface Tint** (`#3A3B3C`): Elevated surfaces — comment input fields, selected rows.
- **Dark Divider** (`#3E4042`): Hairlines.

### Text
- **Text Primary Light** (`#050505`): Primary body on light mode — a near-black softer than pure `#000000`.
- **Text Primary Dark** (`#E4E6EB`): Primary body on dark mode.
- **Text Secondary Light** (`#65676B`): Timestamps, comment metadata, secondary labels on light.
- **Text Secondary Dark** (`#B0B3B8`): Timestamps on dark.
- **Text Tertiary** (`#8A8D91`): Disabled text, lowest-emphasis metadata (e.g., "Sponsored"), shared across modes.

### Reactions Palette
Each Reaction has a distinct color. Reactions appear in the horizontal popover on long-press and as small color-dot indicators at the bottom of a post ("You, Sarah, and 23 others reacted").
- **Like Blue** (`#1877F2`): The classic Facebook Blue thumbs-up.
- **Love Pink** (`#F3425F`): Heart reaction.
- **Care Warm** (`#F7B928`): Hugging-a-heart reaction (introduced 2020).
- **Haha Yellow** (`#F7B928`): Laughing emoji reaction.
- **Wow Yellow** (`#F7B928`): Wide-mouth emoji reaction.
- **Sad Yellow** (`#F7B928`): Tear emoji reaction.
- **Angry Orange** (`#E9710F`): Red-faced anger reaction.

Note: Haha, Wow, Sad, and Care all share the same amber yellow as base color on their emoji faces. Their distinguishing feature is the emoji illustration, not the color.

### Semantic
- **Live Red** (`#FA3E3E`): "LIVE" badge on live video and streaming indicators.
- **Error Red** (`#FA383E`): Destructive actions, post-failed banners.
- **Success Green** (`#42B72A`): "Create Post" green pill CTA, success toast, new-message indicator.
- **Info Blue** (`#1877F2`): Same as primary Blue for informational callouts.
- **Sponsored Label** (`#8A8D91`): "Sponsored" text on ads.

### Memory / Story Ring Gradient
- **Story Ring (Gradient)**: `#C13584` → `#E1306C` → `#FD1D1D` → `#F77737` (pink → red → orange) — inherited from Instagram's story ring, used on Facebook Stories.
- **Memory Card Colors**: Blue `#1877F2`, Green `#42B72A`, Purple `#8B46C5` — used as gradient backgrounds for "On This Day" memory cards.

## 3. Typography Rules

### Font Family
- **Primary (current)**: `SF Pro` (Apple system font) — adopted as the platform default when Facebook moved away from custom branding for the iOS app. Uses SF Pro Text below 20pt and SF Pro Display at 20pt+ automatically.
- **Historical**: Previously `Helvetica` on iOS and `Klavika` on marketing/print. Current iOS app relies on the system font.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Helvetica Neue', Helvetica, Arial, sans-serif`
- **CJK / Non-Latin**: Apple's system font handles CJK, Arabic, Hebrew, etc. natively — no additional fonts needed.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| "f" Logo | SF Pro Display | 22pt | 900 | 1.0 | 0pt | The Facebook `f` in the top nav — weight 900 / Heavy |
| Screen Title | SF Pro Display | 24pt | 700 | 1.15 | -0.2pt | "Home", "Marketplace", "Menu" title on respective tabs |
| Section Header | SF Pro Display | 20pt | 700 | 1.2 | -0.1pt | "Stories", "Suggested for you", "Your Memories" |
| Post Display Name | SF Pro Text | 15pt | 600 | 1.3 | 0pt | Post author's display name |
| Post Body | SF Pro Text | 15pt | 400 | 1.35 | 0pt | The main post text — primary reading size |
| Post Body (Large Text Mode) | SF Pro Text | 24pt | 600 | 1.25 | 0pt | When user posts short text only, Facebook auto-styles it at 24pt on a colored background |
| Comment Body | SF Pro Text | 14pt | 400 | 1.3 | 0pt | Comment text inside the bubble |
| Comment Name | SF Pro Text | 13pt | 600 | 1.3 | 0pt | Commenter name at top of comment bubble |
| Timestamp / Meta | SF Pro Text | 13pt | 400 | 1.3 | 0pt | "2 hrs · Public", "12 comments" — color `#65676B` |
| Reaction Count | SF Pro Text | 13pt | 400 | 1.3 | 0pt | "You, Sarah and 45 others" |
| Primary Button | SF Pro Text | 15pt | 600 | 1.0 | 0pt | "Like", "Comment", "Share" action row labels |
| CTA Button | SF Pro Text | 17pt | 600 | 1.0 | 0pt | "Post", "Send", "Log In" — the filled blue pills |
| Tab Label | SF Pro Text | 11pt | 500 | 1.0 | 0pt | Bottom tab labels (shown always) |
| Link / Mention | SF Pro Text | inherit | 400 | inherit | 0pt | `#1877F2` inline |
| Sponsored / Ad | SF Pro Text | 13pt | 400 | 1.3 | 0pt | "Sponsored" label above ad card — color `#65676B` |

### Principles
- **System font, not custom**: Facebook uses SF Pro across iOS — no proprietary face in the current app. Respect the platform's native optical sizing.
- **Weight concentrated at 400 / 600 / 700 / 900**: Regular for body, semibold for names and buttons, bold for section headers, heavy for the `f` logo. No thin or light.
- **Dynamic Type respected broadly**: Post body, comment body, bio, message body all scale. Timestamps and tab labels cap at xLarge to preserve dense layouts.
- **Color on text is structural**: Blue for links, secondary gray for metadata, primary black on white canvas. No decorative text coloring.
- **Reactions count is plain text**: "You, Sarah, and 45 others" is rendered in standard secondary gray — the reaction emojis are the icon, not colored text.

## 4. Component Stylings

### Buttons

**Primary Blue CTA ("Post", "Send", "Log In")**
- Shape: Rounded rectangle, 8pt corner radius (not a pill — Facebook's buttons are blockier than X/Threads)
- Background: `#1877F2`
- Text: `#FFFFFF` SF Pro Text 17pt w600
- Padding: 12pt vertical, 24pt horizontal (full-width on login screens; inline on compose bar)
- Disabled: opacity 0.5
- Pressed: background `#0A5FC8` + scale 0.98
- Font: SF Pro Text 17pt w600

**Create Post Green Pill (Menu > "Create")**
- Shape: Pill, 500pt corner radius
- Background: `#42B72A`
- Text: `#FFFFFF` SF Pro Text 15pt w600
- Padding: 8pt vertical, 20pt horizontal
- Used sparingly — on the Menu tab for the "Create" entry and on Marketplace "List for Sale"

**Action Row Buttons (Like / Comment / Share)**
The row below every post. Three buttons evenly spaced.
- Shape: invisible button (no background by default), 40pt tall
- Text: SF Pro Text 15pt w600 `#65676B` (secondary gray)
- Icon: SF Pro glyph 20pt + 6pt gap + label
- Default Like: thumbs-up outline `hand.thumbsup` icon
- Active Like (user liked with default): thumbs-up filled in `#1877F2` + "Like" label turns `#1877F2`
- Active Reaction (user picked a non-Like reaction): shows the reaction emoji inline + label name ("Love", "Haha", etc.) in reaction color
- Long-press: opens Reactions popover (see Distinctive Components)
- Label background on active Like (rare): pill pill `#E7F3FF` light blue tint

**Follow / Add Friend Blue Pill**
- Shape: Rounded rectangle, 6pt corner radius
- Background: `#1877F2` (Add Friend) / `#E4E6EB` (Friends - neutral gray)
- Text: white for Add Friend; `#050505` for Friends
- Icon: `person.badge.plus` 16pt for Add Friend; `person.fill.checkmark` for Friends
- Padding: 8pt vertical, 16pt horizontal
- Font: SF Pro Text 15pt w600

### Cards & Containers

**Post Card (The Feed Unit)**
The card-based feed is Facebook's signature layout. Every feed item is a white card.
- Background: `#FFFFFF` light / `#242526` dark
- Corner radius: 8pt
- Margin: 8pt horizontal from canvas edge, 8pt vertical gap between cards
- Shadow: none by default (the light-gray canvas provides the visual separation); on press, subtle highlight
- Structure (top-to-bottom):
  - **Header row**: 40pt circular avatar + name/timestamp + overflow `ellipsis` — padding 12pt
  - **Post body**: SF Pro Text 15pt w400, aligned full-width within card (not indented after avatar — body takes full card width with 12pt horizontal padding)
  - **Media** (optional): 0pt-margin edge-to-edge inside card (card clips to rounded corners)
  - **Reaction summary**: small reactions icons + "You, Sarah, and 45 others" text in 13pt `#65676B` + "12 comments · 3 shares" — 12pt padding
  - **Action row**: 1pt `#E4E6EB` divider above, then 3 buttons (Like / Comment / Share) at 40pt tall evenly spaced
  - **Comment preview** (optional): Top 1-2 comments shown directly below action row, each in its own rounded bubble

**Stories Row (Top of Feed)**
- Height: 200pt
- Horizontal scroll of story thumbnails
- Each thumbnail: 110pt × 200pt rounded rectangle, 12pt corner radius
- Thumbnail structure: full-bleed background image + gradient overlay + 32pt circular avatar top-left + name text bottom-left
- First card: "Create a Story" — white card with `+` glyph and user's blurred profile pic as background
- Unread story: 3pt border in Instagram gradient (`#C13584` → `#E1306C` → `#FD1D1D` → `#F77737`) around avatar
- Read story: no border, 3pt gray ring around avatar

**Comment Bubble**
- Background: `#F0F2F5` light / `#3A3B3C` dark
- Corner radius: 16pt
- Padding: 8pt vertical, 12pt horizontal
- Structure: commenter name (13pt w600) + comment body (14pt w400) stacked
- Max width: 80% of card width, aligned to leading side (after the commenter avatar)
- Avatar: 32pt circular leading
- Nested replies: indented 44pt with a 1pt vertical rule connecting parent to child

**Ad Card**
- Same structure as a post card
- "Sponsored" label above the header row in 13pt `#65676B`
- CTA button at the bottom (above the action row) — "Shop Now", "Learn More", "Sign Up" — a `#E7F3FF` blue-tinted button with `#1877F2` text

**Memory Card**
- Full-width card, 200pt tall, colored gradient background (blue, green, or purple)
- "On This Day" label top
- "1 year ago" subtitle in white 15pt w400 80% opacity
- Memory content image or post preview center
- "Share" CTA at bottom

**Profile Cover Hero**
- Height: 200pt
- Full-width banner image (edge-to-edge, no margin)
- 168pt circular avatar overlapping the bottom of the cover, centered horizontally, with a 4pt white border
- Below: display name in 24pt w700, secondary line "Lives in …" in 15pt w400 `#65676B`

### Navigation

**Top Nav Bar (Home tab)**
- Height: 56pt + safe area (taller than iOS default due to the Facebook branding)
- Background: `#FFFFFF` light / `#242526` dark — not blue anymore (Facebook quietly moved away from the blue top bar in 2020 on iOS; it's now white/dark-card with a blue `f` logo)
- Leading: **blue `f` logo** 28pt — the brand anchor (SF Pro Display 22pt w900 in a blue rounded square OR the actual glyph image)
- Trailing: three circular icon buttons in a row — Search (`magnifyingglass`), Messenger (`ellipsis.message.fill`), and optionally a third depending on region — each 32pt inside a 36pt gray circle `#E4E6EB`
- Hairline divider 1pt `#E4E6EB` below

**Bottom Tab Bar**
- Height: 49pt + safe area (iOS default)
- Background: `#FFFFFF` light / `#242526` dark with 1pt top border `#E4E6EB`
- 5 Tabs (left to right): Home (`house`), Video (`play.rectangle`), Marketplace (`cart`), Notifications (`bell`), Menu (`line.3.horizontal`)
- Icon size: 26pt
- Active color: `#1877F2` (the blue)
- Inactive: `#65676B`
- Labels: SF Pro Text 11pt w500, shown always
- Active tab: filled icon variant; inactive: outlined
- Notifications tab: red badge `#FA383E` with white count number (bottom-right of bell icon)

**Tab-Level Top Nav (Marketplace / Video / Notifications / Menu)**
- Height: 44pt + safe area
- Leading: tab title in SF Pro Display 24pt w700
- Trailing: tab-specific icons (search, filter, etc.)

### Input Fields

**Compose Post Bar (Top of Feed)**
- Height: 56pt, white card, 8pt corner radius
- Leading: 40pt circular user avatar
- Center: "What's on your mind?" placeholder 15pt w400 `#65676B` in a pill-shaped tappable area with `#F0F2F5` fill, 500pt radius
- Trailing: two quick icons — photo picker 24pt `#45BD62` and live video 24pt `#FA3E3E`
- Tap anywhere: opens the full-screen Compose modal

**Search Bar**
- Height: 40pt
- Corner radius: 500pt (full pill)
- Background: `#F0F2F5` light / `#3A3B3C` dark
- Leading: `magnifyingglass` 16pt in `#65676B`
- Placeholder: "Search Facebook" in 15pt w400 `#65676B`

**Comment Input (at bottom of post detail)**
- Height: 40pt, grows vertically on long text
- Corner radius: 20pt (pill)
- Background: `#F0F2F5` light / `#3A3B3C` dark
- Leading: emoji picker 20pt
- Trailing: `paperplane.fill` blue when text present, `camera.fill` + gallery icons when empty

### Distinctive Components

**Reactions Popover (Long-press on Like)**
The signature Facebook micro-interaction.
- Horizontal floating pill appearing above the Like button on long-press
- Contains 7 emoji reactions in a row: Like (👍), Love (❤️), Care (🤗), Haha (😂), Wow (😮), Sad (😢), Angry (😠)
- Each icon: 40pt emoji illustration in a 48pt tap area
- Background: `#FFFFFF` light / `#242526` dark, shadow `rgba(0,0,0,0.2) 0 4px 16px`, 500pt radius pill
- Gesture: user long-presses Like → popover appears with pop animation (scale 0.5 → 1.0 + fade) → user drags across icons; hovered icon scales to 1.3 + label tooltip appears above it; release to select that reaction
- Haptic: `.impact(.light)` on open + `.selection` on each icon hover

**Like Thumb Icon with Count Summary**
- Below the post body, above the action row, a horizontal summary
- Leading: stacked reaction emoji icons (up to 3 overlapping in circles: Like + Love + Haha indicator dots)
- Text: "You, Sarah, and 45 others" in 13pt `#65676B` (tappable to see full reaction breakdown)
- Trailing: "12 comments · 3 shares" in 13pt `#65676B`

**Stories Ring Avatar**
- 60pt × 60pt circular avatar
- 3pt ring border in Instagram gradient for unread
- Tap: opens story viewer (same pattern as Instagram)

**Live Badge**
- Red `#FA3E3E` 500pt-radius pill
- Text: "LIVE" SF Pro Text 10pt w700 uppercase white
- Small pulsing red dot beside text
- Positioned on live video thumbnails, Watch Party entries

**Marketplace Listing Card**
- Card layout, 8pt radius
- Image top (square aspect, full-width of card)
- Price `$45` below image in SF Pro Display 17pt w700
- Title 15pt w400 in 2-line clamp
- Location / distance in 13pt `#65676B` below title
- Tap opens detail view

**Reels Vertical Feed**
- Fullscreen edge-to-edge vertical video player
- Leading action column: Like, Comment, Share, More — stacked vertically, each with count underneath
- Bottom: author info + caption
- Swipe up: next Reel; swipe down: previous
- This is accessed via the Video tab (or a Reels tab in some regions)

**Memory / On-This-Day Card**
- Full-width card with saturated gradient background (blue / purple / green)
- "On This Day" label top-left in SF Pro Display 15pt w700 white
- Year badge "1 year ago" 13pt
- Memory content (the original post thumbnail) center
- "Share Memory" CTA button bottom

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 56
- Card horizontal margin from canvas: 8pt (narrower than content apps — cards are close to the edges)
- Between cards: 8pt gap
- Inside-card padding: 12pt horizontal, 12pt vertical
- Action row buttons: evenly distributed across card width

### Grid & Container
- Content width: full device width minus 16pt (8pt left + 8pt right for cards)
- Stories row: 200pt tall, full-width horizontal scroll with 8pt inter-story gap
- Reactions popover: 7 icons at 48pt each = ~336pt wide, centered above the Like button

### Whitespace Philosophy
- **Card rhythm**: The feed's visual rhythm is driven by the 8pt canvas gap between cards — a subtle but constant drumbeat that separates each content unit
- **Dense inside, breathing outside**: Each card packs avatars, names, body, media, reactions, action row — but the gap between cards gives the eye rest
- **Profile hero is spacious**: The 200pt cover photo + 168pt avatar creates a heroic profile landing, distinct from the dense feed

### Border Radius Scale
- Blocky Button (6pt): Add Friend / Friends pills (slightly softer than pure rect)
- Standard (8pt): Post cards, CTA buttons, search bars on marketplace
- Thumbnail (12pt): Story thumbnails, video player controls
- Comment Bubble (16pt): Comment containers
- Pill (20pt): Comment input field, quick-action pills
- Full Pill (500pt): Reactions popover, "What's on your mind" pill, search bars
- Circle (50%): Avatars, reaction icons in popover, icon buttons in top nav

## 6. Depth & Elevation

Facebook uses cards with subtle shadows — the most elevation-aware of the social apps. The card surface separating from the gray canvas is the primary depth cue.

| Level | Treatment | Use |
|-------|-----------|-----|
| Canvas (Level 0) | `#F0F2F5` / `#18191A` — the between-card gray | Feed background |
| Card (Level 1) | `#FFFFFF` / `#242526` with no shadow (canvas contrast alone) | Post cards, ads, suggestions |
| Pressed Card (Level 1.5) | Card with slight darkening `rgba(0,0,0,0.03)` overlay | Card tap feedback |
| Reactions Popover (Level 2) | `rgba(0,0,0,0.2) 0 4px 16px` | Floating reactions on long-press |
| Floating Messenger (Level 3) | `rgba(0,0,0,0.3) 0 8px 24px` | Messenger chat head floating over content |
| Sheet (Level 3) | `rgba(0,0,0,0.25) 0 -4px 20px` | Bottom sheets, share sheet, reactions breakdown |
| Modal Backdrop | `rgba(0,0,0,0.5)` | Behind full-screen compose, media lightbox |

**Shadow Philosophy**: Cards don't cast their own drop shadows — they rely on the gray canvas providing visual separation. Shadows appear only on floating elements that truly need to elevate above content: the Reactions popover, floating Messenger chat heads, and bottom sheets.

### Motion
- **Like tap**: thumbs-up icon scales 0.9 → 1.15 → 1.0 spring over 300ms, fills in `#1877F2`, count ticks up, `.impact(.light)` haptic
- **Reactions long-press**: icons pop up with stagger (each icon +25ms delay) scale 0.5 → 1.0 spring 400ms total; hovered icon scales to 1.3 with tooltip
- **Reactions drag-select**: haptic `.selection` on each icon hover; on release, selected icon scales to 1.5 → 1.0 with `.impact(.medium)` haptic
- **Card tap**: subtle background darken 3% for 150ms on press; release for navigation
- **Stories ring tap**: ring animates (scale 1.0 → 0.9 → 1.0) + opens full-screen story viewer with crossfade
- **Memory card reveal**: confetti particles fall for 500ms on first view per day, paired with a warm haptic
- **Scroll-refresh**: pull-to-refresh with custom Facebook loading indicator (the `f` glyph appears and the dot inside bounces)
- **Reaction selected**: the corresponding emoji swaps into the action row's Like slot with a cross-fade 200ms + label changes color to match

## 7. Do's and Don'ts

### Do
- Use soft gray `#F0F2F5` as the light canvas — it's the between-card space that defines Facebook's rhythm
- Put every feed item in a white rounded card (8pt radius) — Facebook is the most card-based of social apps
- Use Facebook Blue `#1877F2` for the brand, active Like, primary CTAs, and links — consistently
- Keep the 5-tab bottom bar: Home / Video / Marketplace / Notifications / Menu — with labels always shown
- Use the Reactions popover on long-press — 7 emoji in a horizontal floating pill, each scaling as the user drags across
- Use SF Pro (system font) at weights 400/600/700/900 — the `f` logo at 900 is the only heavy weight
- Use the blue `f` glyph as the brand anchor in the top-left — not a word mark, just the `f`
- Use stories ring in Instagram's pink-red-orange gradient on unread stories
- Keep card padding at 12pt inside edges — consistent density across all card types
- Pair each tab label with both the icon and 11pt text — Facebook is label-forward, not icon-only like X/Threads

### Don't
- Don't use pure black canvas — Facebook's light mode canvas is `#F0F2F5`, dark is `#18191A`
- Don't hide tab labels — Facebook shows them; this is a deliberate choice for accessibility and utility
- Don't add colors beyond the Reactions palette — the 7 reaction colors are the expressive palette, nothing else
- Don't use heavy drop shadows on cards — the gray canvas does the separation work
- Don't use fancy custom fonts — the current app uses SF Pro, not Klavika or Helvetica Neue
- Don't remove the Reactions popover — the 7-emoji long-press is the signature interaction
- Don't use uppercase on button labels except the "LIVE" badge
- Don't default the top nav bar to blue — it's white/dark-card with a blue `f` anchor (the historical blue top bar is deprecated)
- Don't make stories square — they're 110pt × 200pt vertical rectangles, 12pt radius
- Don't use green as a primary accent — green (`#42B72A`) is reserved for creation CTAs only

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Card inner width ~359pt; story thumbnails 96pt wide |
| iPhone 13/14/15 | 390pt | Standard card width ~374pt; story thumbnails 110pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav — `f` shifts left of island |
| iPhone 15/16 Pro Max | 430pt | Card width ~414pt; story thumbnails 120pt; more comments preview visible |
| iPad | 768pt+ | Multi-column layout — feed in center, profile in right sidebar, nav in left rail |

### Dynamic Type
- Post body, comment body, bio, message body: full scale
- Display name, timestamps, action row labels: cap at xxLarge
- Tab labels: fixed 11pt (layout-sensitive)
- Reaction counts: fixed 13pt
- Large-text posts (when user types short text): already at 24pt, scale with Dynamic Type

### Orientation
- Feed / Marketplace / Notifications / Menu: **portrait-locked**
- Video / Reels: **rotates to landscape** on video playback
- Media lightbox (photo / video viewer): **rotates freely**

### Touch Targets
- Like / Comment / Share buttons: 40pt tall rows
- Reaction icons in popover: 48pt tap area each, 40pt glyph
- Tab bar icons: 26pt glyph, 49pt hit area
- Avatar tap: 40pt glyph, 44pt hit
- `ellipsis` overflow: 24pt glyph, 44pt hit

### Safe Area Handling
- Top: top nav respects safe area + Dynamic Island; `f` logo shifts left of island
- Bottom: tab bar respects home indicator
- Sides: 8pt card margin on all scrollable screens
- Story thumbnails extend slightly into the 16pt safe inset to achieve the full-bleed edge-peek effect

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#F0F2F5`
- Canvas (dark): `#18191A`
- Card white: `#FFFFFF`
- Card dark: `#242526`
- Divider: `#E4E6EB`
- Text primary (light): `#050505`
- Text primary (dark): `#E4E6EB`
- Text secondary: `#65676B`
- Facebook Blue: `#1877F2`
- Blue pressed: `#0A5FC8`
- Love pink: `#F3425F`
- Haha/Wow/Sad/Care yellow: `#F7B928`
- Angry orange: `#E9710F`
- Live red: `#FA3E3E`
- Success green: `#42B72A`

### Example Component Prompts
- "Build a Facebook post card in SwiftUI: 8pt corner radius white `#FFFFFF` background on a `#F0F2F5` canvas, 8pt horizontal margin from screen edge, 8pt gap between cards. Header row: 40pt circular avatar + display name 'Sarah Johnson' in SF Pro Text 15pt w600 `#050505` + '2 hrs · Public' in 13pt w400 `#65676B` + trailing `ellipsis` 24pt `#65676B`. Body: SF Pro Text 15pt w400 primary text with 1.35 line height, 12pt horizontal padding. Media edge-to-edge rounded to card corners. Reaction summary: 3 stacked reaction circles + 'You, Sarah, and 45 others' in 13pt `#65676B`. 1pt `#E4E6EB` divider, then action row with 3 buttons (Like / Comment / Share) at 40pt tall, SF Pro Text 15pt w600 `#65676B` + 20pt SF Symbol icons."
- "Create the Facebook Reactions popover on long-press of Like: horizontal floating pill at `#FFFFFF` 500pt corner radius with shadow `rgba(0,0,0,0.2) 0 4px 16px`. 7 reactions spread horizontally: 👍 Like, ❤️ Love, 🤗 Care, 😂 Haha, 😮 Wow, 😢 Sad, 😠 Angry — each 40pt emoji glyph in a 48pt tap area. Open animation: scale 0.5 → 1.0 spring 400ms with 25ms stagger per icon. On drag across: hovered icon scales to 1.3 with a tooltip label above ('Love', 'Haha', etc.) and `.selection` haptic. On release: selected icon bounces 1.5 → 1.0 + `.impact(.medium)` haptic + emoji swaps into action row Like slot with cross-fade 200ms."
- "Build Facebook's top nav bar: 56pt tall + safe area, white `#FFFFFF` background on light, `#242526` on dark, 1pt `#E4E6EB` divider bottom. Leading: blue `f` logo 28pt — use a blue `#1877F2` rounded square 32pt with white `f` glyph inside at weight 900 (or the actual brand glyph). Trailing: horizontal row of 36pt circular buttons with `#E4E6EB` background — `magnifyingglass` 20pt for search, `ellipsis.message.fill` 20pt for Messenger. Each button tap-area 44pt."
- "Design the Facebook tab bar: 49pt + safe area, 5 tabs with labels always shown. Tabs left-to-right: Home (`house.fill` / `house`), Video (`play.rectangle.fill` / `play.rectangle`), Marketplace (`cart.fill` / `cart`), Notifications (`bell.fill` / `bell` with red badge), Menu (`line.3.horizontal`). Active icon 26pt in `#1877F2` blue, label in SF Pro Text 11pt w500 blue. Inactive icon `#65676B`, label also `#65676B`. Notifications badge: 16pt red `#FA383E` circle at top-right of bell icon with white count number 11pt w700."
- "Create a Facebook Stories row: 200pt tall horizontal scroll, 8pt leading inset. First card 'Create a Story' with blurred user-profile-pic background + white `+` glyph in center + 'Create Story' label bottom. Subsequent cards: 110pt × 200pt rounded 12pt, full-bleed story-preview image, dark gradient overlay bottom 50% for text legibility, 32pt circular avatar top-left with a 3pt Instagram gradient border ring for unread (pink `#C13584` → red `#E1306C` → orange `#F77737`) or 3pt gray ring for read, name in SF Pro Text 13pt w600 white bottom-left."
- "Build the Facebook comment: leading 32pt circular avatar, trailing a comment bubble at `#F0F2F5` background, 16pt corner radius, 8pt × 12pt padding, max 80% of card width. Inside bubble: commenter name SF Pro Text 13pt w600 `#050505` + comment body SF Pro Text 14pt w400 `#050505`. Below bubble: 'Like · Reply · 2h' in SF Pro Text 13pt w600 `#65676B` with 12pt gaps between. Nested replies indented 44pt with a 1pt `#E4E6EB` vertical rule connecting parent to child."

### Iteration Guide
1. Canvas is soft gray `#F0F2F5` light / dark gray `#18191A` dark — cards are white/dark-card floating above
2. Every feed item is a CARD — 8pt corner radius, 12pt padding, 8pt gap between cards — the most card-based social app
3. Facebook Blue `#1877F2` is the brand color — top nav `f` logo, active Like, primary CTAs, links
4. The 5-tab bottom bar is canonical: Home / Video / Marketplace / Notifications / Menu with labels always shown (NOT icon-only)
5. Reactions popover on long-press of Like — 7 emoji in a horizontal floating pill, scale 1.3 on hover, haptic on each, swap into action row on release
6. SF Pro platform font at weights 400/600/700/900 — no custom font; the `f` logo at 900 is the only heavy weight
7. The top nav is NOT blue anymore — it's white/dark-card with the blue `f` anchor (historical blue bar is deprecated)
8. Stories ring uses Instagram pink-red-orange gradient for unread — inherited from Meta
9. Card shadows are subtle — the canvas gray alone provides the elevation cue; avoid heavy drop shadows
10. Action row labels are SF Pro 15pt w600 in secondary gray — turn blue when Like is active; show reaction emoji + label name if user picked a non-Like reaction
