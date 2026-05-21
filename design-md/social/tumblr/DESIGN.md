# Design System Inspiration of Tumblr (iOS)

## 1. Visual Theme & Atmosphere

Tumblr's iOS app is a deep-navy canvas built for an endlessly scrolling dashboard of expressive, multi-format posts. The signature background is a rich midnight blue (`#001935`) — not black, not gray, a saturated navy that feels like the inside of a zine printed on dark stock. Posts sit on slightly lighter navy surfaces (`#001020` and `#0B2A45`) so the dashboard reads as a stack of cards floating in blue space. The atmosphere is intentionally moody and creative: this is a place for GIFs, fan art, text essays, and reblog chains, and the dark navy lets full-bleed imagery and animated GIFs glow without UI competing for attention.

The accent system is unusually multi-chromatic for a social app — Tumblr leans into its playful, anti-corporate identity with three accents: Tumblr Blue (`#00B8FF`) for primary actions, links, and the active tab; a punchy green (`#00CF35`) for follow/confirm and notes-positive states; and a hot pink-red (`#FF4930`) for the like heart and its burst animation. This trio is deliberate brand personality — Tumblr has never been monochrome-minimal, and the colors signal "expressive, irreverent, yours." The single most distinctive interaction is the like: tapping the heart triggers a confetti-like heart-burst that scatters tiny hearts outward, a beloved micro-moment of Tumblr delight.

Typography is the system font (San Francisco) mapped to an Inter/Helvetica fallback, used cleanly so the content — long text posts, quotes, photo sets, GIF reblogs — carries the expression. The structurally distinctive component is the reblog chain: a thread of indented, attributed blocks where each reblogger's commentary stacks beneath the original, with the source blog name in blue at the top of each segment and a left rule indenting the quoted ancestry. The tag bar (a row of `#tags` under each post) and the notes-count row ("1,204 notes") are first-class Tumblr metadata. The whole system says: deep, moody, expressive, and built for remix culture.

**Key Characteristics:**
- Deep-navy canvas (`#001935`) with darker navy surfaces — a midnight zine, not black
- Three accents: Blue `#00B8FF` (actions/links), Green `#00CF35` (follow/notes), Pink-red `#FF4930` (like)
- Heart-burst on like — scattered confetti hearts, the signature delight moment
- Reblog chain — indented, attributed commentary blocks stacked with a left rule
- Tag bar (`#tags`) and notes-count row as first-class post metadata
- Multi-format dashboard: text, photo, GIF, quote, link, audio, video posts
- "Radar" / recommended widget surfacing a featured post
- System font (Inter/Helvetica fallback), clean so content expresses
- Bottom tabs (Home / Search / New post-center / Activity / Account), dark default

## 2. Color Palette & Roles

### Primary
- **Tumblr Blue** (`#00B8FF`): Primary CTA, links, active tab, reblog-source blog name, the "+" new-post FAB.
- **Blue Pressed** (`#0090CC`): Active/pressed state for blue CTAs.
- **Tumblr Green** (`#00CF35`): Follow button, confirm/success, positive notes accent.
- **Tumblr Pink-Red** (`#FF4930`): The like heart, the heart-burst confetti, liked-state.

### Canvas & Surfaces (Dark — the default)
- **Navy Canvas** (`#001935`): The signature deep-navy dashboard background.
- **Surface 1** (`#001020`): Lowest surface — nav bars, deep wells, the darker navy.
- **Surface 2** (`#0B2A45`): Post cards, elevated rows, the card the dashboard stacks.
- **Surface 3** (`#13395B`): Pressed states, hover-equivalent, input fields.
- **Divider** (`#36465D`): Hairline separators, reblog-chain left rule.
- **Text Primary** (`#FFFFFF`): Post body, blog names, headlines.
- **Text Secondary** (`#8A9AAE`): Timestamps, notes counts, metadata, reblog "via".
- **Text Tertiary** (`#5C6B80`): Disabled labels, low-emphasis hints.

### Semantic
- **Like Pink-Red** (`#FF4930`): Like heart active + burst (same as accent pink-red).
- **Notes Green** (`#00CF35`): Positive notes / new-activity accent.
- **Error Red** (`#FF4930`): Destructive confirms reuse the pink-red.
- **Warning Amber** (`#FFB300`): Mature-content / flagged-post labels.
- **Link Blue** (`#00B8FF`): In-body links and `@mentions` (same as accent blue).

