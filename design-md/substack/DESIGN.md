# Design System Inspiration of Substack (iOS)

## 1. Visual Theme & Atmosphere

Substack's iOS app is built around a single belief: the writing is the product, and the writer's voice deserves a page that looks like a fine magazine, not a feed. Open any post and you land on a clean white reading surface — a serif headline set tight, an italic serif dek, a compact byline with the publication's logo and subscriber count, and then long-form body text in a generous reading serif at 18pt with 1.65 line-height. There are no cards around the prose, no boxes, no shadows in the reading column. The chrome is deliberately minimal and editorial: the only saturated color in the entire experience is **Substack Orange (`#FF6719`)**, and it is rationed almost entirely to one job — the **Subscribe** button. Substack monetizes writers, so the orange Subscribe CTA is the single most important pixel in the app and everything else recedes to let it land.

The signature screen is the **post reader**: publication row (logo tile + name + subscriber count), serif title, italic serif subtitle, byline with avatar and "X min read", body prose with inline orange-left-border pull-quotes, a sticky **Subscribe card** (often a paywall break — "Keep reading with a subscription") with the orange button and the price line, and a bottom action bar (Like ♥ in orange, Comment, Restack, Share). The second signature surface is the **Inbox**: a chronological list of new posts from every publication the reader subscribes to — a small square publication thumbnail, an uppercase orange publication name, a serif post title, byline + read-time meta, and an orange unread dot. Inbox is the home tab; it is an email-replacement reading queue, not an algorithmic feed.

The color system is intentionally sparse. White paper carries the reading experience; near-black serif ink does the words; Substack Orange is the brand and the conversion; a single link blue (`#4A6FE3`) handles inline hyperlinks; everything else is neutral grey. The typography is the design: a high-quality reading serif (Spectral / Source Serif family) for everything the reader *reads* — titles, deks, body, pull-quotes — and a clean grotesque (Inter / system) for the chrome the reader *operates* — buttons, labels, the tab bar, metadata. The app has a Reader (light) and a Dark theme; the dark theme softens the page to charcoal `#121212` but the orange never changes — it is the one constant signal.

**Key Characteristics:**
- White reading surface (`#FFFFFF`) light / soft charcoal (`#121212`) dark — a magazine page, not a feed
- Serif everything-you-read (title, dek, body, pull-quote); sans everything-you-operate (buttons, tabs, meta)
- Substack Orange (`#FF6719`) rationed almost entirely to the Subscribe CTA — the one conversion
- Long-form reading body: serif 18pt, 1.65 line-height, generous measure — built for 18-minute reads
- Sticky Subscribe card / paywall break with orange button + price line ("$8/month · $80/year")
- Inbox as home — a chronological subscription queue with orange publication labels + unread dots
- Publication row: logo tile + name + subscriber count — the publication is the brand, not Substack
- Pull-quotes with a 3pt orange left border, italic serif
- Bottom action bar: Like (orange heart) / Comment / Restack / Share
- Discover, Notes (a short-form companion feed), and Profile complete the tabs
- Restraint: one orange, one link blue, neutral greys — no decorative color anywhere

## 2. Color Palette & Roles

### Primary (Brand & Interactive)
- **Substack Orange** (`#FF6719`): The single brand + conversion color — Subscribe button, active tab, unread dot, liked heart, publication label, pull-quote border. Held identical light AND dark.
- **Orange Pressed** (`#E5560E`): Pressed state of the Subscribe button and orange controls.
- **Orange Tint Surface** (`#FFF7F2` light / `#2A1C12` dark): Subscribe-card / paywall background wash.
- **Orange Tint Border** (`#FFD9C2` light / `#5C3A24` dark): Subscribe-card hairline.
- **Link Blue** (`#4A6FE3`): Inline hyperlinks in body prose only — the one non-orange accent.

### Reading Surface (Light — "Reader" theme)
- **Paper White** (`#FFFFFF`): The reading page.
- **Reading Ink** (`#1F1F1F`): Body prose — near-black, warm-neutral, not pure black.
- **Title Ink** (`#1A1A1A`): Titles and headers.
- **Subhead Ink** (`#57534E`): Dek / subtitle, warm grey.
- **Meta Grey** (`#8A8A8A`): Byline meta, captions.
- **Hairline** (`#ECECEC`): Byline and section rules.

