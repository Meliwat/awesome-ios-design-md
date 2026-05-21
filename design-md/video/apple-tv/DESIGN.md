# Design System Inspiration of Apple TV (iOS)

## 1. Visual Theme & Atmosphere

The Apple TV app is the most *restrained* streaming surface Apple ships. Its philosophy is the opposite of broadcast loudness: the catalog is presented like a curated gallery on a true-black wall. The canvas is pure `#000000` — not a charcoal, not a near-black — chosen so that on OLED iPhones the pixels switch off entirely and inset, rounded artwork appears to float in void. Open the **Watch Now** tab and you see a large "Watch Now" title in SF Pro 34pt bold, an inset rounded **hero card** (a single featured Apple TV+ Original, art with 16pt corner radius and a generous bottom scrim), then the signature **Up Next** rail — a horizontal row of 16:9 thumbnails, each with a thin white resume-progress bar and a small "Apple TV+" channel tag — followed by editorial shelves: "MLS Season Pass", "Apple TV+ Originals", "Top Movies", "What to Watch". Everything breathes: large margins, restrained type, no badges screaming for attention.

The chrome is glass-and-blur, pure Apple HIG. The bottom tab bar (**Watch Now / TV+ / Store / Search**, or **MLS** when subscribed) is a translucent material — a `blur(20px) saturate(180%)` layer over true black with a hairline `#38383A` top divider. There is exactly one accent color: **system blue `#0A84FF`** (the iOS dark-mode systemBlue), used only for the active-tab tint and text links ("More", "See All"). There is no brand-red, no gradient, no second hue. The primary call to action — "Play" — is a solid **white** button with a black label and a fully rounded 12pt radius; secondary actions ("+ Watchlist") use the iOS translucent control fill `rgba(120,120,128,0.36)`. The restraint *is* the brand: Apple TV looks like part of the OS, not a third-party app.

Two product surfaces are signature. **MLS Season Pass** is the one place a non-system color appears: the league's hot pink `#ED1A6F`, used on its shelf header treatment, its badge, and live-match chips. Live sports (MLS, MLB) carry the standard iOS destructive-adjacent red `#FF453A` with a pulsing dot for "LIVE". Everything else — Apple TV+ Originals, store rentals — stays monochrome with the single blue link accent.

Typography is **SF Pro** (Display for titles, Text for body) — Apple's system face; the closest free substitute is **Inter**. The hierarchy is HIG-canonical: Large Title 34pt, hero 28pt, row header 22pt, body 17pt, with the familiar iOS optical sizing and tight tracking on large sizes. Dynamic Type is first-class; the layout reflows rather than truncates.

**Key Characteristics:**
- True-black canvas (`#000000`) — OLED pixels off; inset rounded artwork floats in void
- **Watch Now** home: large SF Pro title + inset rounded hero card + the Up Next rail
- **Up Next rail** — 16:9 thumbnails with a thin white resume-progress bar + "Apple TV+" channel tag
- **One accent** — system blue `#0A84FF` for the active tab and text links only; nothing else
- **White primary CTA** — "Play" is solid `#FFFFFF` with a black label, 12pt rounded; glass secondary
- Glass-and-blur chrome — translucent `blur(20px)` tab bar over true black, hairline divider
- **MLS Season Pass** — the one non-system color: league hot-pink `#ED1A6F` on its shelf & live chips
- Live sports red `#FF453A` + a pulsing dot — standard iOS, used sparingly
- Generous whitespace, HIG spacing — the most restrained of all streaming apps
- SF Pro typography (Inter fallback), Dynamic Type first-class, optical tracking on large titles
- Bottom tab bar: Watch Now / TV+ / Store / Search (MLS when subscribed)

## 2. Color Palette & Roles

The Apple TV app is dark-first and true-black. A light mode exists only inside the embedded Store / account sheets (which follow system appearance); the browse experience is always true-black.