### Light Mode (Limited Use)
Tumblr offers a light theme, but the iOS default and the brand-defining experience is the deep navy. A light variant exists for users who choose it.
- **Light Canvas** (`#FFFFFF`)
- **Light Surface** (`#F4F6F8`)
- **Light Text** (`#001935`)

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro Text` / `SF Pro Display` (system) on iOS
- **Cross-platform parity**: `Inter`, then `Helvetica Neue`
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'Inter', 'Helvetica Neue', 'Segoe UI', Roboto, Arial, sans-serif`
- **Quote / serif posts**: an optional serif (`Georgia`) for quote-type posts — Tumblr renders quotes with editorial weight

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | SF Pro Display | 28pt | 700 | 1.2 | -0.4pt | "Search", "Activity" large nav title |
| Quote Post | Georgia / SF Pro Display | 26pt | 700 | 1.3 | -0.3pt | Big editorial quote-type post text |
| Section Header | SF Pro Display | 20pt | 700 | 1.2 | -0.2pt | "Radar", "Recommended for you" |
| Blog Name | SF Pro Text | 16pt | 700 | 1.25 | 0pt | Poster's blog name on a post header |
| Post Body | SF Pro Text | 16pt | 400 | 1.5 | 0pt | Text-post body — long-form friendly |
| Reblog Comment | SF Pro Text | 15pt | 400 | 1.45 | 0pt | Commentary in a reblog-chain segment |
| Body (Settings) | SF Pro Text | 15pt | 400 | 1.4 | 0pt | Settings descriptions |
| Tag | SF Pro Text | 14pt | 400 | 1.3 | 0pt | `#tags` in the tag bar (blue-tint on press) |
| Notes / Meta | SF Pro Text | 13pt | 600 | 1.3 | 0pt | "1,204 notes", timestamps |
| Button (Primary) | SF Pro Text | 16pt | 700 | 1.0 | 0pt | "Follow", "Reblog", "Post" |
| Tab Label | SF Pro Text | 10pt | 600 | 1.0 | 0.1pt | Bottom tab bar labels (often icon-only) |
| Reblog Source | SF Pro Text | 14pt | 700 | 1.2 | 0pt | Blue blog-name atop each chain segment |

### Principles
- **Long-form friendly**: 16pt body at 1.5 line height — Tumblr text posts can be essays
- **Quote posts get editorial weight**: 26pt, optionally serif — quotes are a first-class post type
- **Weight at 400 / 600 / 700**: regular body, semibold notes/meta, bold names/buttons
- **Blue is the link/source color**: reblog-source blog names and in-body links are `#00B8FF`
- **Clean type, expressive content**: the system font stays neutral so GIFs/art/essays carry the voice

## 4. Component Stylings

### Buttons

**Primary Button ("Post" / "Reblog")**
- Background: `#00B8FF`
- Text: `#001935` (dark navy text on bright blue for contrast)
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: 6pt (Tumblr uses tight, slightly-soft rectangles)
- Font: SF Pro Text 16pt weight 700
- Pressed: `#0090CC`, scale 0.98

**Follow Button**
- Background: transparent
- Text: `#00CF35`
- Border: 1.5pt solid `#00CF35`
- Padding: 8pt vertical, 18pt horizontal
- Corner radius: 6pt
- Active "Following": filled `#00CF35` with `#001935` text
- Pressed: background `#13395B`

**New Post FAB (Center Tab)**
- Shape: Circle, 52pt, raised in the center of the tab bar
- Background: `#00B8FF`
- Icon: SF Symbol `plus` 24pt in `#001935`
- Shadow: `rgba(0,184,255,0.35) 0 6px 18px`
- Pressed: scale 0.94, `#0090CC`, soft haptic; opens the post-type picker (text/photo/quote/link/chat/audio/video)

**Post Action Icons (Reply / Reblog / Like / Share)**
- Size: 22pt glyph, 44pt hit area
- Default: `#8A9AAE`
- Reblog active: `#00CF35` (green)
- Like active: `#FF4930` (pink-red) + heart-burst
- Spacing: evenly distributed across the post footer; notes count leading-left

### Cards & Containers

