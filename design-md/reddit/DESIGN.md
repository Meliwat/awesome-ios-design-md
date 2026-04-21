# Design System Inspiration of Reddit (iOS)

## 1. Visual Theme & Atmosphere

Reddit's iOS app is a card-based community reader organized around the single most recognizable interactive control in consumer tech: the upvote/downvote column. Every post in a feed — whether from r/aww, r/AskReddit, or r/programming — is laid out the same way: a horizontal card with the vote arrow stack on the left (up arrow, karma number, down arrow), a subreddit avatar + "r/subredditname", a post title, optional media preview, and a row of actions (comments count, share, save, more). The consistency of this unit is the brand. A Reddit power user can scan 50 cards in 20 seconds because every card is structurally identical.

Reddit's color system is a two-pole light-mode vs dark-mode pair stitched together by the Reddit Orange-Red (`#FF4500`) — the single brand accent seen in the app logo, subreddit pill tints, and (historically) the upvote hover. The functional accent work, though, is done by a smaller palette: Upvote Orange (`#FF8717`) on active upvotes, Downvote Blue-Purple (`#7193FF` light / `#9494FF` dark) on active downvotes, and a grayscale for dormant votes. This is a meaningful chromatic decision: orange + blue-purple are complementary, they read semantically (warmth vs coolness) for approve vs disapprove, and they NEVER both light up at once because a user can only vote one way.

The mood is information-density-first. Reddit is a reading and scrolling app — people spend minutes in a thread, not seconds — so the app tolerates high-density cards with small type (14pt body, 12pt metadata) and uses ample white space ONLY inside cards (16pt internal padding), not between them. Newer Reddit iOS adds media-first card variants — photo-dominant, video-autoplaying — but the bone structure (vote column + meta + title + actions) stays. Subreddit customization is a signature feature: each community can set its own accent color (the "subreddit color"), its own banner image, its own sidebar rules, so a thread in r/AnimalCrossing looks cozy green and a thread in r/programming looks cool steel.

**Key Characteristics:**
- Reddit Orange-Red (`#FF4500`) as the brand color — logo, subreddit pill accent, rare CTAs
- Vote column on every post/comment — up arrow, karma number, down arrow in a vertical stack
- Upvote orange (`#FF8717`) and downvote blue-purple (`#7193FF`) as complementary semantic pair
- Card-first feed UI — every post is a self-contained card with avatar + title + preview + actions
- Subreddit avatars as circular icons — and subreddit accent color customization per community
- Flair pills (rectangular 4pt-radius chips) for post tags — per-subreddit styled
- Comment thread indentation lines — thin vertical rules on the leading edge of nested replies
- Snoo (the alien mascot) as the fallback avatar and Empty State character
- Chat Channels with real-time chat — a newer feature with Discord-like group messaging
- Media-first post variants — full-bleed image cards, video autoplay with mute toggle
- Light mode (`#F6F7F8` new / `#DAE0E6` old) and dark mode (`#1A1A1B` canvas, `#272729` cards)
- Reddit Sans (custom font, 2023+) with Noto Sans and SF Pro fallbacks

## 2. Color Palette & Roles

### Primary Brand
- **Reddit Orange-Red** (`#FF4500`): The single brand accent — logo, primary CTA pill, subreddit pill accent, occasional button.
- **Reddit Red** (`#FF585B`): Alerts, destructive banners, "community removed" warnings.
- **Orange-Red Pressed** (`#CC3700`): Pressed state for primary CTAs.

### Vote Colors (The Semantic Pair)
- **Upvote Orange** (`#FF8717`): Active upvote — slightly yellower than Reddit Red, intentionally distinct from the brand red.
- **Upvote Orange (Dark Mode)** (`#FFAA66`): Lighter variant for dark mode contrast.
- **Downvote Blue-Purple (Light)** (`#7193FF`): Active downvote — indigo-adjacent, cold.
- **Downvote Blue-Purple (Dark)** (`#9494FF`): Lighter variant for dark mode.
- **Vote Arrow Inactive** (`#878A8C` light / `#818384` dark): Gray dormant state.

