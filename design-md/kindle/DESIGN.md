# Design System Inspiration of Kindle (iOS)

## 1. Visual Theme & Atmosphere

Kindle's iOS app exists to disappear. Its entire design philosophy is that the reading page *is* the product, and every pixel of chrome is engineered to vanish the instant a book opens. Tap into a title and the navigation, status bar, and toolbars fade away in 250ms, leaving a single justified column of warm-ink serif text on a sepia page (`#FBF0D9`). There are no cards, no borders, no shadows, no accent colors in the reading view — just type, generous leading (1.7+), hyphenated justified text, and a thin progress whisper at the bottom edge ("38% · 14 min left in chapter"). A single tap in the center of the page brings the chrome back; a tap on the edges (or a swipe) turns the page. This radical reduction is the brand: Kindle competes with paper, so it behaves like paper.

The signature screen is the **reader page**: chapter eyebrow ("CHAPTER SEVEN"), a serif chapter title, body set in a reading serif (Bookerly on real devices; Bitter is a close free analog) at ~15.5pt with 1.72 line-height and justified, hyphenated text, an optional drop-cap on the opening paragraph, and the location/time-left footer. Pulling up the bottom toolbar reveals the iconic **Aa typography panel** — font family, size, line spacing, margins, and the five page **themes** (White, Sepia, Green, Dark, Black) shown as round swatches with the active one ringed in Amazon orange. The second signature surface is the **Library**: a grid of full-color cover art, each cover wearing a thin orange reading-progress bar along its bottom edge, with "Continue reading" surfacing the most recent book at the top of Home.

Outside the reader, Kindle is an Amazon product and uses Amazon's system: near-black chrome (`#1A1A1A`), Amazon Ember as the UI typeface, and Amazon Orange (`#FF9900`) as the single brand accent — used for the primary "Read now" / "Buy" CTA, reading-progress fills, the active-theme ring, and selected tab. There is no second accent. Cover art provides all the color; the UI is monochrome warm-neutral plus orange. The app has two distinct surface contexts: the **reading surface**, which obeys the user's chosen page theme regardless of system appearance, and the **app chrome** (Home, Library, store, settings), which follows system light/dark.

**Key Characteristics:**
- The reading page is the product — chrome fades out 250ms on book open, taps it back
- Sepia paper (`#FBF0D9`) + warm ink (`#5F4B32`) as the default reading theme — paper, not screen
- Five reading themes: White / Sepia / Green / Dark / Black — user-chosen, OS-independent
- Reading serif (Bookerly → Bitter analog) at ~15.5pt, 1.72 line-height, justified + hyphenated
- Amazon Orange (`#FF9900`) — the single accent: CTA, progress fill, active-theme ring, selected tab
- Amazon Ember + near-black `#1A1A1A` chrome — this is an Amazon app
- Library = full-color cover grid, each cover wearing a thin orange progress bar
- The Aa typography panel — font, size, spacing, margins, theme swatches
- Progress everywhere: "38% · 14 min left in chapter", location numbers, percent
- Quiet bottom progress whisper in the reader; no heavy chrome
- Minimal bottom tab bar: Home / Library / Discover / More

## 2. Color Palette & Roles

### Primary (Brand & Interactive)
- **Amazon Orange** (`#FF9900`): The single brand accent — primary CTA ("Read now", "Buy"), reading-progress fill, active-theme ring, selected tab, in-text highlight tint.
- **Orange Pressed** (`#E88B00`): Pressed state of the orange CTA.
- **Kindle Black** (`#1A1A1A`): Primary chrome surface / text-on-orange / app bars.
- **Link Blue** (`#1A98FF`): Hyperlinks, footnote links, store links (inherited from Amazon system).
- **Kindle Blue** (`#4FB3D9`): Accent for X-Ray / Word Wise / accessibility highlights (secondary, sparing).

### Reading Themes (the core surface system)
The reading surface obeys the user's chosen theme, independent of OS appearance.

| Theme | Page Background | Body Ink |
|-------|-----------------|----------|
| White | `#FFFFFF` | `#1A1A1A` |
| Sepia (default) | `#FBF0D9` | `#5F4B32` |
| Green | `#C5E1C5` | `#33492F` |
| Dark | `#2A2A2A` | `#D8D8D8` |
| Black (OLED) | `#000000` | `#C8C8C8` |