### App Surfaces (Light)
- **Surface Subtle** (`#F7F6F4`): Search field, grouped rows.
- **Divider** (`#E7E5E1`): List dividers.

### App Surfaces (Dark)
- **Dark Canvas** (`#121212`): Reading page + app background in dark — soft charcoal, NOT pure black.
- **Dark Surface 1** (`#1B1B1B`): Cards, the Subscribe card base, rows.
- **Dark Surface 2** (`#262626`): Pressed rows, search field.
- **Dark Divider** (`#2E2E2E`): Hairline rules on dark.

### Text
- **Text Primary Light** (`#1A1A1A`): Chrome labels, titles.
- **Text Secondary Light** (`#6B6B6B`): Metadata, captions.
- **Text Primary Dark** (`#EDEDED`): Body + chrome on dark.
- **Text Secondary Dark** (`#A6A6A6`): Secondary text on dark.
- **Text Tertiary Dark** (`#6E6E6E`): Disabled / overline on dark.

### Semantic
- **Error Red** (`#D93025`): Unsubscribe confirm, payment error.
- **Success Green** (`#1A8917`): "Subscribed" / saved toast.
- **Like Orange** (`#FF6719`): Liked-heart fill (reuses brand orange).
- **Warning Amber** (`#E5560E`): Non-blocking warnings (reuses pressed orange).

## 3. Typography Rules

### Font Family
- **Reading Serif**: Substack sets long-form in a high-quality reading serif. Use **`Source Serif 4`** (Frank Grießhammer / Adobe, SIL OFL — an optical-size transitional serif) as the reference face for titles, deks, body, and pull-quotes; `Spectral` (Production Type, SIL OFL) is an equally faithful alternative. The reading face is the brand's editorial voice.
- **UI / Chrome**: **`Inter`** (Rasmus Andersson, SIL OFL) — a neutral humanist grotesque; used for buttons, the tab bar, labels, byline meta, and all chrome.
- **iOS Fallback**: serif → `Georgia` / `'Times New Roman'`; sans → `-apple-system, 'SF Pro Text'`.
- **Reader font/size is user-adjustable**: the reader can pick serif/sans + text size in reading settings — respect it globally on the post body.
- **Rationale**: Substack competes with magazines and email newsletters — body must read like editorial print; chrome must be invisible and neutral so the orange Subscribe is the only thing that pops.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Source Serif 4 | 30pt | 700 | 1.2 | -0.4pt | "Inbox", "Discover" large nav title |
| Post Title | Source Serif 4 | 26pt | 700 | 1.22 | -0.3pt | The hero post headline |
| Section Heading (in post) | Source Serif 4 | 22pt | 700 | 1.3 | -0.2pt | H2 within a post body |
| Pull Quote | Source Serif 4 | 21pt | 600 italic | 1.4 | 0pt | Orange-left-border quote |
| Reading Body | Source Serif 4 | 18pt | 400 | 1.65 | 0pt | The long-form prose — user-scalable |
| Subtitle / Dek | Source Serif 4 | 17pt | 400 italic | 1.4 | 0pt | The post's italic deck under the title |
| Button | Inter | 15pt | 700 | 1.0 | 0pt | "Subscribe", primary CTAs |
| Label / Publication | Inter | 14pt | 600 | 1.3 | 0pt | Publication name, list labels |
| Metadata | Inter | 13pt | 400 | 1.4 | 0pt | "Mario Gabriele · 18 min read · May 14" |
| Caption | Inter | 12pt | 400 | 1.4 | 0pt | Subscriber count, footnote |
| Publication Eyebrow | Inter | 11pt | 700 | 1.0 | 0.4pt | UPPERCASE publication label in inbox |
| Overline | Inter | 11pt | 700 | 1.0 | 0.8pt | "FROM THE GENERALIST" all-caps |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |

### Principles
- **Serif reads, sans operates**: anything the reader *reads* (title, dek, body, pull-quote, section heads) is the serif; anything the reader *acts on* (buttons, tabs, labels, meta) is Inter.
- **Long-form first**: body is 18pt at 1.65 line-height with a generous measure — designed for sustained 15–20 minute reads, not skimming.
- **The dek is italic serif**: the subtitle/deck under a title is italic — an editorial convention that signals "magazine".
- **Numerals are sans**: subscriber counts, prices, read-times use Inter tabular figures.
- **Reader controls size**: the post body must honor the user's chosen reading text size and face globally.
- **Dynamic Type first-class**: titles, deks, body, section heads scale; tab labels, eyebrows, captions stay fixed.