### Canvas & Surfaces (Light Mode)
- **Canvas (New)** (`#F6F7F8`): Default light canvas — a cool very-light gray.
- **Canvas (Classic)** (`#DAE0E6`): The "old Reddit" blue-tinged light canvas, still used in some contexts.
- **Card Surface** (`#FFFFFF`): Post cards and comment threads sit on white.
- **Surface 2** (`#F2F3F5`): Hovered/pressed card state.
- **Divider** (`#EDEFF1`): Hairline between cards, between comments.
- **Subreddit Banner Placeholder** (`#0079D3`): Default blue tint if no custom banner.

### Canvas & Surfaces (Dark Mode)
- **Canvas (Dark)** (`#1A1A1B`): Primary dark canvas.
- **Card Surface (Dark)** (`#272729`): Post cards and comment threads.
- **Surface 2 (Dark)** (`#343536`): Hovered / pressed card state, input backgrounds.
- **Divider (Dark)** (`#343536`): Dark-mode separator.
- **Chat Surface (Dark)** (`#161617`): Slightly darker than canvas for chat screens.

### Text
- **Text Primary (Light)** (`#1A1A1B`): Post titles, body text.
- **Text Secondary (Light)** (`#7C7C7C`): Metadata — "2h ago", "r/subreddit • 125 comments".
- **Text Tertiary (Light)** (`#AFAFAF`): Placeholder, disabled.
- **Text Primary (Dark)** (`#D7DADC`): Dark-mode primary.
- **Text Secondary (Dark)** (`#818384`): Dark-mode metadata.
- **Text Link** (`#0079D3`): Hyperlinks in comments and post body.

### Subreddit Accent Colors
Each subreddit can set its own "accent color" — seen on the join button, the sidebar, and the banner tint. This is configurable per-community, commonly:
- Default (`#0079D3`), Gaming (`#24A0ED`), AskReddit (`#FF4500`), Memes (`#A5A4A4`), etc.

### Semantic
- **Success Green** (`#46D160`): Successful actions.
- **Warning Yellow** (`#FFB000`): Warning banners.
- **Error Red** (`#FF585B`): Error messages.
- **Gold Award** (`#FFB000`): Gold Award badge (Reddit removed in 2023 but still in historical data).
- **Silver Award** (`#C7C7C7`): Silver Award badge.
- **Reddit Premium** (`#FFD635`): Premium subscriber badge, Reddit Gold era.
- **NSFW Tag** (`#F3B200` pill with black text): "NSFW" post flair.
- **Spoiler Tag** (`#000000` pill with white text): "SPOILER" flair.

## 3. Typography Rules

### Font Family
- **Primary (2023+)**: `Reddit Sans` (proprietary, released 2023) — a humanist sans with gentle curves, designed for long reading sessions. Has Regular, Medium, SemiBold, Bold, Black.
- **Secondary (Fallback)**: `Noto Sans` — used for non-Latin scripts.
- **Tertiary (Fallback)**: `SF Pro Text` / `SF Pro Display` — iOS native fallback.
- **Monospace**: `Reddit Mono` (2023+) for code blocks; fallback `SF Mono`.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Noto Sans', Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | Reddit Sans | 24pt | 700 | 1.2 | -0.3pt | "Home", "Popular", subreddit name on detail |
| Post Title | Reddit Sans | 18pt | 600 | 1.25 | -0.2pt | The post title card headline |
| Body Text | Reddit Sans | 14pt | 400 | 1.5 | -0.1pt | Post body, comment body |
| Comment Body | Reddit Sans | 14pt | 400 | 1.5 | -0.1pt | Same as body (comments are first-class) |
| Metadata | Reddit Sans | 12pt | 400 | 1.3 | 0pt | "r/subreddit • 2h ago • 125 comments" |
| Karma (Vote Column) | Reddit Sans | 12pt | 700 | 1.0 | 0pt | The number between up/down arrows |
| Subreddit Pill | Reddit Sans | 12pt | 600 | 1.0 | 0pt | "r/AnimalCrossing" pill |
| Flair Pill | Reddit Sans | 11pt | 600 | 1.0 | 0.2pt | Per-subreddit colored flair tags |
| Username | Reddit Sans | 12pt | 500 | 1.2 | 0pt | "u/username" in comments |
| Button (Primary) | Reddit Sans | 14pt | 600 | 1.0 | 0pt | "Join", "Continue" |
| Tab Label | Reddit Sans | 11pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Nav Title | Reddit Sans | 17pt | 600 | 1.2 | -0.2pt | Top nav bar title |
| Section Header | Reddit Sans | 13pt | 700 | 1.2 | 0.5pt | "COMMUNITY INFO" tracked uppercase |
| Code (Mono) | Reddit Mono | 13pt | 400 | 1.5 | 0pt | Inline + block code in comments |