### Primary (Interactive)
- **CTA White** (`#FFFFFF`): Primary action — "Play" / "Resume" button fill. The single most important control.
- **CTA White Pressed** (`#E5E5EA`): Pressed state + scale 0.98.
- **CTA Label** (`#000000`): Black text/icon on the white CTA.
- **System Blue** (`#0A84FF`): The only accent — active-tab tint, text links ("More", "See All"), "Subscribe" tint button.
- **Blue Pressed** (`#0060DF`): Pressed state of the tinted button.
- **Glass Fill** (`rgba(120,120,128,0.36)`): Secondary action ("+ Watchlist", add) — iOS translucent control fill.

### Canvas & Surfaces (Light Mode — embedded Store / account only)
- **Light Canvas** (`#FFFFFF`): Store / account sheet background when system is light.
- **Light Surface** (`#F2F2F7`): Grouped rows (iOS systemGroupedBackground).
- **Light Divider** (`#C6C6C8`): Hairline separators.
- **Light Text** (`#000000`): Body text in light sheets.

### Canvas & Surfaces (Dark Mode — the product)
- **True Black Canvas** (`#000000`): Primary app background — OLED true black.
- **Surface 1** (`#1C1C1E`): Cards, grouped rows, sheets (iOS secondarySystemBackground).
- **Surface 2** (`#2C2C2E`): Elevated chips, pressed rows (iOS tertiarySystemBackground).
- **Divider** (`#38383A`): Hairline dividers, tab-bar top border (iOS separator on dark).

### Text
- **Text Primary** (`#FFFFFF`): Titles, headlines, primary labels.
- **Text Secondary** (`#98989F`): Metadata, subheads, captions (iOS secondaryLabel on dark).
- **Text Tertiary** (`#636366`): Disabled, chevrons, faint meta (iOS tertiaryLabel on dark).

### Semantic
- **MLS Pink** (`#ED1A6F`): MLS Season Pass shelf treatment, badge, live-match chip — the one brand-color exception.
- **Live Red** (`#FF453A`): Live sports "LIVE" badge — pulsing dot (iOS systemRed on dark).
- **Success Green** (`#30D158`): Download-complete, purchase confirmation (iOS systemGreen on dark).
- **Rating Gold** (`#FFD60A`): Critic / star rating (iOS systemYellow on dark).

### Role → Color Table

| Role | Light Mode (Store sheets) | Dark Mode (product) |
|------|---------------------------|---------------------|
| Canvas | `#FFFFFF` | `#000000` |
| Surface 1 | `#F2F2F7` | `#1C1C1E` |
| Surface 2 | `#FFFFFF` | `#2C2C2E` |
| Divider | `#C6C6C8` | `#38383A` |
| Text Primary | `#000000` | `#FFFFFF` |
| Text Secondary | `#3C3C4399` | `#98989F` |
| Text Tertiary | `#3C3C434D` | `#636366` |
| Primary CTA | `#000000` (label on white) | `#FFFFFF` |
| Accent (links / active tab) | `#007AFF` | `#0A84FF` |
| MLS | `#ED1A6F` | `#ED1A6F` |
| Live | `#FF3B30` | `#FF453A` |

## 3. Typography Rules

