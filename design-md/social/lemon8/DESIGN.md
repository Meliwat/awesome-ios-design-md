# Design System Inspiration of Lemon8 (iOS)

## 1. Visual Theme & Atmosphere

Lemon8's iOS app is a digital lifestyle magazine. Where most social apps default to a single-column feed of full-width posts, Lemon8 opens to a **two-column staggered masonry grid** of richly styled lifestyle cards — a layout that reads like flipping through the discovery board of a glossy magazine crossed with Pinterest and Xiaohongshu (RED). The canvas is bright editorial white (`#FFFFFF`), generous with whitespace, and every card is a small magazine cover: a tall lifestyle photo, a punchy 1-2 line title, pastel topic tags, and a tiny creator byline with a like count. The aesthetic is aspirational, warm, and content-dense without feeling cluttered — the masonry's varied heights create a pleasing editorial rhythm as you scroll.

The signature surface is the **discovery masonry feed**. Cards break across two columns with uneven heights (because cover images vary from 1:1 to 3:4 to 4:5), 10pt column gaps, and 12pt vertical gaps. Each card has a 14pt corner radius and a very soft elevation (`0 1px 3px` + `0 4px 12px` at low opacity) so it lifts gently off the white. Tapping a card opens the **post detail**: a swipeable photo carousel at the top (with a "5 photos" pill), then the title in Poppins, a rich body of styled text with inline subheadings and embedded photos, the pastel tag row, the creator card with a Follow button, and a comment thread — structured like a magazine article, not a tweet.