### Principles
- **14pt is the workhorse**: Post body, comments, replies all at 14pt regular. This is tuned for long-form reading in a chat-like density.
- **12pt for metadata**: Timestamps, subreddit pills, comment context all at 12pt — small but scannable.
- **18pt for post title** is the hero — big enough to catch attention on a card, small enough to fit multi-line titles.
- **Dynamic Type respected**: Body text and post titles scale; vote-column karma number, tab labels, and flair pills are fixed for layout.
- **Username hierarchy**: "u/username" in 12pt weight 500 — not as prominent as subreddit `r/` pills, because Reddit is community-first, not user-first.
- **Markdown rendering**: Post and comment bodies render full Markdown — `**bold**`, `*italic*`, `~~strike~~`, `> quote`, `[link](url)`, `` `code` ``, triple-backtick blocks, headers, lists.

## 4. Component Stylings

### Buttons

**Primary Pill (Join / Continue)**
- Shape: Pill, 24pt corner radius (or 500pt for full-capsule)
- Background: `#FF4500` for universal primary; subreddit-accent color for community-specific CTA
- Text: white, Reddit Sans 14pt weight 600
- Padding: 8pt vertical, 20pt horizontal
- Pressed: darker shade + scale 0.97
- Used for: "Join" (subreddit), "Continue", "Create Post"

**Secondary Outline**
- Background: transparent
- Text: subreddit accent color (or `#1A1A1B` / `#D7DADC` default)
- Border: 1pt solid `#EDEFF1` (light) or `#343536` (dark)
- Pressed: background `#F2F3F5` / `#343536`
- Used for: "Joined" (after joining), "Follow"

**Subreddit Join Button**
- Same as primary pill, but tinted in the subreddit's custom accent color
- When joined, shifts to outline style with "Joined" text
- Icon prefix: `+` (not joined) or `checkmark` (joined)

**Vote Arrow (The Signature Button)**
- Two stacked icons with a karma number between them
- Arrow icon: upward/downward chevron, 18pt
- Default (inactive): `#878A8C` / `#818384`
- Active up: `#FF8717` (orange)
- Active down: `#7193FF` / `#9494FF` (blue-purple)
- Hit target: 36pt × 36pt per arrow
- Tap: immediate state change + soft haptic + brief scale bounce (1.0 → 1.2 → 1.0)
- Karma number: Reddit Sans 12pt weight 700, center-aligned between arrows, color matches active vote state (orange/blue-purple/gray)

**Icon Actions (Comments, Share, Save, More)**
- Size: 18pt icon + optional count
- Layout: horizontal row below post content
- Default: `#878A8C` / `#818384`
- Active (saved): `#FFB000` gold
- Tap hit area: 44pt

### Cards & Containers

**Post Card (The Signature Component)**
A self-contained post as a card with structured sections.
- Background: `#FFFFFF` light / `#272729` dark
- Corner radius: 0pt (old Reddit style) or 16pt (new card style — user preference toggle)
- Padding: 12pt internal
- Structure (TOP to BOTTOM):
  1. **Header row** (10pt height): subreddit avatar (20pt circular) + `r/subreddit` pill + `•` + timestamp + `•` + optional "Pinned" or "Crosspost" tag
  2. **Title row**: post title in Reddit Sans 18pt weight 600, `#1A1A1B` / `#D7DADC`, up to 3-line clamp
  3. **Flair row** (optional): horizontal row of pill tags, 11pt weight 600
  4. **Preview row** (optional): image, video, gallery, link card, or text preview
  5. **Action row** (32pt height): Vote arrow column + karma (left) + comments count + share + save + more (right side)
- Between cards: 8pt gap with canvas color showing through
- Tap: slight background tint (`#F2F3F5` / `#343536`) + navigate to detail

**Post Card — Media-First Variant**
For image/video-dominant posts, the media takes the top 60% of the card with title + vote column overlaid or below:
- Image: full-width, up to 16:9 or 1:1 aspect
- 16pt radius on corners (when cards enabled); 0pt when classic view
- Title below image in 18pt weight 600
- Vote column at bottom-left; actions bottom-right