### App Chrome — Light Mode
- **Chrome Canvas** (`#FFFFFF`): Home / Library / store background.
- **Surface Subtle** (`#F4F2EE`): Grouped rows, search field.
- **Divider** (`#E4E2DD`): Hairline rules in lists.

### App Chrome — Dark Mode
- **Dark Canvas** (`#0E0E0E`): App chrome background in system dark.
- **Dark Surface 1** (`#1A1A1A`): Cards, rows, toolbars.
- **Dark Surface 2** (`#242424`): Pressed rows, search field.
- **Dark Divider** (`#2E2E2E`): Hairline rules on dark.

### Text (Chrome)
- **Text Primary Light** (`#1A1A1A`): Library titles, settings labels.
- **Text Secondary Light** (`#6B6B6B`): Metadata, progress captions.
- **Text Primary Dark** (`#E8E8E8`): Primary text on dark chrome.
- **Text Secondary Dark** (`#9A9A9A`): Secondary text on dark chrome.
- **Text Tertiary Dark** (`#6A6A6A`): Disabled / overline on dark.

### Semantic
- **Error Red** (`#E0533D`): Download failure, remove-from-device confirm.
- **Success Green** (`#2FAE5F`): "Downloaded" / sync-complete toast.
- **Progress Orange** (`#FF9900`): Reading progress bar fill (reuses brand orange).
- **Warning Amber** (`#FF9900`): Non-blocking warnings (reuses orange).

## 3. Typography Rules