**Dashboard Post Card (THE core unit)**
- Background: `#0B2A45` on the `#001935` navy canvas
- Corner radius: 8pt; full content width with 8pt horizontal margins (near edge-to-edge)
- Margin: 8pt between cards (navy shows as a thin gutter)
- Header: 36pt rounded-square avatar (6pt radius) → blog name (16pt w700) → trailing follow + ⋯
- Body: format-dependent —
  - **Text**: 16pt w400, 1.5 line height
  - **Photo/GIF**: full-bleed media, 0pt radius inside the card top, no letterboxing
  - **Quote**: 26pt editorial (optionally serif) + "— source" attribution
  - **Link**: a preview block (thumbnail + title + domain) with 6pt radius
- Tag bar: a wrapping row of `#tags` (14pt w400 `#8A9AAE`, tap → blue)
- Footer: notes count (13pt w600) on the left, then Reply · Reblog · Like · Share on the right
- The whole card is a single tappable surface; pressed state lifts to `#13395B`

**Reblog Chain (the signature)**
- A vertical stack of attributed segments inside the post body
- Each segment: blue source blog name (14pt w700 `#00B8FF`) at top, then that blogger's commentary (15pt w400)
- Each ancestor level is indented with a 2pt `#36465D` left rule and ~12pt left padding (deeper = older)
- The newest commentary sits at the bottom, unindented; the original post is the deepest-indented block
- Tapping a source name opens that blog

**Notes Sheet**
- Bottom sheet listing likes/reblogs/replies, each row: avatar + blog name + action glyph (green reblog, pink like)
- Tabbed: "All" / "Comments" / "Reblogs" / "Likes"

**Radar / Recommended Widget**
- An inset card between dashboard posts: "Radar" label + a single featured post preview + "Why you're seeing this"
- Slightly brighter `#13395B` background to distinguish from organic posts

**Profile / Blog Header**
- Customizable header image (blogs are themeable) full-width, 140pt
- Avatar 64pt rounded-square (8pt radius), 4pt navy ring
- Blog title 20pt w700 + bio
- "Follow" green primary + message + ⋯
- Post-count / following stat line

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `#001020` (deep navy, opaque) with a 0.5pt `#36465D` top hairline
- Tabs: Home, Search, **New post (center raised FAB)**, Activity, Account
- Icon size: 26pt
- Active: `#00B8FF`; Inactive: `#8A9AAE`
- Labels: SF Pro Text 10pt w600 (often icon-only)
- Activity tab shows a pink-red unread dot badge

**Top Nav (Dashboard)**
- Height: 48pt + safe area
- Left: Tumblr wordmark / "t" logomark (white), 24pt
- Right: messaging (22pt) + settings (22pt)
- Sits on navy; subtle hairline on scroll

### Input Fields

**Search Field**
- Background: `#13395B` with no border
- Height: 40pt, corner radius 6pt
- Leading `magnifyingglass` 16pt `#8A9AAE`
- Placeholder: "Search Tumblr", 15pt w400 `#8A9AAE`
- Focus: 1.5pt `#00B8FF` ring

**Post Composer**
- Navy sheet (`#001020`), format-aware editor (text body, photo grid, quote field, link URL)
- Tag input row at the bottom: type `#tag` chips, each becomes a pill
- "Post now / Schedule / Draft / Private" options
- Audience: public blog vs. a secondary/private blog selector

### Distinctive Components

**Heart-Burst Like**
- Tapping the like heart fills it `#FF4930` and emits 6–10 tiny hearts that scatter outward and fade over ~600ms — the iconic Tumblr delight; double-tap on media also triggers it with a centered burst
- Paired with a light impact haptic; the notes count increments

**Reblog Chain**
- The defining Tumblr structure: indented, attributed commentary stacked with a left rule — remix culture made into a component

**Tag Bar**
- The wrapping `#tag` row under posts — taps route to the tag feed; tags turn blue on press; first-class discovery metadata

**Radar Widget**
- The "Radar" recommended-post inset — a brighter card surfacing one featured post from across Tumblr

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 52
- Standard margin: 8pt horizontal (cards are near edge-to-edge on the navy gutter)
- Card rhythm: 8pt gap between dashboard posts