The brand color is **Lemon8 Yellow** (`#FFE600`) — but it is used with deliberate restraint. It is NOT a flood color. Yellow appears only on: the post-compose FAB (a rounded yellow rectangle in the tab bar), the active top-tab underline, the primary CTA (Post / Follow), and small accent moments. Critically, Lemon8 Yellow is *always* paired with ink text (`#1A1A1A`) — never white-on-yellow (the contrast would fail and it's off-brand). The workhorse of the system is actually the ink-on-white editorial type and the **pastel topic-tag palette**: mint (`#E4F5EC`/`#1F8A52`), blush (`#FCE8EE`/`#C13E68`), sky (`#E6F0FB`/`#2D6FB8`), butter (`#FFF6CC`/`#8A7300`). Every card carries 1-3 of these soft category tags; they are the color rhythm of the entire app.

Typography pairs a friendly geometric display face with a clean UI sans. **Poppins** (Google Fonts, geometric, rounded) carries display titles, screen headers, tab headers, and post titles — giving Lemon8 its approachable magazine-headline voice. **Inter** handles card titles, body copy, metadata, tags, and tab labels. Hierarchy is editorial: a 32pt display, 18pt post titles, 15pt card titles, 13pt creator metadata, 11pt tags.

**Key Characteristics:**
- Bright editorial white canvas (`#FFFFFF`) — a digital lifestyle magazine, generous whitespace
- Two-column staggered masonry feed — varied card heights, 10pt column gap, 12pt vertical gap
- Lifestyle cards as mini magazine covers — tall photo + punchy title + pastel tags + creator byline
- Lemon8 Yellow (`#FFE600`) used sparingly — post FAB, active tab underline, primary CTA — always with ink text
- Pastel topic-tag palette — mint / blush / sky / butter; every card carries 1-3 category tags
- Post detail as a magazine article — photo carousel + styled body with subheadings + embedded photos
- Poppins display headlines + Inter UI/body
- Soft card elevation (`0 1px 3px` + `0 4px 12px` low-opacity) — cards lift gently off white
- Top segmented tabs — Following / For You / Nearby with a small yellow underline
- Photo-overlay chips ("5 photos", "Guide") in translucent dark, bottom-left of cover
- Creator-centric — bylines, Follow buttons, like counts on every card
- Light-first identity — dark mode exists but the magazine-white aesthetic is core

## 2. Color Palette & Roles

### Primary (Brand — used sparingly)
- **Lemon8 Yellow** (`#FFE600`): Post-compose FAB, active top-tab underline, primary CTA (Post / Follow). ALWAYS paired with ink text `#1A1A1A`.
- **Lemon8 Yellow Pressed** (`#E6CF00`): Pressed state for yellow controls.
- **Ink** (`#1A1A1A`): Primary text, primary dark buttons (Save), the partner color to yellow.

### Canvas & Surfaces (Light Mode — core identity)
- **Canvas White** (`#FFFFFF`): Primary canvas, card background.
- **Surface 1** (`#FAFAFA`): Grouped settings, subtle sections.
- **Surface 2** (`#F2F2F2`): Search field, pressed rows, skeleton placeholders.
- **Divider** (`#ECECEC`): Hairline dividers between sections and comment rows.

### Canvas & Surfaces (Dark Mode — secondary)
- **Dark Canvas** (`#121212`): Primary dark canvas.
- **Dark Surface 1** (`#1C1C1E`): Card background on dark.
- **Dark Surface 2** (`#2A2A2A`): Search field, pressed rows on dark.
- **Dark Divider** (`#2E2E2E`): Hairline dividers on dark.

### Text
- **Ink / Text Primary** (`#1A1A1A`): Titles, card titles, primary text.
- **Text Secondary** (`#6B6B6B`): Creator names, like counts, metadata.
- **Text Tertiary** (`#9A9A9A`): Inactive tabs, timestamps, placeholders.
- **Text Primary Dark** (`#F2F2F2`): Primary text on dark.
- **Text Secondary Dark** (`#A0A0A0`): Metadata on dark.

### Pastel Topic Tags (the color rhythm)
Each card carries 1-3 category tags. Background is the soft pastel; text is the saturated sibling.

| Tag | Light Background | Light Text | Dark Background | Dark Text |
|-----|------------------|-----------|-----------------|-----------|
| Mint (Recipe/Food) | `#E4F5EC` | `#1F8A52` | `#1F3A2C` | `#6FD49E` |
| Blush (Beauty/Fashion) | `#FCE8EE` | `#C13E68` | `#3A2630` | `#F09BB6` |
| Sky (Travel/Wellness) | `#E6F0FB` | `#2D6FB8` | `#1F2F42` | `#8FBEE8` |
| Butter (Home/Lifestyle) | `#FFF6CC` | `#8A7300` | `#3A350F` | `#E6CF66` |
| Lavender (Hobbies) | `#F0EAFB` | `#7050B0` | `#2E263C` | `#C0A8E8` |

### Semantic
- **Like Red** (`#FF2E63`): Liked-heart fill, like count when liked.
- **Success Green** (`#1FB877`): Posted / saved confirmation toast.
- **Error Red** (`#FF3B5C`): Upload failed, destructive confirmation.
- **Photo Chip Scrim** (`rgba(0,0,0,0.55)`): Background of "5 photos" / "Guide" chips on covers.

## 3. Typography Rules

### Font Family
- **Display**: `Poppins` (by Indian Type Foundry, Google Fonts, SIL OFL) — geometric, friendly, rounded; display titles, screen headers, tab headers, post titles. Gives Lemon8 its approachable magazine-headline voice.
- **UI / Body**: `Inter` (by Rasmus Andersson, SIL OFL) — card titles, body copy, metadata, tags, tab labels.
- **Fallback Stack (display)**: `'Poppins', -apple-system, BlinkMacSystemFont, 'SF Pro Display', 'Segoe UI', sans-serif`
- **Fallback Stack (UI)**: `'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Poppins | 32pt | 800 | 1.2 | -0.5pt | Large screen header / hero |
| Screen Title | Poppins | 26pt | 700 | 1.25 | -0.3pt | "Discover", "Profile" |
| Tab Header | Poppins | 22pt | 700 | 1.3 | -0.3pt | Active segmented top tab |
| Post Title | Poppins | 18pt | 600 | 1.3 | -0.2pt | Title on post detail |
| Body | Inter | 16pt | 400 | 1.55 | 0pt | Post article body |
| Subheading (in body) | Poppins | 17pt | 600 | 1.35 | -0.1pt | Section headings inside an article |
| Card Title | Inter | 15pt | 600 | 1.35 | 0pt | Masonry card title (often 13pt on phone) |
| Button | Inter | 15pt | 700 | 1.0 | 0pt | Primary / dark buttons |
| Meta | Inter | 13pt | 400 | 1.4 | 0pt | "mira.styles · 2.4k likes · 3d" |
| Card Byline | Inter | 11pt | 500 | 1.3 | 0pt | Creator name + like count on card |
| Tag | Inter | 11pt | 600 | 1.0 | 0.1pt | Pastel topic tag label (9pt on card) |
| Tab Label | Inter | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Caption | Inter | 12pt | 400 | 1.4 | 0pt | Timestamps, small print |

### Principles
- **Poppins for headlines, Inter for everything else**: Poppins is the magazine voice (titles, headers); Inter is the readable workhorse (body, cards, meta, tags). Never set body copy in Poppins.
- **Card titles are tight and punchy**: 1-2 lines, 15pt (13pt on phone) w600, line-clamp at 2 — they're cover-line teasers, not full sentences.
- **Article body breathes**: post detail body is Inter 16pt w400 at 1.55 line height with Poppins subheadings — reads like an edited magazine feature.
- **Tags are small and quiet**: pastel-on-pastel, 9-11pt w600 — they categorize without shouting.
- **Dynamic Type first-class**: display, titles, body scale; card bylines, tags, tab labels stay fixed (layout-sensitive in the masonry).

## 4. Component Stylings

### Buttons

**Primary Button (Post / Follow / primary CTA)**
- Shape: Fully rounded pill (500pt corner radius)
- Background: `#FFE600` (Lemon8 Yellow)
- Text: `#1A1A1A` (ink — NEVER white), Inter 15pt weight 700
- Padding: 13pt vertical, 28pt horizontal
- Pressed: background `#E6CF00` + scale 0.98
- Disabled: `#FFE600` at 40% opacity, text `#9A9A9A`

**Dark Button (Save / secondary primary)**
- Background: `#1A1A1A` (ink)
- Text: `#FFFFFF`, Inter 15pt weight 700
- Pill (500pt), padding 13pt / 28pt
- Pressed: `#000000`

**Outline Button (Share / Cancel)**
- Background: transparent
- Text: Ink (`#1A1A1A`) light / `#F2F2F2` dark
- Border: 1pt `#D8D8D8` light / `#404040` dark
- Pill (500pt), padding 11pt / 22pt
- Font: Inter 14pt weight 600

**Follow Pill (on card / creator)**
- Background: `#FFE600`, pill, text `#1A1A1A` Inter 12pt w700, padding 7pt / 18pt
- Following state: outline — transparent bg, 1pt `#D8D8D8` border, text `#6B6B6B`

**Text Link Button**
- Font: Inter 14pt weight 600, color Ink `#1A1A1A`
- No underline; pressed: 60% opacity

### Core Atoms

**Lifestyle Card (masonry cell)**
- Background: `#FFFFFF` (card), 14pt corner radius
- Cover image: full-width, native-ish aspect (1:1 / 3:4 / 4:5), top corners follow card radius
- Photo chip (optional): bottom-left, `rgba(0,0,0,0.55)` bg, white 10pt w600, 6pt radius, 4pt/8pt padding, `blur(4px)` backdrop ("5 photos", "Guide")
- Body: 10-11pt padding
  - Title: Inter 13-15pt w600 `#1A1A1A`, line-clamp 2
  - Tag row: pastel tags, 5pt gap, 7pt top margin
  - Byline row: 18pt circular avatar + creator name (Inter 11pt `#6B6B6B`) + like heart + count (right)
- Elevation: `0 1px 3px rgba(0,0,0,0.06)` + `0 4px 12px rgba(0,0,0,0.04)`
- Press: scale 0.98 + slight shadow lift

**Pastel Topic Tag**
- Pill-ish rounded rect, 8pt corner radius (5pt on card)
- Background: pastel (mint/blush/sky/butter/lavender); Text: saturated sibling, Inter 11pt w600 (9pt on card)
- Padding: 7pt/14pt (3pt/7pt on card)
- Tappable -> topic feed

**Avatar**
- Circle: 18pt on card byline, 24pt in comments, 56pt on profile/creator card
- Fallback: linear-gradient(135deg, `#FFE600`, `#FF2E63`) with initials

**Like Heart**
- Inactive: outline, `#6B6B6B` 2pt stroke
- Active: filled `#FF2E63`, count turns `#FF2E63`
- Tap: scale 1.0 → 1.2 → 1.0 over 260ms spring + soft haptic

**Photo Carousel (post detail)**
- Full-width, swipeable, native aspect; page dots bottom-center (active dot ink, inactive `rgba(0,0,0,0.25)`)
- Top-right counter chip "1/8" in `rgba(0,0,0,0.55)`

### Navigation

**Top Segmented Tabs**
- Row: "Following", "For You", "Nearby" — Poppins 18-22pt
- Active: ink (`#1A1A1A`) w700 + a small (20pt wide, 3pt tall) `#FFE600` underline centered below, 2pt radius
- Inactive: `#9A9A9A` w600
- Trailing search glyph (22pt ink stroke)

**Bottom Tab Bar**
- Height: 54pt + safe area
- Background: `rgba(255,255,255,0.96)` with `saturate(180%) blur(20px)`, 0.5pt top border `#ECECEC`
- Tabs (5): Home, Discover, **Post** (center — yellow rounded rectangle, not an icon-only tab), Inbox, Me
- Post FAB: 42×30pt `#FFE600` rounded rect (9pt radius) with an ink `+` glyph
- Icon size: 23pt; Active: ink fill; Inactive: `#9A9A9A`
- Labels: Inter 10pt w500 (Post has no label)

**Top Nav (Post detail)**
- Height: 44pt + safe area, translucent
- Leading: back chevron; Trailing: share + ⋯ overflow
- Creator avatar + name + Follow pill can pin to the nav on scroll

### Input Fields

**Search Bar**
- Height: 40pt, background `#F2F2F2` / `#2A2A2A` dark, 20pt radius (capsule)
- Leading magnifyingglass 16pt `#9A9A9A`
- Placeholder "Search recipes, outfits, places" Inter 14pt `#9A9A9A`
- Focus: 1.5pt ink border

**Comment Composer**
- Height 44pt min, `#F2F2F2` bg, 22pt radius capsule
- Trailing "Post" text in ink (disabled `#9A9A9A` when empty)

### Distinctive Components

**Discovery Masonry Feed**
- The signature layout. Two columns, balanced by shortest-column placement. Column gap 10pt, card vertical gap 12pt.
- Each card a lifestyle mini-cover; infinite scroll; new cards drop into the shorter column
- Pull-to-refresh: yellow lemon-slice spinner

**Post Detail (magazine article)**
- Photo carousel -> Poppins 18pt title -> creator card (avatar + name + Follow) -> styled body (Inter 16pt body, Poppins 17pt subheadings, embedded full-width photos at 14pt radius, inline pastel tags) -> tag row -> like/save/share action bar -> comments

**Creator Card (in post / profile)**
- 56pt avatar + name (Poppins 17pt w600) + follower count (Inter 13pt `#6B6B6B`) + Follow pill
- Optional 1-line bio

**Topic Hub Header**
- A pastel-tinted banner (the topic's tag color at full pastel) with the topic name in Poppins 26pt and a post count

**Photo Overlay Chip**
- "5 photos" / "Guide" / "Recipe" — `rgba(0,0,0,0.55)` bg, white Inter 10pt w600, 6pt radius, bottom-left of cover

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 5 (card tag gap), 8, 10 (masonry column gap), 12, 16, 20, 24, 32
- Masonry column gap: **10pt**; card vertical gap: **12pt**
- Card inner padding: 10-11pt
- Screen horizontal inset: 10pt (feed) / 16pt (text content)

### Grid & Container
- Feed: 2-column masonry, ~10pt outer + 10pt column gap (phone)
- Post detail: photo carousel full-bleed; text content 16pt insets
- iPad: 3-4 column masonry; post detail in centered 680pt column
- Cards never exceed ~50% screen width minus gaps on phone

### Whitespace Philosophy
- **Editorial generosity**: white is the magazine page — cards float on it with soft shadows, never edge-to-edge
- **Masonry rhythm**: varied card heights create a flowing, browsable cadence — the whitespace between cards is intentional
- **Article breathing room**: post detail body uses 1.55 line height and 16pt insets so it reads like a feature, not a caption

### Border Radius Scale
- Square (0pt): full-bleed carousel photos (when full-screen)
- Subtle (5pt): tags on cards, small chips
- Standard (8pt): pastel topic tags, photo-overlay chips, inputs (small)
- Card (14pt): lifestyle cards, embedded article photos
- Comfortable (20pt): bottom sheets, search capsule, modals
- Pill (500pt): all buttons, Follow pills, segmented controls
- Circle (50%): avatars, like-burst particles

## 6. Depth & Elevation

Lemon8 uses soft, magazine-print elevation — cards lift gently off the white page; nothing is harsh.

| Level | Treatment | Use |
|-------|-----------|-----|
| Page (Level 0) | No shadow | Canvas, article body text, dividers |
| Card (Level 1) | `rgba(0,0,0,0.06) 0 1px 3px` + `rgba(0,0,0,0.04) 0 4px 12px` | Lifestyle cards in the masonry feed |
| Floating (Level 2) | `rgba(0,0,0,0.10) 0 4px 16px` | Post FAB region, sticky creator bar |
| Sheet (Level 3) | `rgba(0,0,0,0.12) 0 -4px 24px` | Bottom sheets (Share, comment composer, filters) |
| Modal Overlay | `rgba(0,0,0,0.45)` scrim | Behind modals, full-screen photo viewer |

**Shadow Philosophy**: The masonry card shadow is deliberately *two-layer and very low opacity* — a tight `0 1px 3px` for crispness plus a diffuse `0 4px 12px` for the gentle float. This mimics a magazine page where photos sit slightly proud of the paper. There is no heavy Material elevation; Lemon8's depth is soft and editorial. In dark mode shadows nearly vanish, so cards gain a 0.5pt `#2E2E2E` border as the separation cue.

### Motion
- **Masonry card press**: scale 1.0 → 0.98 over 120ms, shadow lifts slightly; release springs back
- **Card -> post detail**: tapped card's cover expands into the post carousel over 300ms ease-out (shared element); back collapses in 260ms
- **Like heart tap**: scale 1.0 → 1.2 → 1.0 over 260ms spring (damping 0.6) + soft haptic + brief `#FF2E63` particle burst
- **Top tab switch**: yellow underline slides horizontally to the new tab over 220ms ease-out; content cross-fades 180ms
- **Photo carousel**: paged horizontal scroll with 1:1 finger tracking; dots animate
- **Masonry infinite load**: new cards fade + 6pt slide-up over 220ms as they drop into the shorter column
- **Pull to refresh**: yellow lemon-slice rotates while pulling, spins on release
- **Post / save success**: green `#1FB877` checkmark pops (200ms) + toast slides up
- **Haptic feedback**: soft on like, light tick on tab switch, medium on follow/save confirm

## 7. Do's and Don'ts

### Do
- Use bright editorial white (`#FFFFFF`) as the canvas — Lemon8 is a magazine
- Build the feed as a two-column staggered masonry with varied card heights
- Style each card as a mini magazine cover: tall photo + punchy title + pastel tags + creator byline
- Use Lemon8 Yellow (`#FFE600`) sparingly — post FAB, active tab underline, primary CTA only
- ALWAYS pair Lemon8 Yellow with ink text (`#1A1A1A`) — never white-on-yellow
- Use the pastel topic-tag palette (mint/blush/sky/butter) as the app's color rhythm
- Use soft two-layer low-opacity card shadows so cards float gently off the white
- Use Poppins for display/titles, Inter for body/cards/meta
- Structure post detail like a magazine article — carousel + subheadings + embedded photos
- Give the post-compose FAB a yellow rounded-rectangle treatment in the tab bar
- Line-clamp card titles to 2 lines — they're cover teasers, not paragraphs

### Don't
- Don't use a single-column full-width feed — the masonry is the signature
- Don't flood the UI with yellow — it's a sparing accent, not a background
- Don't put white text on Lemon8 Yellow — always ink `#1A1A1A`
- Don't use heavy/hard Material shadows — Lemon8's elevation is soft and editorial
- Don't set body copy or card titles in Poppins — that's Inter's job
- Don't make the canvas off-white or gray — bright `#FFFFFF` is the magazine page
- Don't use saturated tag backgrounds — the topic tags are soft pastels with saturated text
- Don't center card titles — they're left-aligned cover lines
- Don't crop card covers to a fixed square — varied aspect ratios feed the masonry rhythm
- Don't bury topic tags — every card should carry 1-3, they're the color system
- Don't force dark mode as primary — the light magazine aesthetic is core identity

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 2-column masonry, ~8pt gaps, card title 13pt |
| iPhone 13/14/15 | 390pt | Standard 2-column, 10pt gaps |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in translucent nav |
| iPhone 15/16 Pro Max | 430pt | 2-column, slightly wider cards, 15pt card titles |
| iPad (portrait) | 768pt | 3-column masonry; post detail centered 680pt |
| iPad (landscape) | 1024pt+ | 4-column masonry; 2-pane (feed + detail) optional |

### Dynamic Type
- Display, screen titles, post title, body: full scale
- Card titles: scale to L then line-clamp at 2
- Tags, card bylines, tab labels: FIXED (layout-sensitive in the masonry)

### Orientation
- Masonry re-balances column count and card widths on rotation
- Post detail rotates; carousel becomes full-screen lightbox in landscape
- iPad landscape: optional 2-pane (masonry left, article right)

### Touch Targets
- Card: full card tappable (always > 44pt)
- Like heart: 24pt glyph, 44pt hit
- Tab bar icons: 23pt glyph, 48pt hit; Post FAB 42×30pt with 48pt hit
- Tag: full-pill tappable, 32pt min
- Buttons: 44pt tall minimum

### Safe Area Handling
- Top: translucent segmented tabs respect safe area + Dynamic Island
- Bottom: translucent tab bar respects home indicator
- Sides: feed keeps ~10pt insets; article content 16pt; full-screen photo viewer edge-to-edge
- Keyboard: comment composer docks above keyboard

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#121212`
- Surface 1 / 2: `#FAFAFA` / `#F2F2F2` light · `#1C1C1E` / `#2A2A2A` dark
- Divider: `#ECECEC` light / `#2E2E2E` dark
- Ink / text primary: `#1A1A1A` light / `#F2F2F2` dark
- Text secondary: `#6B6B6B` · tertiary `#9A9A9A`
- Lemon8 Yellow: `#FFE600` (pressed `#E6CF00`) — always with ink `#1A1A1A`
- Tag mint: `#E4F5EC` / `#1F8A52`
- Tag blush: `#FCE8EE` / `#C13E68`
- Tag sky: `#E6F0FB` / `#2D6FB8`
- Tag butter: `#FFF6CC` / `#8A7300`
- Like red: `#FF2E63` · Success `#1FB877` · Error `#FF3B5C`
- Card shadow: `rgba(0,0,0,0.06) 0 1px 3px` + `rgba(0,0,0,0.04) 0 4px 12px`

### Example Component Prompts
- "Build the Lemon8 discovery masonry feed in SwiftUI: a two-column staggered grid that places each card into the currently-shorter column. 10pt column gap, 12pt vertical gap, 10pt outer inset. Each card: `#FFFFFF` bg, 14pt corner radius, soft shadow (`0 1px 3px rgba(0,0,0,0.06)` + `0 4px 12px rgba(0,0,0,0.04)`). Card content: cover image at varied aspect (1:1 / 3:4 / 4:5) with an optional bottom-left `rgba(0,0,0,0.55)` chip ('5 photos'), then 10pt padding holding a 2-line-clamped title (Inter 13pt w600 `#1A1A1A`), a pastel tag row, and a byline (18pt avatar + name Inter 11pt `#6B6B6B` + like heart + count)."

- "Create a Lemon8 lifestyle card: white card, 14pt radius, soft two-layer shadow. Top: cover photo (aspect 3:4) with top corners following the card radius and a `rgba(0,0,0,0.55)` 'Guide' chip bottom-left (white Inter 10pt w600, 6pt radius). Body padding 11pt: title 'Autumn capsule wardrobe' Inter 13pt w600 `#1A1A1A` clamped to 2 lines; tag row with a blush tag ('Fashion', bg `#FCE8EE` text `#C13E68`, 9pt w600, 5pt radius); byline row: 18pt gradient avatar + 'mira.styles' (Inter 11pt `#6B6B6B`) + like heart + '2.4k'."

- "Render the Lemon8 top segmented tabs: a row of 'Following', 'For You', 'Nearby' in Poppins 22pt. Active tab is ink `#1A1A1A` w700 with a 20pt-wide 3pt-tall `#FFE600` underline centered 6pt below it (2pt radius); inactive tabs are `#9A9A9A` w600. Trailing magnifyingglass 22pt ink stroke. Switching tabs slides the yellow underline horizontally over 220ms ease-out and cross-fades content."

- "Build the Lemon8 bottom tab bar: 54pt + safe area, `rgba(255,255,255,0.96)` with `saturate(180%) blur(20px)`, 0.5pt top border `#ECECEC`. Five slots: Home, Discover, Post, Inbox, Me. The Post slot is NOT an icon — it's a 42x30pt `#FFE600` rounded rectangle (9pt radius) centering an ink `+` glyph. Other tabs: 23pt icon, ink fill when active, `#9A9A9A` inactive, Inter 10pt w500 label."

- "Create a Lemon8 pastel topic-tag row: pill-ish rounded rects (8pt radius), Inter 11pt w600, 7pt/14pt padding. Mint (`#E4F5EC` bg / `#1F8A52` text) for Recipe, blush (`#FCE8EE` / `#C13E68`) for Beauty, sky (`#E6F0FB` / `#2D6FB8`) for Travel, butter (`#FFF6CC` / `#8A7300`) for Home. Each tag tappable -> topic feed."

- "Render the Lemon8 post detail (magazine article): a full-width swipeable photo carousel with page dots and a '1/8' counter chip top-right (`rgba(0,0,0,0.55)`). Below at 16pt insets: title in Poppins 18pt w600 `#1A1A1A`; creator card (56pt avatar + name Poppins 17pt w600 + follower count Inter 13pt `#6B6B6B` + yellow Follow pill); styled body (Inter 16pt w400 line-height 1.55, Poppins 17pt w600 subheadings, embedded full-width photos at 14pt radius); pastel tag row; like/save/share action bar; comments."

### Iteration Guide
1. Canvas is bright editorial white `#FFFFFF` (dark `#121212`) — it's a magazine, light is the core identity
2. The feed is a two-column staggered MASONRY of varied-height lifestyle cards — never single-column
3. Lemon8 Yellow `#FFE600` is a sparing accent (post FAB, active tab underline, primary CTA) — never a flood, ALWAYS with ink text
4. Pastel topic tags (mint/blush/sky/butter) are the app's color rhythm — every card carries 1-3
5. Cards are mini magazine covers: tall photo + punchy 2-line title + tags + creator byline + like count
6. Card elevation is soft and two-layer (`0 1px 3px` + `0 4px 12px`, very low opacity) — gentle float, never harsh
7. Poppins = display/titles/headers; Inter = body/cards/meta/tags — never swap them
8. Post detail is a structured magazine article — carousel + subheadings + embedded photos, not a tweet
9. The post-compose FAB is a yellow rounded rectangle in the tab bar, not a plain icon
10. Card titles line-clamp at 2 lines — they're cover teasers; the body breathes at 1.55 line height