## 4. Component Stylings

### Buttons

**Subscribe Button (the conversion)**
- Shape: Rounded rectangle, 6pt corner radius, full content width in cards
- Height: 42–48pt
- Background: `#FF6719` (Substack Orange)
- Text: `#FFFFFF`, Inter 15pt weight 700
- Pressed: background `#E5560E` + scale 0.98
- This is the single most important element in the app — it appears once per post (in the sticky/paywall card) and on publication pages; never duplicated as decoration

**Subscribed / Outline State**
- Background: transparent
- Border: 1pt `#6B6B6B` light / `#A6A6A6` dark
- Text: `#1A1A1A` light / `#EDEDED` dark with a leading check, Inter 14pt weight 600
- Corner radius: 6pt
- Pressed: background `#F7F6F4` / `#262626`

**Ghost Button ("Share", secondary)**
- Background: `#F7F6F4` / `#262626`
- Border: 1pt divider
- Text: Inter 13pt weight 600
- Corner radius: 6pt

**Text / Link Button ("Continue reading", "See all")**
- Font: Inter 14pt weight 600
- Color: `#4A6FE3`
- No underline

### Post Reader (the core surface)

The reading view is Substack's fundamental surface.
- Background: `#FFFFFF` (Reader) / `#121212` (Dark)
- Publication row: 34pt logo tile (6pt radius) + name (Inter 14pt/600) + "X subscribers" (12pt grey)
- Title: serif 26pt/700, tight leading, -0.3pt tracking
- Dek: italic serif 17pt/400, `#57534E`
- Byline: 26pt avatar + "Author · Date · X min read" (Inter 13pt grey), with a 0.5pt hairline below
- Body: serif 18pt/400, 1.65 line-height, ink `#1F1F1F`; inline links `#4A6FE3` underlined
- Pull-quote: serif 21pt/600 italic, 3pt `#FF6719` left border, 18pt left padding
- Images: full-bleed-to-measure, optional italic serif caption 13pt grey
- Section headings: serif 22pt/700
- No cards/boxes/shadows in the reading column — prose flows on bare paper

### Subscribe Card / Paywall Break
- Background: `#FFF7F2` (orange tint) / `#2A1C12` dark
- Border: 1pt `#FFD9C2` / `#5C3A24`, 10pt corner radius, 14pt padding, centered
- Heading: serif 15pt/700 ("Keep reading with a subscription")
- Subtext: Inter 12pt grey
- Orange Subscribe button (full width, 42pt, 6pt radius)
- Price line: Inter 11pt grey ("$8/month · $80/year · cancel anytime")
- Often inserted mid-post as the paywall cut (free preview above, blurred/locked below)

### Inbox Row
- Layout: 56pt square publication thumbnail (6pt radius) + content column + unread dot
- Publication eyebrow: Inter 11pt/700 UPPERCASE `#FF6719`, 0.4pt tracking
- Post title: serif 15pt/700, 1.3 line-height, 2-line clamp
- Meta: Inter 12pt `#6E6E6E` ("Author · X min · date")
- Unread dot: 8pt circle `#FF6719`
- Row divider: 1pt divider; full-row tap → post reader
- Inbox is the home tab — chronological, NOT algorithmic

### Navigation

**Post Top Bar**
- Height: 44pt + safe area
- Leading: back chevron
- Trailing: Share icon + ⋯ overflow (Mute publication, Copy link, Report)
- Background: paper/charcoal, 0.5pt bottom hairline; the publication name can appear centered on scroll

**Bottom Tab Bar**
- Height: 50pt + safe area
- 4 tabs: Inbox, Discover, Notes, Profile
- Icon size: 22pt; labels Inter 10pt weight 600
- Active: `#FF6719` (filled glyph); inactive: `#9A9A9A` / `#6E6E6E`
- No tint pill — active is glyph fill + orange
- Background: paper / charcoal, 0.5pt top hairline

### Input Fields

**Search (Discover)**
- Height: 40pt
- Background: `#F7F6F4` / `#262626`
- Corner radius: 8pt
- Leading `magnifyingglass` 16pt grey
- Placeholder: "Search Substack" Inter 14pt grey
- Focus: 1.5pt `#FF6719` border