### Font Family
- **Brand**: `SF Pro` — Apple's system typeface. `SF Pro Display` for ≥20pt titles, `SF Pro Text` for body, with optical sizing handled by the system.
- **Closest Free Substitute**: `Inter` (by Rasmus Andersson, Google Fonts, SIL OFL) — a neutral grotesque designed in the SF Pro idiom; the standard SF substitute for non-Apple builds.
- **Alternate Substitute**: `-apple-system` / `BlinkMacSystemFont` resolves to SF Pro automatically on Apple platforms — prefer it when shipping on iOS.
- **System Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: SF Pro lining; tabular for time codes, scores, "24 min left".

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | SF Pro Display | 34pt | 800 | 1.05 | -0.8pt | "Watch Now", "Search" nav titles |
| Hero Title | SF Pro Display | 28pt | 800 | 1.05 | -0.6pt | Featured hero-card title |
| Row Header | SF Pro Display | 22pt | 700 | 1.15 | -0.3pt | "Up Next", "MLS Season Pass" |
| Title 3 | SF Pro Display | 20pt | 600 | 1.2 | -0.2pt | Sub-shelf / card title |
| Body | SF Pro Text | 17pt | 400 | 1.45 | 0pt | Synopsis, descriptions |
| Headline | SF Pro Text | 15pt | 600 | 1.3 | 0pt | Up Next thumbnail title |
| Subhead | SF Pro Text | 13pt | 400 | 1.35 | 0pt | "S3 E8 · 24 min left" |
| Caption | SF Pro Text | 12pt | 500 | 1.2 | 0pt | Tab labels, channel tag |
| Eyebrow | SF Pro Text | 11pt | 700 | 1.0 | 1.2pt (uppercase) | "NEW EPISODE" over hero |
| Button | SF Pro Text | 15pt | 600 | 1.0 | 0pt | Primary/secondary button label |
| Footnote | SF Pro Text | 13pt | 400 | 1.3 | 0pt | Legal, fine print in sheets |
| Channel Tag | SF Pro Text | 9pt | 700 | 1.0 | 0.4pt | "Apple TV+" tag on thumbnails |

### Principles
- **HIG canonical**: Sizes and weights track Apple's Human Interface Guidelines text styles — Large Title → Title → Body → Caption — not custom product scales.
- **Optical tracking on large sizes**: Large Title / Hero use tight negative tracking (-0.6 to -0.8pt) exactly as SF Pro Display's optical size dictates.
- **Restraint over emphasis**: weights stay 400 / 600 / 700 / 800 — no black, no condensed, no decorative weight; the type does not shout.
- **One accent, used as link**: blue appears only as the iOS link/tint role, never as a fill behind text or as decoration.
- **Dynamic Type first-class**: Large Title, hero, row headers, body, subhead all scale; tab labels, channel tags, eyebrows stay fixed (layout-sensitive over art).

## 4. Component Stylings

### Buttons

**Primary Button (Play / Resume)**
- Shape: Rounded rectangle, 12pt corner radius (iOS prominent button)
- Background: `#FFFFFF` (always white)
- Text/Icon: `#000000`, SF Pro Text 15pt weight 600, leading ▶ play glyph 14pt
- Padding: 13pt vertical, 30pt horizontal
- Pressed: background `#E5E5EA` + scale 0.98
- Width: hugs content on hero; full-width on detail page

**Tinted Button (Subscribe / Get / Rent)**
- Background: `#0A84FF` (system blue — the accent)
- Text: `#FFFFFF`, SF Pro Text 15pt weight 600
- Corner radius: 12pt
- Padding: 13pt vertical, 26pt horizontal
- Pressed: background `#0060DF` + scale 0.98

**Glass / Secondary Button (+ Watchlist / Trailer)**
- Background: `rgba(120,120,128,0.36)` with `backdrop-filter: blur(8px)` (iOS translucent fill)
- Text: `#FFFFFF`, SF Pro Text 15pt weight 600
- Border: none
- Corner radius: 12pt (or 50% circle for icon-only add)
- Padding: 13pt vertical, 24pt horizontal
- Pressed: fill opacity → 0.5

**Text Button (More / See All / Manage)**
- Background: transparent
- Text: `#0A84FF` (system blue link), SF Pro Text 15pt weight 500
- No underline
- Pressed: opacity 0.6

**Icon Button (add, search, avatar)**
- 18-24pt glyph, 44pt hit area
- White; the round "+" add uses the glass fill background
- No background unless the round glass variant

### Core Atoms

**Up Next Thumbnail**
- 16:9 aspect ratio, ~196pt wide on phone (variable per shelf)
- Corner radius: 10pt
- Full-bleed key art, no border
- Bottom resume-progress: 4pt-tall bar, track `rgba(255,255,255,0.28)`, fill solid `#FFFFFF`
- Top-left "Apple TV+" channel tag: glass `rgba(0,0,0,0.4)` blur, SF Pro 9pt w700, 5pt radius
- Title below: SF Pro Text 15pt w600 `#FFFFFF`
- Subhead below: SF Pro Text 13pt w400 `#98989F` — "S3 E8 · 24 min left"
- Pressed: scale 0.97, 150ms