### Grid & Container
- Content width: full device width, 8pt card margins, media full-bleed inside cards
- Photoset grid: 2–3 column mosaic inside a post, 2pt gaps
- Reblog chain: progressive 12pt left indentation per ancestor level

### Whitespace Philosophy
- **Content-dense, edge-to-edge media**: GIFs and photosets bleed to the card edges — minimal chrome around imagery
- **Long-form breathing**: text posts get 1.5 line height for essay readability
- **Cards float on navy**: the thin 8pt navy gutter separates stacked post cards

### Border Radius Scale
- Square (0pt): Full-bleed media inside a card
- Soft (6pt): Buttons, link-preview blocks, avatars (rounded-square), search field
- Card (8pt): Dashboard post cards, larger avatars
- Comfortable (14–16pt): Bottom sheets, modals
- Circle (50%): The center New-post FAB, like-burst hearts, some inline avatars
- Note: Tumblr avatars are **rounded-squares (6–8pt)**, matching its blog-tile heritage

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Navy canvas, nav bars |
| Card (Level 1) | `rgba(0,0,0,0.3) 0 1px 4px` | Post cards lift gently off the navy |
| Radar (Level 1) | brighter `#13395B` fill, no shadow | Recommended inset distinguished by tone, not shadow |
| FAB (Level 2) | `rgba(0,184,255,0.35) 0 6px 18px` | Center New-post button |
| Sheet (Level 3) | `rgba(0,0,0,0.6) 0 -16px 48px` | Notes sheet, composer, menus |
| Burst (overlay) | no shadow, additive hearts | Like heart-burst particles over content |

**Shadow Philosophy**: On the deep navy, shadows are dark and soft but subtle — cards separate mostly through surface-tone steps (`#001935` → `#0B2A45` → `#13395B`) rather than heavy drop shadows. The brighter Radar inset uses *tone*, not elevation, to stand apart. The two real elevations are the blue center FAB and modal sheets. The most memorable "motion-as-depth" is the like burst — additive pink hearts that pop above the content layer.

### Motion
- **Heart-burst**: heart fills `#FF4930`, 6–10 small hearts scatter outward on randomized vectors and fade/scale-down over ~600ms; light haptic; notes count rolls
- **Reblog**: icon turns `#00CF35` with a quick 1.0 → 1.15 → 1.0 bounce, success haptic
- **Double-tap media**: centered large heart pop + burst over the image
- **FAB press**: scale 0.94, `#0090CC`, soft haptic; post-type picker rises 0.3s ease-out
- **Tag press**: tag flashes to `#00B8FF` then routes to the tag feed with a 0.25s push
- **Pull-to-refresh**: a playful custom spinner (Tumblr has historically used branded refresh art); fall back to system spinner

## 7. Do's and Don'ts

### Do
- Use deep navy `#001935` as the canvas with `#0B2A45` post cards — a midnight zine, not black
- Use all three accents with intent: Blue `#00B8FF` actions/links, Green `#00CF35` follow/notes, Pink-red `#FF4930` like
- Make the like a confetti heart-burst — it's Tumblr's signature delight
- Render reblogs as an indented, attributed chain with a left rule and blue source names
- Show the tag bar and notes count as first-class post metadata
- Let media (GIFs, photosets) bleed full-width inside cards with no letterboxing
- Use rounded-square avatars (6–8pt) — Tumblr's blog-tile heritage
- Support quote posts with editorial 26pt (optionally serif) weight

### Don't
- Don't use pure black or gray as the canvas — the saturated navy is the identity
- Don't flatten to a single accent — the blue/green/pink trio is Tumblr's personality
- Don't make the like a static toggle — the heart-burst is essential
- Don't collapse reblog ancestry into a quote blob — the indented chain is the structure
- Don't letterbox or heavily pad GIFs/photosets — media goes edge-to-edge in the card
- Don't make avatars circular — rounded-squares match the blog-tile lineage
- Don't over-chrome the dashboard — content (art, GIFs, essays) is the voice
- Don't drop the tag bar — tags are core Tumblr discovery

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Avatar 32pt; photoset grid 2-col |
| iPhone 13/14/15 | 390pt | Standard 36pt avatar, 8pt card margins |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the wordmark |
| iPhone 15/16 Pro Max | 430pt | Larger media; photoset can go 3-col; FAB stays 52pt |
| iPad | 768pt+ | Multi-column dashboard (masonry), max 620pt reading column for text posts |