**Comment Composer**
- Bottom sheet; multiline Inter 15pt input (comments are sans, not serif — they are chrome/conversation, not the article)
- "Post" orange button in the sheet header
- Threaded replies indented 24pt

### Distinctive Components

**Publication Header (Publication page)**
- Cover band + large logo + publication name (serif 26pt) + tagline (italic serif) + subscriber count
- A prominent orange Subscribe button + "X posts" / "About" tabs

**Sticky Subscribe / Paywall Cut**
- The free preview fades into a locked section behind the Subscribe card — the central monetization moment

**Pull-Quote**
- Italic serif with the signature 3pt orange left rule — the most recognizable in-post element

**Action Bar (post footer)**
- Like (orange heart, count), Comment (count), Restack (Notes share), Share
- Like fills orange with a heart-pop on tap

**Notes Card (Notes tab)**
- Short-form: avatar + name + handle + short text + Like/Reply/Restack — a Twitter-like companion feed, still neutral with orange accents

**Subscription Tier Selector**
- Free / Monthly / Annual cards; the recommended tier ringed in orange with an orange-tint background

**Unread Dot**
- 8pt orange dot on inbox rows and on the Inbox tab badge — the "you have new reading" signal

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48
- Reading content inset: 24pt horizontal (a comfortable editorial measure)
- App/chrome inset: 16pt horizontal
- Paragraph spacing: 18pt between body paragraphs

### Grid & Container
- iPhone reader: single column, 24pt insets (measure ≈ 60–66 chars)
- iPhone inbox: single list, 16pt insets, 56pt thumbnails
- iPad reader: centered column, max measure ≈ 680pt
- iPad inbox: single wide list or two-column on landscape

### Whitespace Philosophy
- **The page breathes like print**: 24pt measure + 1.65 leading + 18pt paragraph spacing — sustained reading comfort over density
- **Chrome is sparse**: lists are simple rows with hairlines; no decorative containers
- **One color**: orange appears for Subscribe / active / unread / like and nothing else; the rest is paper + ink + grey
- **The Subscribe card is the only tinted block**: a single warm-orange wash that draws the eye to the conversion

### Border Radius Scale
- Square (0pt): The reading column, full-bleed images, hairlines
- Subtle (4pt): Inline chips, small tags
- Standard (6pt): Subscribe button, ghost buttons, search field
- Card (8–10pt): Subscribe card, tier cards, inbox thumbnails
- Comfortable (16pt): Bottom sheets, modals
- Pill (500pt): Filter chips
- Circle (50%): Avatars, unread dot

## 6. Depth & Elevation

Substack is nearly flat — the reading column has zero elevation, and shadow appears only on floating sheets and (lightly) on the Subscribe card.

| Level | Treatment | Use |
|-------|-----------|-----|
| Page (Level 0) | No shadow, no border | Reading column, inbox rows, prose |
| Card (Level 1) | `rgba(255,103,25,0.06)` fill + 1pt orange-tint border (no shadow) | Subscribe card, tier cards — color, not depth |
| Sticky (Level 2) | `rgba(0,0,0,0.06) 0 -2px 12px` | The sticky Subscribe bar over the page bottom |
| Sheet (Level 3) | `rgba(0,0,0,0.16) 0 -4px 24px` | Comment composer, share sheet, settings |
| Scrim | `rgba(0,0,0,0.3)` | Behind sheets and modals |

**Shadow Philosophy**: The reading surface must look like a printed page — flat, matte, no shadow. The Subscribe card stands out by *color* (warm orange tint + thin orange border), not by elevation, keeping the editorial calm. Only transient floating things (the sticky bar, sheets) get a faint shadow as a "this floats" cue. On dark mode shadows are nearly invisible, so the Subscribe card relies entirely on its orange-tint border. Chrome cards never carry drop shadow.