**Hero Card (inset, rounded)**
- Inset by 14pt horizontal margin (NOT full-bleed — the signature "floating" treatment)
- Corner radius: 16pt
- Key art with bottom scrim `linear-gradient(180deg, transparent 40%, rgba(0,0,0,0.65) 72%, rgba(0,0,0,0.92) 100%)`
- Content bottom-left: eyebrow (channel + "New Episode") → title (SF Pro Display 28pt w800) → meta (`12pt #98989F` dot-separated) → button pair (white Play + round glass add)

**Live Badge**
- `#FF453A` fill, 6pt corner radius, SF Pro 11pt w700 uppercase, 5pt/10pt padding
- Leading 7pt white dot that pulses (opacity 1 → 0.4, 1.2s loop)

**MLS Season Pass Chip**
- `#ED1A6F` fill (the one brand-color exception), 10pt corner radius, SF Pro 12pt w700
- Used on the shelf header, the subscribe entry point, and live-match chips

**Resume Progress Bar**
- 4pt tall, track `rgba(255,255,255,0.28)`, fill solid white, sits flush at the thumbnail's bottom edge

**Channel Tag**
- Small glass pill (`rgba(0,0,0,0.4)` blur) reading "Apple TV+", top-left of a thumbnail, SF Pro 9pt w700

### Navigation

**Bottom Tab Bar**
- Height: 64pt + safe area
- Background: translucent material — `rgba(0,0,0,0.92)` with `backdrop-filter: blur(20px) saturate(180%)`
- Top border: 0.33pt `#38383A` hairline
- Tabs (4): Watch Now, TV+, Store, Search (MLS appears when subscribed)
- Icon: 24pt; active = `#0A84FF` **filled** glyph, inactive = `#636366` stroked glyph
- No tint pill — the color + fill swap is the active treatment (standard iOS UITabBar)
- Labels: SF Pro Text 12pt w500, always shown

**Large Title Bar (Watch Now)**
- Large SF Pro Display 34pt w800 title, left-aligned, with a trailing circular profile avatar (30pt)
- Collapses to an inline navigation bar on scroll (standard iOS large-title behavior)

**Detail Page**
- Inset rounded hero art + back chevron (top-left, white, system back)
- Sticky inline title appears on scroll past the hero

### Input Fields

**Search Bar**
- Height: 36pt (iOS search field)
- Background: `rgba(120,120,128,0.24)` (iOS searchField fill on dark)
- Border: none
- Corner radius: 10pt
- Leading magnifier 15pt `#98989F`
- Placeholder: "Shows, Movies, and More" SF Pro 17pt `#98989F`
- Text: white; "Cancel" link in `#0A84FF` appears on focus

### Distinctive Components

**Up Next Rail**
- The signature Apple TV component — a continue-watching row of 16:9 thumbnails with a thin white resume bar and "Apple TV+" channel tags; the primary re-entry surface

**Inset Rounded Hero Card**
- Unlike full-bleed competitor billboards, Apple TV's hero is inset by 14pt with a 16pt corner radius, so it floats on the true-black canvas — the signature "gallery" treatment

**MLS Season Pass Shelf**
- The one place a non-system brand color (`#ED1A6F`) appears — league pink on the shelf header, badges, and live-match chips

**Glass Tab Bar**
- A translucent `blur(20px)` material over true black — pure Apple HIG; content scrolls behind it

**System-Native Detail Sheet**
- Show / movie detail pushes as a standard iOS sheet/page with the white Play CTA, glass secondary controls, episodes list, and "More Like This" shelf — indistinguishable from a system app

**Channel Tags**
- Small glass "Apple TV+" pills on thumbnails distinguish first-party Originals from store rentals — the only "badge" the restrained UI allows

## 5. Layout Principles