**Comment**
- Leading indentation line: 2pt `#EDEFF1` / `#343536` vertical bar, `Int × 10pt` from leading edge (each reply level indents 10pt)
- Structure: avatar (optional, 20pt circular) + username + `•` + timestamp + `•` + karma (if not hidden) — then body text
- Body: 14pt weight 400, line-height 1.5
- Trailing actions: reply, vote arrows, share, more (ghost/muted icons)
- Collapse indicator: `[-]` or `[+]` at leading edge to toggle thread collapse

**Subreddit Banner (Detail Screen)**
- Full-width banner image OR solid color matching subreddit accent
- Height: 120-180pt
- Subreddit avatar: circular 72pt, overlapping the bottom of banner by 36pt, white 3pt border
- Below: subreddit name (Reddit Sans 22pt weight 700), member count (12pt `#7C7C7C`)
- Trailing "Join/Joined" button at top-right of banner

**Chat Channel Card**
- Newer Reddit feature — group chat rooms
- Similar card layout to posts but with "Live" badge if active
- Member count + "chatting now" indicator

### Navigation

**Bottom Tab Bar**
- Height: 49pt + safe area
- Background: `#FFFFFF` (light) / `#1A1A1B` (dark) — solid, no blur
- Tabs: Home, Communities, Create (center, with `+` FAB-style), Chat, Inbox
- Active color: `#FF4500` (brand red) or `#D7DADC` (neutral — varies)
- Inactive: `#878A8C` / `#818384`
- Labels: Reddit Sans 11pt weight 500
- Badge: red `#FF585B` circle with white count

**Top Nav (Feed)**
- Height: 44pt + safe area
- Leading: profile avatar (28pt circular)
- Center: subreddit name or "Home" / "Popular" / "All" switcher (tap to open sheet)
- Trailing: search + sort + more
- Background: `#FFFFFF` light / `#1A1A1B` dark with bottom 1pt divider

**Top Nav (Subreddit Detail)**
- Transparent over banner image when scrolled to top
- Collapses to solid surface with subreddit name + back chevron on scroll
- Trailing: Join button + search + more

### Input Fields

**Compose (Create Post)**
- Full-screen modal
- Subreddit picker at top (pill with `r/pickme` placeholder)
- Title input: 18pt weight 600 placeholder "Title"
- Body input: 14pt regular placeholder "body (optional)"
- Formatting toolbar: Bold, Italic, Link, Quote, Code, List (flat icons, 20pt)
- Attach row: Photo/Video, Poll, Link, GIF
- Submit button: `#FF4500` pill "Post" in top-right nav

**Search Bar**
- Height: 40pt
- Corner radius: 20pt (pill)
- Background: `#F2F3F5` light / `#343536` dark
- Leading: `magnifyingglass` 18pt `#878A8C`
- Placeholder: "Search Reddit"

**Comment Input**
- Appears at bottom of thread when "Add a comment" tapped
- Expandable multiline; starts at ~40pt height, grows to ~200pt
- Corner radius: 20pt
- Focus: shows submit button at trailing right in `#FF4500`

### Distinctive Components

**Vote Column (The Signature)**
A vertical stack of three elements: up arrow, karma number, down arrow.
- Width: 44pt (standard) or 32pt (compact)
- Height: ~72pt (22pt arrow + 28pt number + 22pt arrow)
- Up arrow: `chevron.up` or custom up-tri icon, 18pt
- Number: Reddit Sans 12pt weight 700, center
- Down arrow: `chevron.down` or custom down-tri icon, 18pt
- State transitions:
  - None → Up: arrow fills orange, number turns orange, number increments, scale bounce
  - None → Down: arrow fills blue-purple, number turns blue-purple, number decrements, scale bounce
  - Up → None: arrow returns to gray, number to gray, decrements
  - Up → Down: arrow slides down, color changes, number decrements by 2, scale bounce
- Haptic: selection haptic on each state change
- Position: left side of post card (standard), or below media (media-first variant)