### Motion
- **Subscribe tap**: orange button does a 1.0→0.97→1.0 spring (180ms); on success, the button morphs to "Subscribed ✓" with a 220ms crossfade + soft success haptic
- **Like (heart)**: heart fills orange with a 1.0→1.3→1.0 pop (220ms spring) + soft haptic; count ticks up
- **Paywall reveal**: scrolling into the paywall fades the locked text to a blur over 250ms; the Subscribe card slides up 250ms ease-out and sticks
- **Inbox → post**: iOS native push, 300ms; the publication thumbnail can shared-element into the post header
- **Pull to refresh (Inbox)**: orange spinner, standard iOS rubber-band
- **Tab switch**: instant; selected glyph fills with a 120ms crossfade; unread dot animates in with a small scale
- **Notes compose**: bottom sheet slides up 280ms ease-out
- **Read progress**: a thin orange progress line at the very top edge fills as the reader scrolls the post
- **Haptic feedback**: soft impact on Subscribe success and Like; light on pull-to-refresh trigger
- **Restack**: a small toast slides up ("Restacked to Notes") 250ms; no modal

## 7. Do's and Don'ts

### Do
- Make the post reader the product — a clean white page, serif prose, no boxes
- Set everything-you-read in a reading serif (Source Serif 4 / Spectral); everything-you-operate in Inter
- Body at 18pt with 1.65 line-height and a ~24pt editorial measure — built for long reads
- Make the dek/subtitle italic serif — the editorial-magazine signal
- Ration Substack Orange (`#FF6719`) to the Subscribe CTA, active tab, unread dot, liked heart, pull-quote rule — nothing else
- Use the orange-tint Subscribe card as the one tinted block; stand it out by color, not shadow
- Make Inbox the home tab — chronological subscription queue, not an algorithm
- Use the 3pt orange left border for pull-quotes
- Keep the Subscribe button a single instance per post (the sticky/paywall card)
- Hold the orange identical in light and dark — it is the one constant signal

### Don't
- Don't put cards, borders, or shadows around the reading prose — it flows on bare paper
- Don't use pure black for body ink — warm near-black `#1F1F1F` is correct
- Don't use pure black for dark mode — soft charcoal `#121212`
- Don't set the post body in a UI sans — the article is serif
- Don't add a second accent color — orange + one link blue + neutral greys only
- Don't make comments serif — comments/Notes are conversation chrome, set in Inter
- Don't scatter orange decoratively — it must mean "subscribe / active / new / liked"
- Don't make Inbox algorithmic — it is a chronological reading queue (email replacement)
- Don't duplicate the Subscribe button as decoration — one per post, where conversion happens
- Don't animate the reading column decoratively — quiet motion; the read-progress line is the only ambient motion
- Don't add a Material tint pill to the active tab — it is glyph fill + orange

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Reading inset 20pt; inbox thumbnails 52pt |
| iPhone 13/14/15 | 390pt | Standard layout; reading inset 24pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected by post top bar |
| iPhone 15/16 Pro Max | 430pt | Wider measure; larger title |
| iPad (portrait) | 768pt | Centered reading column max ≈ 680pt; inbox single wide list |
| iPad (landscape) | 1024pt+ | Two-column: inbox list + post reader |

### Dynamic Type
- Post title, dek, body, section heads, pull-quotes: full scale, plus the in-app reading-size control as an additional multiplier
- Tab labels, publication eyebrow, captions, overlines: FIXED (layout-sensitive)
- Inbox post titles scale modestly with body text

### Orientation
- Reader rotates; iPad landscape shows inbox + reader side by side
- Inbox reflows; thumbnails stay fixed
- Sheets and the Subscribe card re-anchor on rotation

### Touch Targets
- Subscribe button: 44pt tall minimum (the most important target — never smaller)
- Action-bar items (Like/Comment/Restack/Share): 44pt hit
- Inbox row: full-row tap, 72pt+ tall
- Tab bar icons: 22pt glyph, 49pt hit

### Safe Area Handling
- Reader: prose respects safe area; the read-progress line hugs the very top safe-area edge
- Top bar respects Dynamic Island
- Bottom: tab bar + home indicator respected; the sticky Subscribe bar sits above the home indicator
- Sides: 24pt reading inset, 16pt chrome inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Reading page: `#FFFFFF` light / `#121212` dark
- Body ink: `#1F1F1F` light / `#EDEDED` dark
- Title ink: `#1A1A1A` light / `#EDEDED` dark
- Dek / subhead: `#57534E` light / `#A6A6A6` dark
- Brand + conversion (Subscribe / active / unread / like): `#FF6719` (pressed `#E5560E`)
- Subscribe-card wash: `#FFF7F2` light / `#2A1C12` dark; border `#FFD9C2` / `#5C3A24`
- Link blue (inline only): `#4A6FE3`
- App surface: `#F7F6F4` light / `#1B1B1B`–`#262626` dark
- Hairline: `#ECECEC` light / `#2E2E2E` dark
- Text secondary: `#6B6B6B` light / `#A6A6A6` dark
- Error: `#D93025`