### Spacing System
- Base unit: 4pt (Apple 8pt grid with 4pt half-steps)
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Content horizontal inset: 18pt (rows bleed off the right edge for "more content")
- Hero card inset: 14pt horizontal (the floating treatment)
- Inter-shelf vertical gap: 16-24pt (generous — the restraint signature)
- Thumbnail gap within a row: 12pt

### Grid & Container
- iPhone: 18pt side inset; hero card inset 14pt; rows scroll horizontally and bleed off the right edge
- iPad: shelves show more thumbnails per viewport; hero card larger, still inset; multi-column where HIG applies
- Hero card: inset rounded, ~52% of screen height on phone
- No max content width — shelves are edge-to-edge horizontal scrollers

### Whitespace Philosophy
- **Gallery restraint**: generous inter-shelf gaps (16-24pt) and the inset floating hero — Apple TV deliberately uses *more* whitespace than any competitor
- **Content over chrome**: minimal chrome; the only persistent UI is the glass tab bar and the large title
- **No badge clutter**: the only labels are channel tags and the occasional LIVE/MLS chip — nothing competes with art

### Border Radius Scale
- Square (0pt): rarely — only full-bleed video playback
- Small (6pt): channel tags, small chips
- Card (10pt): Up Next thumbnails, search field
- Standard (12pt): buttons, MLS chip, grouped rows
- Hero (16pt): the inset hero card, sheets
- Comfortable (22pt): large modals, bottom sheets
- Pill (500pt): segmented controls, filter pills
- Circle (50%): profile avatar, round glass add button

## 6. Depth & Elevation

Apple TV depth is HIG-canonical: materials and blur, not drop shadows. The true-black canvas plus the inset rounded hero create the "floating gallery" depth.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow on true black | Shelves, thumbnails, large title |
| Inset Float | 16pt-radius card inset 14pt on `#000` | The hero card "floats" purely via inset + radius |
| Material (Level 1) | `blur(20px) saturate(180%)` over `rgba(0,0,0,0.92)` | Tab bar, navigation bar on scroll |
| Sheet (Level 2) | iOS sheet — `#1C1C1E` surface, 16pt top radius, system shadow | Detail page, Store, account |
| Modal Overlay | `rgba(0,0,0,0.4)` | Dim behind sheets, sign-in, age-gate |

**Shadow Philosophy**: Apple TV essentially does not use drop shadows in the browse experience — depth comes from (a) the inset, rounded hero card floating on pure black, (b) iOS vibrancy/blur materials for chrome, and (c) standard system sheet presentation for detail. On true black a shadow is invisible anyway; the design leans entirely on the material system. Thumbnails lift only via a subtle scale on press/focus (tvOS parity).

### Motion
- **Hero card parallax**: the inset hero art parallaxes gently on scroll; if multiple featured, cross-fades every ~10s (400ms ease-in-out)
- **Shelf scroll**: native momentum horizontal scroll; thumbnails do not snap (free scroll), 60fps
- **Thumbnail press**: scale 1.0 → 0.97 in 120ms ease-out; release springs back (system spring)
- **Large title collapse**: standard iOS large-title → inline transition tied to scroll offset
- **Tab switch**: instant content swap; icon color+fill cross-fades 200ms (system)
- **Detail open**: standard iOS push/sheet — 350ms system transition (no custom shared-element)
- **Live dot pulse**: opacity 1 → 0.4 → 1 over 1.2s, infinite ease-in-out
- **Sheet present**: standard iOS sheet — slides up with the system spring; dim fades in parallel
- **Resume bar**: appears at its watched value (no fill animation — it is state, not progress feedback)
- **Haptic feedback**: light selection on thumbnail tap; standard system haptics — sparing, never custom

## 7. Do's and Don'ts

