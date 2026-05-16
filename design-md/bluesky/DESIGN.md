# Design System Inspiration of Bluesky (iOS)

## 1. Visual Theme & Atmosphere

Bluesky's iOS app is a clean, friendly, sky-bright timeline that feels open and uncluttered. The default light surface is pure white (`#FFFFFF`) with near-black text (`#0B0F14`), and it offers two darker variants: a soft "Dim" mode on a desaturated slate (`#1E2936`) and a deep "Dark" mode (`#0B0F14`). The triple-theme choice is a deliberate accessibility-and-comfort gesture — Bluesky wants you to pick the brightness that suits your eyes, not impose one. Across all three, the timeline is a quiet column of skeet cards separated by hairline dividers, with content given room to breathe.

The accent system is anchored by Bluesky Blue (`#1185FE`) — a confident, slightly electric sky-blue used for links, the active tab, the compose FAB, primary buttons, and the butterfly logomark. Its pressed state deepens to `#0F6FD6`. The single most expressive interaction is the like: a heart that does a springy pop-scale (and a small color fill to the same blue family) when tapped — light, joyful, and instantly legible. The other signature is structural rather than chromatic: a row of pinned, switchable custom-feed tabs at the very top of Home ("Following", "Discover", and any algorithmic feeds you've pinned), making the feed itself a first-class, user-controlled object.

Typography is the system font (San Francisco) at comfortable reading sizes, mapped to an Inter fallback for cross-platform parity. Skeet bodies sit at 15pt with generous line height; display names are 15pt semibold; handles (`@user.bsky.social`) are 14pt in muted gray. The butterfly logo and the pinned-feed selector are the two identity anchors a user sees first. There are no heavy shadows, no skeuomorphism — just clean type, a single bright accent, and a layout that makes the user's choice of feed and theme the center of the experience.

**Key Characteristics:**
- Triple theme: Light `#FFFFFF` / Dim `#1E2936` / Dark `#0B0F14` — user picks comfort level
- Bluesky Blue (`#1185FE`) for links, active tab, compose FAB, primary actions, butterfly logo
- Like = heart pop-scale spring — the joyful signature gesture
- Pinned custom-feed selector tabs at the top of Home — the feed is user-controlled
- Skeet card action row: Reply · Repost · Like · Share, evenly spaced
- Reply-controls chip on the composer (who can reply)
- Butterfly logomark as the brand anchor
- System font at reading sizes (15pt body), Inter fallback
- Bottom tabs (Home / Search / Notifications / Chat / Profile) + compose FAB

## 2. Color Palette & Roles

### Primary
- **Bluesky Blue** (`#1185FE`): Links, active tab tint, compose FAB, primary button fill, butterfly logo, mentions/hashtags.
- **Blue Pressed** (`#0F6FD6`): Active/pressed state for blue CTAs and the FAB.
- **Like Red** (`#EC4899`): The heart's liked fill — a warm pink-red that pops against the blue UI.

### Light Mode (Default)
- **Canvas** (`#FFFFFF`): Primary background, timeline surface.
- **Surface 1** (`#F1F3F5`): Cards-as-rows, search field, grouped settings.
- **Surface 2** (`#E2E8F0`): Pressed rows, chip backgrounds, hover-equivalent.
- **Divider** (`#E2E8F0`): Hairline separators between skeets.
- **Text Primary** (`#0B0F14`): Skeet body, display names, headlines.
- **Text Secondary** (`#697787`): Handles, timestamps, metadata, counts.
- **Text Tertiary** (`#8B98A5`): Disabled labels, low-emphasis hints.

### Dim Mode
- **Dim Canvas** (`#1E2936`): Primary background — a soft desaturated slate.
- **Dim Surface 1** (`#27313E`): Cards-as-rows, elevated areas.
- **Dim Surface 2** (`#2E4052`): Pressed rows, chips.
- **Dim Divider** (`#2E4052`): Hairline separators.
- **Dim Text Primary** (`#F1F3F5`): Body, names.
- **Dim Text Secondary** (`#9CA6B5`): Handles, metadata.

### Dark Mode
- **Dark Canvas** (`#0B0F14`): Primary background — deep near-black slate.
- **Dark Surface 1** (`#161E27`): Cards-as-rows.
- **Dark Surface 2** (`#1E2936`): Pressed rows, sheets.
- **Dark Divider** (`#1E2936`): Hairline separators.
- **Dark Text Primary** (`#F1F3F5`): Body, names.
- **Dark Text Secondary** (`#8B98A5`): Handles, metadata.

### Semantic
- **Error Red** (`#EC4899`)/(`#E5484D`): Validation/destructive (`#E5484D`); like uses pink `#EC4899`.
- **Success Green** (`#2DBE85`): Posted/confirmed, follow-confirmed.
- **Warning Amber** (`#F5A623`): Content-label warnings, rate limits.

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro Text` / `SF Pro Display` (system) on iOS
- **Cross-platform parity**: `Inter` (web/Android reference)
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'Inter', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Monospace**: `ui-monospace, 'SF Mono', Menlo, monospace`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | SF Pro Display | 28pt | 700 | 1.2 | -0.4pt | "Notifications", "Search" large nav title |
| Section Header | SF Pro Display | 21pt | 700 | 1.2 | -0.3pt | "Suggested for you", profile sections |
| Display Name | SF Pro Text | 15pt | 600 | 1.25 | -0.1pt | Poster's display name on a skeet |
| Skeet Body | SF Pro Text | 15pt | 400 | 1.4 | 0pt | The post text |
| Body (Settings) | SF Pro Text | 15pt | 400 | 1.4 | 0pt | Settings descriptions |
| Handle / Meta | SF Pro Text | 14pt | 400 | 1.3 | 0pt | `@user.bsky.social`, "2h", reply context |
| Feed Tab Label | SF Pro Text | 15pt | 600 | 1.2 | -0.1pt | "Following", "Discover" pinned-feed tabs |
| Action Count | SF Pro Text | 13pt | 500 | 1.2 | 0pt | Reply/repost/like numeric counts |
| Button (Primary) | SF Pro Text | 15pt | 600 | 1.0 | -0.1pt | "Post", "Follow" |
| Button (Secondary) | SF Pro Text | 14pt | 600 | 1.0 | 0pt | "Following", outline actions |
| Tab Label | SF Pro Text | 10pt | 600 | 1.0 | 0.1pt | Bottom tab bar labels |
| Reply Context | SF Pro Text | 13pt | 400 | 1.3 | 0pt | "Reply to @name" line above a skeet |

### Principles
- **Comfortable body at 15pt / 1.4**: readable without being heavy — Bluesky is conversational
- **Handle always visible**: `@user.bsky.social` is a 14pt structural element
- **Weight at 400 / 500 / 600 / 700**: regular body, medium counts, semibold names/feeds, bold titles
- **System font, Inter parity**: native iOS feel matching the web app's Inter rendering
- **Blue text only for links/mentions/hashtags** — body text never uses the accent

## 4. Component Stylings

### Buttons

**Primary Button ("Post" / "Follow")**
- Background: `#1185FE`
- Text: `#FFFFFF`
- Padding: 10pt vertical, 20pt horizontal
- Corner radius: 500pt (full pill — Bluesky favors pills for primary actions)
- Font: SF Pro Text 15pt weight 600
- Pressed: `#0F6FD6`, scale 0.97
- Disabled: `#1185FE` at 40% opacity

**Secondary / Following Button**
- Background: transparent
- Text: `#0B0F14` (light) / `#F1F3F5` (dark)
- Border: 1pt solid `#E2E8F0` (light) / `#2E4052` (dim/dark)
- Padding: 8pt vertical, 18pt horizontal
- Corner radius: 500pt
- Pressed: background `#F1F3F5`

**Compose FAB**
- Shape: Circle, 56pt diameter, bottom-right, 16pt from edges, floats above tab bar
- Background: `#1185FE`
- Icon: SF Symbol `square.and.pencil` 24pt in `#FFFFFF`
- Shadow: `rgba(17,133,254,0.35) 0 6px 20px`
- Pressed: scale 0.94, `#0F6FD6`, soft haptic

**Skeet Action Icons (Reply / Repost / Like / Share)**
- Size: 18pt glyph, 44pt hit area
- Default: `#697787` (light) / `#8B98A5` (dark)
- Repost active: `#2DBE85` (green)
- Like active: `#EC4899` (pink-red), with pop-scale spring + fill
- Spacing: evenly distributed across the card's content width
- Count: 13pt w500 trailing each icon when > 0

### Cards & Containers

**Skeet Card (THE core unit)**
- Background: matches canvas (flat, no shadow); separated by a 1pt divider
- Padding: 12pt vertical, 16pt horizontal
- Header: 42pt circular avatar → display name (15pt w600) + `@handle` (14pt `#697787`) + " · 2h" → trailing ⋯
- Reply context line above the header when applicable: "Reply to @name" 13pt `#697787`
- Body: 15pt w400, 1.4 line height; links/mentions/hashtags in `#1185FE`
- Embed: link card (rounded 12pt, thumbnail + title + domain) or media grid (12pt radius)
- Quote-skeet: a nested bordered card (1pt `#E2E8F0`, 12pt radius) inside the body
- Action row: Reply · Repost · Like · Share evenly spaced, counts trailing

**Pinned Custom-Feed Selector**
- A horizontally scrollable tab strip pinned at the very top of Home
- Tabs: "Following", "Discover", + any pinned algorithmic feeds
- Selected: `#1185FE` text with a 3pt rounded blue underline indicator
- Unselected: `#697787`
- Last tab is a "+" or feed-settings affordance to manage pinned feeds
- Switching slides the timeline with a 0.25s cross-fade

**Profile Header**
- Banner full-width, 130pt tall
- Avatar 80pt circular, overlapping the banner by 36pt, 4pt canvas-colored ring
- Display name 21pt w700, `@handle` 15pt `#697787`
- Bio with clickable mentions/hashtags in blue
- "Follow" pill primary + ⋯
- Followers / Following counts inline (15pt, count bold)
- Tab strip: Posts / Replies / Media / Likes / Feeds

**Notification Row**
- 42pt avatar(s) + actor name(s) + action ("liked your post", "reposted", "followed you")
- Type glyph tint: pink like heart, green repost, blue follow
- Tap opens the related skeet or profile

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(255,255,255,0.94)` (light) / `rgba(11,15,20,0.94)` (dark) with `.regularMaterial` blur
- Tabs: Home, Search, Notifications, Chat, Profile
- Icon size: 26pt
- Active: `#1185FE`; Inactive: `#697787` (light) / `#8B98A5` (dark)
- Labels: SF Pro Text 10pt w600
- Notifications/Chat tabs show a blue unread dot/count badge

**Top Nav (Home)**
- Logo: butterfly mark centered or leading, `#1185FE`, 28pt
- Pinned-feed selector strip directly below
- Trailing settings/gear icon (22pt)
- Blur-on-scroll background

### Input Fields

**Search Field**
- Background: `#F1F3F5` (light) / `#1E2936` (dim) / `#161E27` (dark)
- Height: 40pt, corner radius 500pt (pill)
- Leading `magnifyingglass` 16pt `#697787`
- Placeholder: "Search", 15pt w400 `#697787`
- Focus: 1.5pt `#1185FE` ring

**Compose Editor**
- Full-screen sheet, plain text area at 18pt w400
- Character ring counter (300 default) that turns `#E5484D` past the limit
- Toolbar: media, GIF, alt-text, language, and a **reply-controls chip** above the keyboard
- Reply-controls chip: "Everybody can reply" → tap to restrict (Mentioned / Followed) — turns `#1185FE` when restricted

### Distinctive Components

**Switchable Custom Feeds**
- The pinned-feed selector is the signature: the timeline is not one algorithm — it's a user-curated set of feeds you tab between, treating "the feed" as a first-class, swappable object

**Like Pop**
- Tapping the heart fills it pink `#EC4899` and pop-scales 1.0 → 1.25 → 1.0 over ~300ms with a spring; un-liking scales back to outline
- Paired with a light impact haptic; the count rolls up by one

**Reply-Controls Chip**
- On the composer, an inline chip stating who can reply, echoing Bluesky's reply-gating model; blue when restricted — a visible consent control

**Butterfly Logomark**
- The brand anchor, rendered in `#1185FE`, used in the splash, the empty states, and the Home nav

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56
- Standard margin: 16pt horizontal on skeet cards
- Skeet vertical rhythm: 12pt top/bottom padding; cards separated by 1pt dividers

### Grid & Container
- Content width: full device width, 16pt internal padding
- Media grid: 1 image full-width 12pt radius; 2–4 in a 2-col 12pt-radius mosaic, 2pt gaps
- Link/quote embeds: full-width nested card, 12pt radius, 1pt border

### Whitespace Philosophy
- **Conversational, not dense**: 15pt body at 1.4 line height; cards have comfortable internal padding
- **Dividers, not boxes**: separation is a hairline — the timeline stays visually quiet
- **Feed selector is sticky**: the user's curated feeds are always one tap away at the top

### Border Radius Scale
- Square (0pt): Full-bleed banners only
- Soft (8pt): Small chips, inline tags
- Comfortable (12pt): Media attachments, link/quote embed cards
- Large (16pt): Bottom sheets, modals
- Full Pill (500pt): Primary/secondary buttons, search field, the feed-tab underline is rounded
- Circle (50%): Avatars, compose FAB, like/repost hit circles

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Skeet cards, timeline, list rows |
| Card Hairline (Level 1) | 1pt divider per theme | Skeet-to-skeet separation |
| Embed Border (Level 1) | 1pt border, 12pt radius | Link cards, quote-skeets nested in body |
| FAB (Level 2) | `rgba(17,133,254,0.35) 0 6px 20px` | Floating compose button |
| Sheet (Level 3) | `rgba(0,0,0,0.15)` (light) / `rgba(0,0,0,0.5)` (dark) `0 -12px 40px` | Compose sheet, menus |
| Tab Bar Material | `.regularMaterial` over 94%-opaque canvas | Bottom tab bar over scrolling content |

**Shadow Philosophy**: Bluesky is flat by design — the timeline reads like a clean document. The only real elevation is the blue compose FAB (a brand-tinted shadow) and modal sheets. Cards and embeds use 1pt borders/dividers rather than drop shadows. In dark/dim modes, the sheet scrim deepens to 0.5; in light mode it stays barely-there at 0.15. The brightness comes from the canvas and the single blue accent, not from lighting.

### Motion
- **Like pop**: heart fills `#EC4899` and scales 1.0 → 1.25 → 1.0 over ~300ms spring, light haptic, count rolls
- **Repost**: icon turns `#2DBE85` with a quick 1.0 → 1.1 → 1.0 bounce, success haptic
- **Feed switch**: 0.25s cross-fade + small horizontal nudge between pinned feeds
- **Compose FAB**: scale 0.94 on press, soft haptic; sheet presents with a 0.35s spring
- **Theme switch**: instant token swap with a 0.2s color cross-fade across surfaces
- **Pull-to-refresh**: standard system spinner, no custom flourish

## 7. Do's and Don'ts

### Do
- Offer all three themes: Light `#FFFFFF`, Dim `#1E2936`, Dark `#0B0F14`
- Reserve Bluesky Blue (`#1185FE`) for links, mentions, hashtags, active tab, FAB, primary buttons, and the butterfly logo
- Make the like a pink (`#EC4899`) pop-scale spring — it's the joyful signature
- Keep the pinned custom-feed selector at the top of Home and easy to switch
- Always show the full `@user.bsky.social` handle
- Show the reply-controls chip on the composer so reply-gating is visible
- Keep cards flat; separate with hairline dividers and 1pt embed borders
- Use the butterfly mark as the brand anchor in `#1185FE`

### Don't
- Don't ship only one theme — the triple-theme choice is part of the product
- Don't add accent colors beyond blue (and the pink like / green repost) — keep it clean
- Don't truncate the handle's `.bsky.social` domain
- Don't bury custom feeds in settings — the feed selector is first-class
- Don't box every skeet with heavy borders/shadows — dividers keep it quiet
- Don't make the like a static toggle — the spring pop is the personality
- Don't shrink body text below 15pt — Bluesky is conversational and readable
- Don't use heavy elevation — the only shadow is the FAB and modal sheets

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Avatar 38pt; media grid still 2-col |
| iPhone 13/14/15 | 390pt | Standard 42pt avatar, 16pt padding |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the large title |
| iPhone 15/16 Pro Max | 430pt | Body measure widens; FAB stays 56pt |
| iPad | 768pt+ | Split view: feed + thread, max 640pt reading column, persistent left rail |

### Dynamic Type
- Skeet body, names, bios: full scale (conversational app — honor it)
- Action counts, timestamps: scale modestly
- Tab labels: fixed 10pt
- Feed-tab labels: scale modestly, fixed minimum 15pt

### Orientation
- Timeline / Profile: portrait and landscape (iPad split view)
- Media lightbox: rotates to landscape for wide media
- Compose: portrait-locked on iPhone

### Touch Targets
- Skeet action icons: 18pt glyph, 44pt hit circle
- Compose FAB: 56pt
- Tab icons: 26pt glyph, 44pt effective hit
- Feed-selector tabs: full label + 12pt padding tappable

### Safe Area Handling
- Top: butterfly nav and feed selector sit below safe area / Dynamic Island
- Bottom: FAB floats 16pt above the tab bar; both respect the home indicator
- Sides: 16pt content insets; banner edge-to-edge

## 9. Agent Prompt Guide

### Quick Color Reference
- Light canvas: `#FFFFFF` · Dim canvas: `#1E2936` · Dark canvas: `#0B0F14`
- Light surface 1: `#F1F3F5` · Dark surface 1: `#161E27`
- Divider: `#E2E8F0` (light) / `#2E4052` (dim) / `#1E2936` (dark)
- Text primary: `#0B0F14` (light) / `#F1F3F5` (dim+dark)
- Text secondary: `#697787` (light) / `#8B98A5` (dark)
- Bluesky Blue (action/link): `#1185FE`
- Blue pressed: `#0F6FD6`
- Like pink: `#EC4899`
- Repost green: `#2DBE85`

### Example Component Prompts
- "Create a SwiftUI Bluesky skeet card: 42pt circular avatar leading, display name 'Paul' in SF Pro 15pt weight 600 + handle '@pfrazee.com' in 14pt #697787 + ' · 2h', trailing ⋯. Body text 15pt weight 400 at 1.4 line height with links/mentions in #1185FE. Below: Reply · Repost · Like · Share evenly spaced, icons 18pt, 13pt weight-500 counts. Flat card matching canvas with a 1pt #E2E8F0 divider beneath."
- "Build the Bluesky like button: an 18pt heart, default #697787; on tap it fills #EC4899 and pop-scales 1.0 → 1.25 → 1.0 over ~300ms with a spring and a light impact haptic, and the trailing count rolls up by one. Tapping again returns to the outline state."
- "Design the Bluesky pinned custom-feed selector: a horizontally scrollable tab strip at the top of Home with 'Following', 'Discover', and pinned algorithmic feeds; selected tab uses #1185FE text with a 3pt rounded blue underline, unselected is #697787. Switching cross-fades the timeline over 0.25s."
- "Create the Bluesky compose FAB: a 56pt circular button, #1185FE, bottom-right 16pt from edges, SF Symbol 'square.and.pencil' 24pt #FFFFFF, shadow rgba(17,133,254,0.35) 0 6px 20px. Pressed: scale 0.94, #0F6FD6, soft haptic; opens the compose sheet with a 0.35s spring and a reply-controls chip in the toolbar."
- "Build a Bluesky quote-skeet embed: a nested card inside the body with a 1pt #E2E8F0 border and 12pt radius, containing a 24pt avatar + name + handle and a 2-line clamped quoted body, the whole embed tappable to open the quoted post."

### Iteration Guide
1. Ship three themes — Light `#FFFFFF`, Dim `#1E2936`, Dark `#0B0F14` — the choice is the product
2. Bluesky Blue `#1185FE` is links + navigation + primary actions + the butterfly; pink `#EC4899` like, green `#2DBE85` repost
3. The like is a springy pink pop-scale — make it the joyful moment
4. The pinned custom-feed selector lives at the top of Home — the feed is user-controlled
5. Always render the full `@user.bsky.social` handle
6. Cards are flat; separate with hairline dividers and 1pt embed borders — quiet timeline
7. Body text is 15pt at 1.4 line height — conversational and readable
8. The only elevation is the blue FAB and modal sheets — no heavy shadows