### Example Component Prompts
- "Build the Substack post reader in SwiftUI: background `#FFFFFF` (or `#121212` dark). A publication row — 34pt logo tile (6pt radius, orange gradient), name in Inter 14pt weight 600, 'Mario Gabriele · 312,400 subscribers' in Inter 12pt `#8A8A8A`. Title in Source Serif 4 26pt weight 700 `#1A1A1A` tracking -0.3pt; dek in Source Serif 4 italic 17pt `#57534E`; byline (26pt avatar + 'Author · May 14 · 18 min read' Inter 13pt grey) with a 0.5pt `#ECECEC` hairline below. Body in Source Serif 4 18pt weight 400, line-height 1.65, ink `#1F1F1F`, inline links `#4A6FE3` underlined."

- "Create the Substack Subscribe card / paywall break: centered card, background `#FFF7F2` (dark `#2A1C12`), 1pt `#FFD9C2` border, 10pt radius, 14pt padding. Heading 'Keep reading with a subscription' in Source Serif 4 15pt weight 700; subtext Inter 12pt `#8A8A8A`; a full-width 42pt Subscribe button, 6pt radius, background `#FF6719`, label Inter 15pt weight 700 white; price line 'Β$8/month · $80/year · cancel anytime' in Inter 11pt `#A0A0A0`. On tap: button springs 1.0→0.97→1.0 (180ms), then crossfades to 'Subscribed ✓' with a soft success haptic."

- "Render a Substack pull-quote: Source Serif 4 21pt weight 600 italic `#1A1A1A`, a 3pt `#FF6719` left border, 18pt left padding, 6pt top/bottom padding, 20pt bottom margin. No background, no box."

- "Build a Substack inbox row: 56pt square publication thumbnail (6pt radius) + content column + trailing 8pt `#FF6719` unread dot. Eyebrow = publication name in Inter 11pt weight 700 UPPERCASE `#FF6719` 0.4pt tracking; post title in Source Serif 4 15pt weight 700 2-line clamp; meta 'Author · 18 min · May 14' in Inter 12pt `#6E6E6E`. 1pt divider; full-row tap pushes the post."

- "Render the Substack bottom action bar: Like (heart, count) / Comment (count) / Restack / Share, Inter 13pt weight 600 `#57534E`. Liked = heart filled `#FF6719` with a 1.0→1.3→1.0 pop (220ms spring) + soft haptic and the count incremented."

- "Build the Substack bottom tab bar: 4 tabs Inbox / Discover / Notes / Profile, 22pt glyphs, Inter 10pt weight 600 labels. Active = filled glyph `#FF6719`; inactive = outline glyph `#9A9A9A` (dark `#6E6E6E`). No tint pill — color + fill only. Paper `#FFFFFF` / charcoal `#121212` background, 0.5pt top hairline `#ECECEC` / `#2E2E2E`. The Inbox tab shows an orange unread dot badge when new posts arrive."

### Iteration Guide
1. The post reader is the product — a clean white page (`#FFFFFF` / dark `#121212`), serif prose, zero boxes
2. Serif for everything-you-read (title/dek/body/pull-quote); Inter for everything-you-operate (buttons/tabs/meta)
3. Body is serif 18pt at 1.65 line-height with a ~24pt editorial measure — built for 15–20 minute reads
4. The dek/subtitle is italic serif — the magazine signal
5. Substack Orange `#FF6719` is rationed to Subscribe / active tab / unread dot / liked heart / pull-quote rule — nothing else
6. The Subscribe card is the one tinted block; it stands out by warm-orange color, never by shadow
7. Inbox is the home tab — a chronological subscription queue, never algorithmic
8. The reading column is flat — no cards, borders, or shadows; only sheets and the sticky bar float
9. One link blue `#4A6FE3` for inline links is the only non-orange accent; everything else is paper + ink + grey
10. Hold the orange identical in light and dark — it is the single constant conversion signal