### Do
- Use pure true-black `#000000` as the canvas — OLED pixels off, art floats
- Inset the hero card (14pt) with a 16pt corner radius — the signature floating-gallery treatment
- Use SF Pro (or `-apple-system`) and follow HIG text styles — Large Title / Body / Caption
- Keep system blue `#0A84FF` as the *only* accent — active tab + text links, used as the iOS link role
- Make "Play" a solid white `#FFFFFF` button with a black label, 12pt rounded
- Use the iOS translucent fill `rgba(120,120,128,0.36)` for secondary controls
- Render the Up Next rail with 16:9 thumbnails + a thin white resume bar + "Apple TV+" channel tags
- Use generous inter-shelf whitespace (16-24pt) — Apple TV is the most restrained streamer
- Use the MLS hot-pink `#ED1A6F` ONLY for MLS Season Pass (the single brand-color exception)
- Use glass-and-blur materials for chrome (tab bar) — pure Apple HIG

### Don't
- Don't use a charcoal canvas — it must be pure `#000000` (the floating-art effect depends on it)
- Don't make the hero full-bleed — it is inset and rounded; full-bleed breaks the gallery identity
- Don't introduce a second accent or any gradient — blue is the only accent, MLS pink is the only exception
- Don't tint the Play button — it is always white with a black label
- Don't add badges/flags to thumbnails beyond the channel tag and the rare LIVE/MLS chip
- Don't use a Material-style tint pill on the active tab — it is the standard iOS color+fill swap
- Don't crowd shelves — Apple TV's restraint (whitespace, few labels) IS the brand
- Don't animate aggressively or with custom transitions — use system motion (springs, large-title collapse)
- Don't bold body text — body is SF Pro Text 17pt weight 400; restraint over emphasis
- Don't snap-scroll shelves — they are free momentum scrollers that bleed off the right edge
- Don't use MLS pink anywhere except MLS Season Pass surfaces

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Hero card ~330pt; ~1.7 thumbnails per viewport |
| iPhone 13/14/15 | 390pt | Standard layout; hero card 380pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; large title respects it |
| iPhone 15/16 Pro Max | 430pt | Hero card 420pt; ~2 thumbnails per viewport |
| iPad (portrait) | 768pt | Larger inset hero; ~3.5 thumbnails per shelf; HIG margins |
| iPad (landscape) | 1024pt+ | Hero card spans wider (still inset); ~5 thumbnails per shelf; sidebar where applicable |

### Dynamic Type
- Large Title, hero, row headers, body, subhead: full scale (HIG text styles)
- Tab labels, channel tags, eyebrows, "LIVE": FIXED (composited over art / layout-sensitive)
- Synopsis on detail page scales to the accessibility sizes

### Orientation
- Phone: portrait-primary for browsing; player rotates to landscape full-screen
- iPad: both orientations for browse; player always landscape full-screen
- Hero card re-crops (focal point preserved) and stays inset across orientations

### Touch Targets
- Up Next thumbnail: full-card tap, ≥196×112pt
- Tab bar icon: 24pt glyph, 44pt hit (system)
- Play button: ≥44pt tall
- Round glass add: 44×44pt
- Back chevron: system back, 44pt hit

### Safe Area Handling
- Top: large title respects safe area + Dynamic Island; hero card sits below it, inset
- Bottom: glass tab bar respects home indicator; shelves scroll behind it (vibrancy)
- Player: full-bleed edge-to-edge, controls inset from safe area
- Sides: 18pt content inset; 14pt hero-card inset; shelves bleed off the trailing edge

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (true black): `#000000`
- Surface 1: `#1C1C1E`
- Surface 2: `#2C2C2E`
- Divider: `#38383A`
- Text primary: `#FFFFFF`
- Text secondary: `#98989F`
- Text tertiary: `#636366`
- Primary CTA (Play): `#FFFFFF` fill / `#000000` label
- Accent (active tab / links): `#0A84FF`
- Blue pressed: `#0060DF`
- Glass control fill: `rgba(120,120,128,0.36)`
- MLS Season Pass: `#ED1A6F`
- Live red: `#FF453A`
- Rating gold: `#FFD60A`