### Dynamic Type
- Post body, blog names, quote posts: full scale (long-form — honor it)
- Notes count, timestamps, tags: scale modestly
- Tab labels: fixed 10pt
- Reblog source names: scale modestly, fixed minimum 14pt

### Orientation
- Dashboard / Blog: portrait and landscape (iPad masonry)
- Media lightbox / GIF viewer: rotates to landscape for wide media
- Composer: portrait-locked on iPhone

### Touch Targets
- Post action icons: 22pt glyph, 44pt hit area
- Center New-post FAB: 52pt
- Tab icons: 26pt glyph, 44pt effective hit
- Tags: each `#tag` minimum 32pt tappable height with hit-slop

### Safe Area Handling
- Top: wordmark and dashboard sit below safe area / Dynamic Island
- Bottom: tab bar (with raised center FAB) respects the home indicator; sheets dock above it
- Sides: 8pt card margins; media full-bleed within cards

## 9. Agent Prompt Guide

### Quick Color Reference
- Navy canvas: `#001935`
- Surface 1 (nav/wells): `#001020`
- Surface 2 (cards): `#0B2A45`
- Surface 3 (pressed/inputs): `#13395B`
- Divider / chain rule: `#36465D`
- Text primary: `#FFFFFF`
- Text secondary: `#8A9AAE`
- Tumblr Blue (action/link): `#00B8FF`
- Blue pressed: `#0090CC`
- Tumblr Green (follow/notes): `#00CF35`
- Tumblr Pink-Red (like/burst): `#FF4930`

### Example Component Prompts
- "Create a SwiftUI Tumblr dashboard post card: `#0B2A45` card on a `#001935` navy canvas, 8pt corner radius, 8pt gutter. Header: 36pt rounded-square avatar (6pt radius), blog name 'wanderlust-archive' in SF Pro 16pt weight 700, trailing green outline 'Follow' + ⋯. Full-bleed GIF media at the card top (0pt radius, no letterboxing). A wrapping tag bar of `#tags` 14pt `#8A9AAE`. Footer: '1,204 notes' 13pt weight 600 on the left, then Reply · Reblog · Like · Share 22pt on the right."
- "Build the Tumblr like heart-burst: tapping a 22pt heart fills it `#FF4930` and emits 8 small hearts that scatter outward on randomized vectors, scaling down and fading over ~600ms, with a light impact haptic and the notes count rolling up by one. Double-tapping media triggers a centered large heart-pop with the same burst."
- "Design a Tumblr reblog chain: a vertical stack of attributed segments inside the post body; each segment shows the source blog name in `#00B8FF` 14pt weight 700 at the top, then that blogger's commentary 15pt weight 400. Each ancestor level is indented with a 2pt `#36465D` left rule and 12pt left padding (deeper = older); the newest commentary is unindented at the bottom."
- "Create the Tumblr center New-post FAB: a 52pt `#00B8FF` circle raised into the center of the bottom tab bar, SF Symbol `plus` 24pt in `#001935`, shadow rgba(0,184,255,0.35) 0 6px 18px. Pressed: scale 0.94, `#0090CC`, soft haptic; opens a post-type picker (text / photo / quote / link / chat / audio / video)."
- "Build a Tumblr quote post: editorial 26pt weight 700 (optionally a serif like Georgia) quoted text on a `#0B2A45` card, an em-dash attribution '— Source' in 14pt `#8A9AAE`, then the tag bar and the notes/actions footer."

### Iteration Guide
1. Canvas is deep navy `#001935`; cards step up through `#0B2A45` → `#13395B` — never black or gray
2. Use the full accent trio: Blue `#00B8FF` actions/links, Green `#00CF35` follow/notes, Pink-red `#FF4930` like
3. The like is a confetti heart-burst — it is Tumblr's signature; never a static toggle
4. Reblogs are an indented, attributed chain with a left rule and blue source names
5. Media (GIFs, photosets) bleeds full-width inside cards — no letterboxing or heavy padding
6. Tag bar + notes count are first-class metadata under every post
7. Avatars are rounded-squares (6–8pt) — the blog-tile heritage; quote posts get 26pt editorial weight
8. Dark navy is the default; a light theme exists but the brand experience is the midnight dashboard