**Flair Pill**
- Small rectangular or rounded tag attached to post
- Per-subreddit styled — background color, text color, optional emoji prefix
- Shape: 4pt corner radius, 11pt weight 600 text
- Padding: 3pt vertical, 6pt horizontal
- Example tags: "News", "Discussion", "Shitpost", "NSFW", "Spoiler"
- System flairs:
  - "NSFW": `#F3B200` bg, black text
  - "SPOILER": black bg, white text
  - "OC" (Original Content): `#46D160` bg, white text

**Subreddit Pill (Inline `r/subredditname`)**
- Used inside post cards and anywhere a subreddit is referenced inline
- No background; subreddit avatar 16pt circular + `r/name` in 12pt weight 600
- Tap: navigate to subreddit
- Color: brand red `#FF4500` when top-level header, neutral `#1A1A1B` / `#D7DADC` when inline

**Comment Thread Indentation**
- Each nested reply indents 10pt
- A 2pt wide vertical line on the leading edge of nested replies in `#EDEFF1` / `#343536`
- Lines are continuous — when you scroll down the reply chain, the line follows
- Collapse on tap: `[-]` → `[+]` toggles hide sub-thread

**Snoo (The Alien Mascot)**
- Reddit's owl-eyed alien mascot in red-and-orange spacesuit
- Appears on: empty state illustrations, error 404 pages, splash screen, settings > about
- Also featured in Snoovatar feature — users design their own Snoo as their profile avatar

**Award Icons (Deprecated but still visible)**
- Pre-2023 awards: Silver, Gold, Platinum — shown as small icons next to post/comment titles
- Still visible on historical posts even though the feature was removed
- Icons: 16pt round with metal-colored backgrounds

**Media Gallery**
- When a post has multiple images, render as horizontal swipe gallery with pagination dots at bottom
- 1-indexed counter in top-right corner: "1/4"

**Live Chat Channel**
- Reddit Chat Channels render as a Discord-like threaded chat with real-time messages
- Each message: avatar + username (in subreddit accent color) + message body + timestamp
- "N typing" indicator at bottom
- Join/Leave affordance

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 10, 12, 16, 20, 24, 32, 44, 72
- Standard margin: 16pt horizontal on screens
- Card padding: 12pt internal
- Card-to-card gap: 8pt
- Comment indentation step: 10pt per reply level

### Grid & Container
- Feed: single column, full-width cards with 16pt side margins (or 0pt full-bleed in classic view)
- Card content: left-aligned; vote column left, actions right
- Chat: full-width single column with 16pt padding

### Whitespace Philosophy
- **Inside cards**: generous 12-16pt padding
- **Between cards**: minimal 8pt gap — the canvas color acts as a visual break
- **Comment thread**: tight vertical density (10pt between comments) to fit long discussions
- **Post detail**: comfortable reading density (16pt gaps between body sections)

### Border Radius Scale
- Square (0pt): Classic view post cards, dividers
- Soft (4pt): Flair pills, compact buttons
- Standard (8pt): Media corners in classic view, code blocks
- Card (16pt): New card-style post cards, subreddit banner
- Pill (500pt / 24pt): Primary CTAs, Join button, search bar
- Full Circle (50%): Avatars, subreddit icons, up/down arrows on vote

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (0) | No shadow | Cards sitting on canvas (card radius provides visual separation) |
| Hover (1) | `rgba(0,0,0,0.04)` background tint | Pressed card state |
| Floating (2) | `rgba(0,0,0,0.08) 0 2px 8px` | Floating "Back to top" button, overlay toast |
| Sheet (3) | `rgba(0,0,0,0.2) 0 -12px 32px` | Bottom action sheet (sort, filter, more) |
| Modal Overlay | `rgba(0,0,0,0.5)` backdrop | Modal dim |

**Shadow Philosophy**: Reddit is largely flat. Cards sit on the canvas with either a corner radius or a hairline divider — not shadows. Only genuinely floating UI (the "back to top" pill, bottom-sheet actions) has shadows.

### Motion
- **Vote tap**: Arrow icon scales 1.0 → 1.25 → 1.0 spring, color cross-fades, karma number tick animates + haptic selection
- **Card tap**: 200ms ease-out background tint to `#F2F3F5` / `#343536`
- **Navigate to detail**: Native iOS push transition; card stays in place at feed top
- **Comment expand/collapse**: 60fps height animation, 200ms ease
- **Join button**: "Join" → "Joined" with checkmark scale bounce + green flash
- **Media gallery swipe**: Horizontal page gesture with spring snap
- **Pull-to-refresh**: Red Snoo alien icon spins as refresh indicator
- **Infinite scroll**: New cards fade + slide up 8pt over 150ms
- **Chat message arrival**: Slide up from bottom + opacity fade over 200ms