### Example Component Prompts
- "Build the Apple TV Watch Now screen in SwiftUI: a large title 'Watch Now' in SF Pro Display 34pt w800 (`.largeTitle` style) with a trailing 30pt circular profile avatar. Below: an inset hero card — `.padding(.horizontal, 14)`, 16pt corner radius, key art with a bottom scrim `linear-gradient(180deg, .clear 40%, rgba(0,0,0,0.65) 72%, rgba(0,0,0,0.92) 100%)`. Card content bottom-left: eyebrow (Apple TV+ glyph + 'New Episode') SF Pro 11pt w700 uppercase `#98989F`; title SF Pro Display 28pt w800 `#FFFFFF`; meta SF Pro 12pt w500 `#98989F` dot-separated; button pair — white 'Play' (fill `#FFFFFF`, label `#000`, 12pt radius, ▶ glyph) + round 44pt glass '+' (`rgba(120,120,128,0.36)` blur)."

- "Create the Apple TV Up Next rail: a horizontal scroller, 18pt leading inset, 12pt gaps, bleeds off the trailing edge. Each thumbnail 16:9 ~196pt wide, 10pt corner radius. Bottom resume bar: 4pt tall, track `rgba(255,255,255,0.28)`, fill solid `#FFFFFF` at the watched fraction, flush to the bottom edge. Top-left glass 'Apple TV+' tag `rgba(0,0,0,0.4)` blur, SF Pro 9pt w700, 5pt radius. Below: title SF Pro 15pt w600 `#FFFFFF`, subhead SF Pro 13pt w400 `#98989F` ('S3 E8 · 24 min left'). Press: scale 0.97 over 120ms."

- "Render the Apple TV bottom tab bar: 64pt + safe area, translucent material `rgba(0,0,0,0.92)` `blur(20px) saturate(180%)`, 0.33pt top hairline `#38383A`. Four tabs Watch Now / TV+ / Store / Search, 24pt SF Symbols. Active = `#0A84FF` filled glyph, inactive = `#636366` stroked glyph, no tint pill. Labels SF Pro 12pt w500 always shown. Content scrolls behind it (vibrancy)."

- "Build the Apple TV MLS Season Pass shelf header: row header 'MLS Season Pass' SF Pro Display 22pt w700 `#FFFFFF` with a trailing chevron `#636366`; an `#ED1A6F` chip (10pt radius, SF Pro 12pt w700 white) as the subscribe entry point. This pink is the ONLY non-system brand color in the app — use it nowhere else."

- "Create the Apple TV primary 'Play' button: fill `#FFFFFF`, label 'Play' SF Pro Text 15pt w600 `#000000` with a leading 14pt ▶ glyph, 12pt corner radius, 13pt/30pt padding. Pressed: `#E5E5EA` + scale 0.98. The secondary action beside it is a round 44pt glass button `rgba(120,120,128,0.36)` with a white '+' — never tint either with blue."

- "Render an Apple TV live sports chip: `#FF453A` fill, 6pt radius, 'LIVE' SF Pro 11pt w700 uppercase white with a leading 7pt white dot pulsing opacity 1 → 0.4 over 1.2s infinite. For MLS matches, the channel tag uses `#ED1A6F` instead."

### Iteration Guide
1. Canvas is pure true-black `#000000` — NEVER charcoal; the inset art must float in void on OLED
2. The hero is an *inset* (14pt) rounded (16pt) card — NOT full-bleed; this is the signature gallery treatment
3. System blue `#0A84FF` is the ONLY accent, used as the iOS link/active-tab role — no gradients, no second hue
4. "Play" is solid white `#FFFFFF` with a black label, 12pt rounded; secondary uses the iOS translucent fill
5. The Up Next rail (16:9 thumbnails + thin white resume bar + 'Apple TV+' tags) is the signature surface
6. Typography is SF Pro / `-apple-system` following HIG text styles — Large Title 34pt down to Caption; body w400
7. Chrome is glass-and-blur (`blur(20px)` tab bar over true black) — pure Apple HIG, content scrolls behind
8. MLS pink `#ED1A6F` is the ONE brand-color exception — MLS Season Pass surfaces only, nowhere else
9. Restraint is the brand — generous whitespace (16-24pt inter-shelf), no badge clutter, system motion only
10. Live sports use red `#FF453A` + a pulsing dot, sparingly — everything else stays monochrome + blue link