### Font Family
- **Reading Serif**: `Bookerly` (Kindle's bundled commissioned reading serif by Dalton Maag). For non-Amazon implementations, use **`Bitter`** (Huerta Tipográfica, Google Fonts, SIL OFL) — a slab-leaning transitional serif with similar dark, even color and excellent screen reading at small sizes. Kindle also ships Amazon Ember, Caecilia, Palatino, Helvetica Neue, Georgia, Baskerville, OpenDyslexic as selectable reading faces.
- **UI / Chrome**: `Amazon Ember` (Dalton Maag for Amazon). For non-Amazon implementations, fall back to `-apple-system, 'SF Pro Text'` — a neutral grotesque; used for navigation, library, settings, and all chrome.
- **Reading face is user-selectable**: respect the reader's chosen serif/sans + size + spacing + margins globally — never hard-code one.
- **iOS Fallback**: serif → `Georgia` / `'Times New Roman'`; sans → `-apple-system`.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Ember | 30pt | 700 | 1.2 | -0.4pt | "Library", "Home" large nav title |
| Chapter Title | Bitter | 24pt | 700 | 1.25 | 0pt | Chapter head in the reader |
| Book Title | Ember | 20pt | 700 | 1.3 | 0pt | Book detail / library title |
| Subtitle / Author | Ember | 17pt | 500 | 1.4 | 0pt | "by Anthony Doerr" |
| Reading Body | Bitter | 15.5pt | 400 | 1.72 | 0pt | The book text — user-scalable; justified + hyphenated |
| List Title | Ember | 15pt | 500 | 1.4 | 0pt | "Continue reading", row titles |
| Metadata | Ember | 14pt | 400 | 1.4 | 0pt | "38% · 14 min left in chapter" |
| Caption | Ember | 12pt | 500 | 1.4 | 0pt | "Downloaded · 412 pages" |
| Chapter Eyebrow | Ember | 11pt | 700 | 1.0 | 1.5pt | "CHAPTER SEVEN" all-caps |
| Tab Label | Ember | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Drop Cap | Bitter | ~52pt | 700 | 0.82 | 0pt | Optional opening-paragraph cap |
| Footnote / Note | Ember | 13pt | 400 | 1.5 | 0pt | Footnote popover, highlights note |

### Principles
- **The page is typeset, not designed**: reading text is justified, hyphenated, generously leaded (1.72) — it should read like a fine paperback, not a web page.
- **Reading face is the user's**: size, family, line spacing, and margins are user controls — the design must scale gracefully across all of them.
- **Chrome is neutral and quiet**: Amazon Ember at small sizes, no decorative weights — chrome must never compete with the page.
- **One drop-cap maximum**: a single ornamental cap on a chapter's first paragraph; nowhere else.
- **Numerals everywhere**: location, percent, time-left use Ember tabular figures for stable progress display.

## 4. Component Stylings

### Buttons

**Primary CTA ("Read now" / "Buy" / "Download")**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#FF9900` (Amazon Orange)
- Text: `#1A1A1A`, Ember 15pt weight 700
- Padding: 13pt vertical, 26pt horizontal
- Pressed: background `#E88B00` + scale 0.98
- This is the only filled-orange element in chrome

**Secondary / Outline Button ("Download", "Sample")**
- Background: transparent
- Border: 1pt `#6B6B6B` light / `#9A9A9A` dark
- Text: `#1A1A1A` light / `#E8E8E8` dark, Ember 14pt weight 500
- Corner radius: 8pt
- Pressed: background `#F4F2EE` / `#242424`

**Pill Button ("Add to Library", filter chips)**
- Background: `#F4F2EE` / `#242424`
- Border: 1pt divider
- Text: Ember 13pt weight 500
- Corner radius: 500pt

**Text / Link Button ("Sample this book", "See all")**
- Font: Ember 14pt weight 500
- Color: `#1A98FF`
- No underline

### Reading Page (the core atom)

The reading view is Kindle's fundamental surface.
- Background: the active reading theme's page color
- Body: reading serif at user size, justified, hyphenated, 1.72 line-height
- Horizontal margins: user-controlled (Narrow / Standard / Wide) — default ~26pt
- Paragraph spacing: 0 (indent-based, like print) OR user-set block spacing
- Chapter eyebrow + chapter title at chapter starts; optional drop-cap on first paragraph
- Footer whisper: percent + "X min left in chapter" + location, in muted ink at 55% opacity
- Zero chrome while reading; single center-tap restores chrome with a 200ms fade
- Page turn: tap right edge / swipe left → next page (curl or slide per user setting); tap left edge → previous
- Text selection → highlight (4 colors) / note / lookup / translate / search popover

### Aa Typography Panel
- Bottom sheet over the page, 12pt top radius, theme-tinted background
- Rows: Font (segmented list of faces), Size (− / Aa / +), Line Spacing, Margins, Orientation
- **Theme swatches**: 5 round 30pt dots (White / Sepia / Green / Dark / Black); active dot ringed 2pt Amazon Orange with 2pt offset
- Brightness slider (with "Match system" toggle)
- Changes apply live behind the sheet

### Library Cover Cell
- Cover: full-color art, 2:3 aspect, 4pt corner radius, `rgba(0,0,0,0.4) 0 4px 10px` shadow
- Reading progress: a 3pt bar pinned to the cover's bottom edge — `#FF9900` fill on `rgba(255,255,255,0.2)` rail
- Below cover: percent + author in Ember 11pt secondary
- Downloaded badge: small check/cloud glyph top-right
- Grid: 3-up on iPhone, 14pt gutter; long-press → quick actions (Download, Remove, Mark as read)

### Reading Progress Bar
- Rail: 4pt, `#242424` / `#E4E2DD`
- Fill: `#FF9900`
- Caption: Ember 12pt — "38% · ch. 7" / "4h left" / "Finished"
- Also rendered as a thin edge whisper at the bottom of the reading page

### Navigation

**Reader Top Bar (when chrome is shown)**
- Height: 44pt + safe area, theme-tinted translucent
- Leading: back chevron (to Library)
- Trailing: search-in-book, bookmark, X-Ray / contents icons — all 18–20pt, muted theme ink
- Fades out on read; center-tap restores

**Bottom Tab Bar (chrome context)**
- Height: 50pt + safe area
- 4 tabs: Home, Library, Discover, More
- Icon size: 22pt; labels Ember 10pt weight 500
- Active: `#FF9900` (filled glyph) — light; `#C77800` tuned for sepia-tinted chrome
- Inactive: theme/secondary muted ink
- No tint pill — active is glyph fill + orange color

### Input Fields

**Search (Discover / Library)**
- Height: 40pt
- Background: `#F4F2EE` / `#242424`
- Corner radius: 8pt
- Leading `magnifyingglass` 16pt secondary
- Placeholder: "Search the Kindle Store" Ember 14pt secondary
- Focus: 1.5pt `#FF9900` border

**In-Book Search**
- Full-screen sheet; results list with location + snippet (matched term highlighted orange)
- Tap result → jumps to location, briefly flashes the matched line

### Distinctive Components

**Reader Footer Whisper**
- Percent + "X min left in chapter" + location, muted theme ink @ 55%, Ember 11pt
- A 2pt progress hairline above it, orange fill

**Aa Theme Swatch Row**
- 5 round swatches; active ringed in Amazon Orange — the most recognizable Kindle control

**Continue Reading Card (Home)**
- Most-recent book: cover thumbnail + title + "X% · X min left" + a wide orange "Read now"
- Surfaces at the very top of Home

**Highlight & Note Popover**
- On text selection: 4 highlight color chips (yellow/blue/pink/orange-amber), Note, Lookup (dictionary/X-Ray/Wikipedia tabbed), Translate, Search, Share
- Selected text stays highlighted in the chosen color in the page

**X-Ray Panel**
- Bottom sheet listing People / Terms / Images with occurrence counts and sparkline of where they appear in the book

**Word Wise / Reading Ruler**
- Optional inline glosses above hard words; accessibility ruler dims all but the current line

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48
- Chrome content inset: 16pt horizontal
- Reading margins: user-controlled (Narrow ≈ 16pt / Standard ≈ 26pt / Wide ≈ 40pt)
- Library cover gutter: 14pt

### Grid & Container
- iPhone reader: single justified column at the user's margin width
- iPhone library: 3-up cover grid, 14pt gutter
- iPad reader: optional two-page spread in landscape; centered single column in portrait with wider max margins
- iPad library: 5–6-up cover grid

### Whitespace Philosophy
- **The page breathes maximally**: generous margins + 1.72 leading — reading comfort over density
- **Chrome is sparse**: lists are simple rows; no decorative containers
- **Covers carry the color**: library is a quiet grid where the art is the only saturation
- **One accent**: orange appears for CTA / progress / active state and nothing else

### Border Radius Scale
- Square (0pt): The reading page, full-bleed dividers
- Cover (4pt): Library cover art
- Standard (6–8pt): Buttons, search fields, chips
- Comfortable (12pt): Bottom sheets (Aa panel, X-Ray, search)
- Pill (500pt): Filter chips, "Add to Library"
- Circle (50%): Theme swatches, avatars

## 6. Depth & Elevation

Kindle is nearly flat — the reading page has zero elevation by design, and shadow appears only on cover art and floating sheets.

| Level | Treatment | Use |
|-------|-----------|-----|
| Page (Level 0) | No shadow, no border | The reading page — paper has no shadow on itself |
| Cover (Level 1) | `rgba(0,0,0,0.4) 0 4px 10px` | Library cover art (only) |
| Sheet (Level 2) | `rgba(0,0,0,0.18) 0 -4px 24px` | Aa panel, X-Ray, in-book search, highlight popover |
| Scrim | `rgba(0,0,0,0.3)` | Behind sheets and modals |

**Shadow Philosophy**: The reading surface must look like a physical page — flat, matte, shadowless. The only elevated objects are book covers (physical objects cast shadows) and transient sheets (they float over the page and you tap outside to dismiss). On the Black/Dark reader themes shadows are invisible, so sheets gain a 1pt theme-divider border as the elevation cue. Chrome cards never carry shadow — separation is by tone, not depth.

### Motion
- **Chrome fade on book open**: nav + status + tab fade out over 250ms ease-out; reverse on center-tap (200ms ease-in)
- **Page turn**: slide (220ms ease-out) or curl (user setting); tap-edge and swipe both supported; soft haptic optional off by default
- **Aa panel**: slides up 280ms ease-out; theme change applies live behind it with a 150ms cross-tint of background + ink
- **Theme swatch select**: tapped dot's orange ring scales in 1.0→1.15→1.0 over 200ms; page background + ink cross-fade 150ms
- **Library progress bar**: on appear, fills 0→current over 500ms ease-out
- **Continue reading**: cover does a subtle scale-in (1.0 from 0.96, 250ms) on Home appear
- **Highlight commit**: selected range fills with the chosen color over 120ms; soft haptic
- **Sync ("Furthest page read")**: a quiet non-modal banner slides down 250ms — "You're at 38% — sync to furthest read (62%)?"
- **Haptic feedback**: soft impact on highlight commit and theme select; none on page turn by default

## 7. Do's and Don'ts

### Do
- Make the reading page the product — fade ALL chrome out on book open
- Default to Sepia (`#FBF0D9` paper / `#5F4B32` ink) — paper, not screen
- Offer all five reading themes and let the reader's choice override OS appearance
- Set body in a reading serif, justified, hyphenated, 1.72 line-height — it must read like a paperback
- Use Amazon Orange (`#FF9900`) as the ONLY accent — CTA, progress, active-theme ring, selected tab
- Put a thin orange progress bar on every library cover's bottom edge
- Keep cover art full-color; the library is a quiet grid where art is the only saturation
- Respect the reader's font / size / spacing / margin choices globally — design to scale across them
- Use one drop-cap maximum, only on a chapter's opening paragraph
- Keep chrome neutral Amazon Ember on near-black `#1A1A1A`

### Don't
- Don't put cards, borders, or shadows on the reading page — paper is flat
- Don't tie the reading surface to OS dark mode — the reader's theme choice wins
- Don't use pure white ink on the Black theme — dimmed `#C8C8C8` reduces halation
- Don't introduce a second accent color — orange is the only one; no green/blue CTAs
- Don't set reading body in a UI sans by default — the book is serif
- Don't left-align/ragged the reading column by default — Kindle justifies and hyphenates
- Don't keep chrome visible while reading — a single center-tap reveals it; otherwise it's gone
- Don't tint or crop cover art with UI overlays beyond the thin bottom progress bar
- Don't animate the reading surface decoratively — page turn + fade only, quiet and fast
- Don't hard-code one reading font/size — every reader sets their own
- Don't add a Material tint pill to the active tab — it's glyph fill + orange

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Library 3-up at ~100pt covers; reader Standard margin ≈ 22pt |
| iPhone 13/14/15 | 390pt | Standard layout; reader Standard margin ≈ 26pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected by reader top bar when chrome shown |
| iPhone 15/16 Pro Max | 430pt | Library 4-up; wider default reading column |
| iPad (portrait) | 768pt | Single centered reading column, wide max margins; library 5-up |
| iPad (landscape) | 1024pt+ | Optional two-page spread reader; library 6-up |

### Dynamic Type
- The reading body uses the **in-app** size control, not system Dynamic Type — but it must honor it as a floor when "Match system" is on
- Chrome (titles, list rows, captions): full system Dynamic Type scale
- Tab labels, chapter eyebrow, footer whisper: FIXED (layout-sensitive)

### Orientation
- Reader rotates; iPad landscape can show a two-page spread
- Library reflows column count to width
- Aa panel and sheets re-anchor on rotation

### Touch Targets
- Page-turn tap zones: full left third / right two-thirds (or symmetric per setting)
- Center-tap (reveal chrome): center ~40% of the page
- Theme swatch: 30pt glyph, 44pt hit
- Tab bar icons: 22pt glyph, 49pt hit
- Primary CTA: 44pt tall minimum

### Safe Area Handling
- Reader: page content respects safe area; footer whisper sits above the home indicator
- Top bar (when shown) respects Dynamic Island
- Bottom: tab bar + home indicator respected in chrome
- Sides: chrome 16pt inset; reading margins are the user's chosen width

## 9. Agent Prompt Guide

### Quick Color Reference
- Reading default page: `#FBF0D9` (Sepia) / ink `#5F4B32`
- Reading themes: White `#FFFFFF`/`#1A1A1A`, Green `#C5E1C5`/`#33492F`, Dark `#2A2A2A`/`#D8D8D8`, Black `#000000`/`#C8C8C8`
- Brand accent (CTA / progress / active): `#FF9900` (pressed `#E88B00`)
- Chrome black: `#1A1A1A`
- App chrome canvas: `#FFFFFF` light / `#0E0E0E` dark
- Chrome surface: `#F4F2EE` light / `#1A1A1A`–`#242424` dark
- Text primary: `#1A1A1A` light / `#E8E8E8` dark
- Text secondary: `#6B6B6B` light / `#9A9A9A` dark
- Link: `#1A98FF`
- Kindle blue (X-Ray accent): `#4FB3D9`
- Error: `#E0533D`

### Example Component Prompts
- "Build the Kindle reading page in SwiftUI: full-bleed background = active theme page color (default Sepia `#FBF0D9`). A chapter eyebrow 'CHAPTER SEVEN' in Amazon Ember 11pt weight 700 letter-spacing 1.5pt at 55% ink; chapter title in Bitter 24pt weight 700 ink `#5F4B32`; body in Bitter 15.5pt weight 400, line-height 1.72, `.multilineTextAlignment(.justified)` with hyphenation, ink `#5F4B32`. Optional 52pt Bitter drop-cap on the first paragraph. A bottom whisper: percent + 'X min left in chapter' at Ember 11pt 55% ink, with a 2pt orange `#FF9900` progress hairline above it. Zero chrome — a single center-tap fades the nav/tab back in over 200ms."

- "Create the Kindle Aa typography panel: a bottom sheet (12pt top radius, theme-tinted bg) with rows Font / Size (− Aa +) / Line Spacing / Margins, and a theme row of five 30pt round swatches — White `#FFFFFF`, Sepia `#FBF0D9`, Green `#C5E1C5`, Dark `#2A2A2A`, Black `#000000`. The active swatch is ringed 2pt Amazon Orange `#FF9900` with 2pt offset; tapping a swatch scales its ring 1.0→1.15→1.0 over 200ms and cross-fades the page background + ink behind the sheet over 150ms."

- "Render a Kindle library cover cell: full-color cover art at 2:3, 4pt corner radius, `rgba(0,0,0,0.4) 0 4px 10px` shadow. A 3pt reading-progress bar pinned to the cover's bottom edge — `#FF9900` fill on `rgba(255,255,255,0.2)` rail at the read fraction. Below: percent + author in Amazon Ember 11pt `#6B6B6B`. Grid 3-up, 14pt gutter; long-press opens Download / Remove / Mark-as-read."

- "Build the Kindle 'Continue reading' card for Home: leading cover thumbnail (2:3, 4pt radius, shadow), title in Ember 20pt weight 700, '38% · 14 min left in chapter' in Ember 14pt `#6B6B6B`, and a full-width orange `#FF9900` 'Read now' button (8pt radius, text `#1A1A1A` Ember 15pt 700). Card surfaces at the very top of Home; cover scales in 0.96→1.0 over 250ms on appear."

- "Render the Kindle highlight popover: on text selection show 4 round highlight color chips (yellow, blue, pink, orange-amber), then Note, Lookup, Translate, Search, Share. On commit, fill the selected range with the chosen color over 120ms and a soft haptic; the highlight persists in the page text."

- "Build the Kindle bottom tab bar (chrome context): 4 tabs Home / Library / Discover / More, 22pt glyphs, Ember 10pt weight 500 labels. Active = filled glyph in Amazon Orange `#FF9900` (use `#C77800` if chrome is sepia-tinted); inactive = muted secondary ink. No tint pill — color + fill only. Background follows system: `#FFFFFF` light / `#0E0E0E` dark, 0.5pt top divider."

### Iteration Guide
1. The reading page is the product — every pixel of chrome fades out on book open; center-tap restores it
2. Default reading theme is Sepia `#FBF0D9` paper / `#5F4B32` ink — paper, never screen
3. Five reading themes (White/Sepia/Green/Dark/Black) and the reader's choice overrides OS appearance
4. Body is a reading serif (Bitter analog), justified + hyphenated, 1.72 line-height — like a paperback
5. Amazon Orange `#FF9900` is the ONLY accent — CTA, progress fill, active-theme ring, selected tab
6. Every library cover wears a thin orange progress bar on its bottom edge
7. Cover art is the only saturation in chrome; the library is an otherwise quiet grid
8. The reading surface is flat — no cards, borders, or shadows; covers and sheets are the only elevated things
9. Respect the reader's font/size/spacing/margin controls globally — design must scale across all of them
10. Chrome is neutral Amazon Ember on near-black `#1A1A1A`; never add a second accent color