## 7. Do's and Don'ts

### Do
- Use `#FF4500` (Reddit Orange-Red) as the single brand accent — logo, key CTAs
- Use the semantic vote pair: `#FF8717` upvote orange and `#7193FF` downvote blue-purple
- Show the vote column on EVERY post and comment — it's the brand
- Keep cards flat — use corner radius (16pt in card view, 0pt in classic) not shadows
- Render Markdown inline: bold, italic, quote, code, link
- Support subreddit accent color customization — the Join button should tint to community color
- Use circular subreddit avatars — `r/subredditname` pill next to avatar
- Indent comment replies 10pt per level with a 2pt vertical rule on the leading edge
- Use Reddit Sans (fallback Noto Sans, then SF Pro) at 14pt body / 18pt title / 12pt meta
- Offer both card view (16pt corners) and classic view (0pt full-bleed) as a user preference

### Don't
- Don't flip the semantic vote colors — upvote MUST be warm (orange) and downvote cool (blue-purple)
- Don't put the vote column anywhere other than left of post content (or below media in media-first)
- Don't use drop shadows on post cards — Reddit is flat
- Don't skip the subreddit `r/name` pill — every post must identify its community
- Don't use brand red `#FF4500` for the upvote — that's `#FF8717` orange
- Don't use blue `#0079D3` as an accent — that's the LINK color, not the brand color
- Don't round subreddit avatars to anything other than circles
- Don't compress the metadata row — `r/subreddit • timestamp • comments` should be readable
- Don't use Discord-style role colors for usernames — Reddit keeps usernames neutral
- Don't animate vote state changes too aggressively — a subtle bounce + color swap is enough

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Vote column collapses to 32pt; title clamp 2 lines |
| iPhone 13/14/15 | 390pt | Standard 44pt vote column; 3-line title clamp |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in nav |
| iPhone 15/16 Pro Max | 430pt | Standard layout with slightly wider cards |
| iPad | 768pt+ | Two-column feed in landscape |

### Dynamic Type
- Post title, body, comment body: respect Dynamic Type
- Karma number, tab labels, flair pills, vote arrow icons: fixed size
- Timestamp / metadata: respects Dynamic Type but capped at 14pt

### Orientation
- Feed, thread, chat: portrait-locked on iPhone (except video posts in landscape)
- Image / video viewer: rotates to landscape
- iPad: all orientations; feed becomes 2-column in landscape

### Touch Targets
- Vote arrows: 36pt × 36pt each, stacked
- Card tap: full card surface
- Action row icons: 44pt hit area
- Subreddit pill tap: hit area expands around pill
- Comment collapse arrow: 32pt hit area

### Safe Area Handling
- Top: nav bar respects safe area; transparent over banner scrolls into solid
- Bottom: tab bar respects home indicator; chat compose bar floats above safe area
- Sides: 16pt content insets

## 9. Agent Prompt Guide

### Quick Color Reference
- Reddit Orange-Red: `#FF4500`
- Reddit Red (alert): `#FF585B`
- Upvote Orange: `#FF8717`
- Upvote Dark Variant: `#FFAA66`
- Downvote Blue-Purple (light): `#7193FF`
- Downvote Blue-Purple (dark): `#9494FF`
- Vote Inactive (light): `#878A8C`
- Vote Inactive (dark): `#818384`
- Canvas (new light): `#F6F7F8`
- Canvas (classic light): `#DAE0E6`
- Canvas (dark): `#1A1A1B`
- Card surface (light): `#FFFFFF`
- Card surface (dark): `#272729`
- Surface 2 (light): `#F2F3F5`
- Surface 2 (dark): `#343536`
- Divider (light): `#EDEFF1`
- Divider (dark): `#343536`
- Text primary (light): `#1A1A1B`
- Text secondary (light): `#7C7C7C`
- Text primary (dark): `#D7DADC`
- Text secondary (dark): `#818384`
- Link: `#0079D3`
- Default subreddit accent: `#0079D3`
- Gold Award / Premium: `#FFB000`
- Success green: `#46D160`

### Example Component Prompts
- "Create a SwiftUI Reddit vote column: 44pt wide, 72pt tall stack. Top: up-chevron icon 18pt (gray #878A8C default, orange #FF8717 when active). Center: karma number in Reddit Sans 12pt weight 700, centered, color matching active state. Bottom: down-chevron icon 18pt (gray default, #7193FF blue-purple when active). Tap: scale 1.0 → 1.25 → 1.0 spring on icon + selection haptic + number tick increment. Three states: neutral, up (orange), down (blue-purple)."
- "Build a Reddit post card: background #FFFFFF light / #272729 dark, 16pt corner radius, 12pt internal padding. Top row: 20pt circular subreddit avatar + `r/AnimalCrossing` pill in 12pt weight 600 + '• 2h ago • 125 comments' in 12pt #7C7C7C. Next: post title in Reddit Sans 18pt weight 600 #1A1A1B, max 3 lines. Optional flair row: horizontal pills with per-subreddit colors. Image preview if available, 16pt corners. Bottom action row: vote column left + (comments icon + count, share icon, save icon, more-ellipsis) right at 18pt each #878A8C."
- "Design the Reddit comment thread row: leading indentation bars at 10pt per reply level, 2pt wide #EDEFF1. Header row: 20pt avatar (optional) + 'u/username' 12pt weight 500 + '• 2h' timestamp 12pt #7C7C7C + '• 234' karma 12pt weight 500. Body text in Reddit Sans 14pt weight 400 line-height 1.5. Support markdown inline. Trailing action row: reply icon + vote arrows + share + more. Collapse indicator `[-]` / `[+]` at leading edge toggles sub-thread visibility."
- "Create the subreddit banner detail: full-width banner image (120-180pt tall) or solid accent color. Circular subreddit avatar (72pt) with white 3pt border overlapping the bottom edge by 36pt. Below: subreddit name in Reddit Sans 22pt weight 700, member count '142k members' in 12pt #7C7C7C. Top-right of banner: Join pill in subreddit accent color (#FF4500 default, or custom like #0079D3), white text 14pt weight 600, pill 24pt corner radius — morphs to 'Joined' with checkmark prefix after tap."
- "Build a Reddit flair pill: rectangular tag, 4pt corner radius, 3pt vertical × 6pt horizontal padding. Reddit Sans 11pt weight 600. Per-subreddit styled — backgrounds and text colors configurable. System flairs: 'NSFW' in `#F3B200` bg + black text, 'SPOILER' in black bg + white text, 'OC' in `#46D160` bg + white text."
- "Design the Reddit bottom tab bar: 5 tabs — Home, Communities, Create (center, +-icon styled differently), Chat, Inbox. Icons 22pt. Active color: #FF4500 (brand) or #1A1A1B (neutral — both in use). Inactive: #878A8C. Labels: Reddit Sans 11pt weight 500. Background: solid #FFFFFF light / #1A1A1B dark, no blur. Red `#FF585B` badge pill on Inbox tab when unread messages exist."

### Iteration Guide
1. The vote column is non-negotiable — up-chevron + karma number + down-chevron appears on EVERY post and comment
2. Upvote is ORANGE `#FF8717` and downvote is BLUE-PURPLE `#7193FF` — these are complementary and semantic (warm vs cool)
3. Reddit brand red `#FF4500` is the BRAND color (logo, primary CTA) — it is NOT the upvote color. Do not confuse.
4. Cards are FLAT — use 16pt corner radius in card view, 0pt full-bleed in classic view, NEVER drop shadows
5. Canvas is `#F6F7F8` (new light) / `#1A1A1B` (dark); cards sit on `#FFFFFF` light / `#272729` dark
6. Subreddit accent color is configurable per community — the Join button and sidebar tint adapt
7. Comment threads indent 10pt per reply with a 2pt `#EDEFF1` / `#343536` vertical rule on the leading edge
8. Typography: Reddit Sans (fallback Noto Sans, then SF Pro). 14pt body / 18pt title / 12pt meta. Weights 400, 500, 600, 700.
9. Flair pills are 4pt radius rectangles with per-subreddit or per-system color; NSFW `#F3B200`, Spoiler black, OC green
10. Render full Markdown inline in post body and comments — bold, italic, quote, code, link
