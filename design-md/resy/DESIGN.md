# Design System Inspiration of Resy (iOS)

## 1. Visual Theme & Atmosphere

Resy's iOS app reads like a curated dining magazine that happens to book tables. The canvas is **pure black** (`#000000`) — Resy is dark-first by design, not as a dark-mode option but as the brand. Black makes full-color restaurant photography and the editorial serif headlines pop the way ink does on a glossy printed page. The whole personality is *taste + access*: editorial restaurant write-ups, a curated "Hit List", and the tension of a hard-to-get reservation. The single brand color — **Resy Red** (`#C73E3A`) — is used with discipline on the "Book Now" CTA, the selected reservation slot, and the active tab, and almost nowhere else.

The two signature UI moments are the **reservation calendar + time-slot picker** and the **editorial restaurant detail page**. The picker is a horizontal date strip (Wed 21 / Thu 22 / Fri 23 …) where the selected day is filled Resy Red, sitting above a grid of time-slot chips. Resy's slot treatment is distinctive: **available slots are outlined in red** (transparent fill, red 1pt border, the time + seating type like "Dining Room" / "Bar" / "Counter"), the recommended/primary slot is **solid red**, and when nothing is available the slot becomes a **dashed amber "Notify" chip** that joins the waitlist. The restaurant detail page is the editorial conversion surface: a full-bleed photo carousel, the restaurant name set in a **high-contrast display serif**, a gold rating ("★ 9.4"), cuisine/price/neighborhood metadata, an italic editorial tagline, the date strip, and the slot grid. Search → restaurant → date → slot → confirm is the spine, and **Notify** (waitlist for sold-out tables) is a first-class parallel path.

The color system around the red is editorial and restrained: an **amber Notify accent** (`#D99A2B`) for waitlist/alerts, a **muted gold** (`#C9A24B`) for ratings and "Hit List" curation, and otherwise pure black, near-black surfaces, and high-contrast white text. There is no decorative color — Resy's "color" is photography and typographic contrast.

Typography is the soul of the brand: a **high-contrast display serif** for restaurant names and editorial titles (Resy's brand voice is a refined didone/transitional serif; **Playfair Display** is the closest free analog) paired with a clean neutral sans for UI, metadata, and slots (**Inter**). On iOS the sans falls back to SF Pro and the serif to New York / Georgia. Hierarchy is magazine-like: a serif display name, an italic serif editorial note, then crisp sans metadata and slot times. The type system should feel like a Condé Nast dining feature, not a generic booking utility.

**Key Characteristics:**
- Pure black canvas (`#000000`) — dark-first by design, not a dark-mode toggle; photography and serif type pop like print
- Editorial display serif (Playfair Display / Resy serif) for restaurant names + titles; clean sans (Inter) for UI/slots
- A single disciplined brand color: Resy Red `#C73E3A` (bright `#E2504B`) for Book, selected slot, active tab
- Reservation date strip + slot grid — selected day solid red; **available slots red-outlined**, primary slot solid red
- Notify (waitlist) as a first-class path — dashed **amber** `#D99A2B` "Notify" chip when sold out
- Editorial restaurant detail — photo carousel + serif name + gold rating + italic tagline + date/slot picker
- Muted gold `#C9A24B` for ratings and the curated "Hit List"
- Seating-type labels on slots ("Dining Room" / "Bar" / "Counter" / "Patio") — Resy books specific seatings
- High typographic contrast does the work of color — restrained, magazine-grade
- Square-ish 8–10pt radii (not pill-soft) — Resy's geometry is crisp and editorial
- Minimal navigation chrome: 5-tab bottom bar — Search / Hit List / Reservations / Notify / Account

## 2. Color Palette & Roles

### Primary (Interactive)
- **Resy Red** (`#C73E3A`): Primary interactive color — "Book Now" button, selected/primary reservation slot, active tab.
- **Resy Red Bright** (`#E2504B`): Active-tab tint and emphasis on pure black, error.
- **Resy Red Pressed** (`#A8302D`): Pressed state for primary buttons and slots.
- **Red Outline** (`#C73E3A` @ 1pt border, transparent fill): Available reservation slots — Resy's signature.

### Canvas & Surfaces (Dark — primary brand)
- **Resy Black** (`#000000`): Primary canvas — pure black, the brand.
- **Surface 1** (`#121212`): Cards, sheets, grouped rows.
- **Surface 2** (`#1C1C1C`): Pressed rows, secondary panels, search field fill.
- **Surface 3** (`#242424`): Elevated chips, hovered states.
- **Divider** (`#262626`): Hairline dividers between rows and around slots.

### Canvas & Surfaces (Light — secondary inversion)
- **Canvas White** (`#FFFFFF`): Light canvas (light mode is supported but black is the brand).
- **Surface Gray** (`#F4F4F4`): Cards, grouped rows.
- **Surface Pressed** (`#EAEAEA`): Pressed rows.
- **Divider Light** (`#E2E2E2`): Hairline dividers.

### Text
- **Text Primary Dark** (`#F5F5F5`): Primary text on black — near-white for editorial contrast.
- **Text Secondary Dark** (`#9E9E9E`): Metadata, captions, seating labels.
- **Text Tertiary Dark** (`#6A6A6A`): Disabled, separators.
- **Text Primary Light** (`#101010`): Primary text on white.
- **Text Secondary Light** (`#6B6B6B`): Secondary on white.

### Brand / Functional Accents

| Role | Dark Mode | Light Mode |
|------|-----------|-----------|
| Notify Amber (waitlist / alerts) | `#D99A2B` | `#B97E16` |
| Hit Gold (rating / curation) | `#C9A24B` | `#A6802F` |
| Available-slot border | `#C73E3A` | `#C73E3A` |
| Confirmed Green | `#4FA773` | `#2F8C56` |

### Semantic
- **Success Green** (`#4FA773` dark / `#2F8C56` light): Reservation confirmed.
- **Error Red** (`#E2504B` dark / `#C73E3A` light): Booking failed, slot just lost.
- **Notify / Waitlist Amber** (`#D99A2B`): On the Notify list, table alert.
- **Info Surface** (`#1C1C1C` dark / `#F4F4F4` light): Neutral informational background.

## 3. Typography Rules

### Font Family
- **Editorial / Display**: a high-contrast transitional/didone serif (Resy's brand voice). **Playfair Display** (by Claus Eggers Sørensen, SIL OFL) is the closest free analog and recommended substitute — used for restaurant names, editorial titles, and feature headlines.
- **UI / Body**: `Inter` (by Rasmus Andersson, SIL OFL) — clean neutral sans for metadata, slots, buttons, navigation.
- **Fallback Stack (serif)**: `'New York', Georgia, 'Times New Roman', serif`
- **Fallback Stack (sans)**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **iOS**: SF Pro / New York are the system fallbacks when Inter / Playfair aren't bundled — preserve the high-contrast editorial tone.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display Title | Playfair Display | 34pt | 800 | 1.15 | -0.5pt | "Tonight in NYC", feature headlines |
| Restaurant Name | Playfair Display | 27pt | 700 | 1.15 | -0.3pt | Detail-page restaurant title |
| Section Header | Inter | 22pt | 700 | 1.3 | -0.2pt | "The Hit List", "Reservations" |
| Card Title | Inter | 18pt | 600 | 1.3 | -0.1pt | Restaurant name in list cards |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Paragraph copy, descriptions |
| Editorial Note | Playfair Display | 15pt | 400 (italic) | 1.55 | 0pt | Italic restaurant tagline |
| Meta | Inter | 14pt | 400 | 1.4 | 0pt | "Italian · $$$ · Williamsburg" |
| Slot Time | Inter | 14pt | 700 | 1.2 | 0pt | "6:30" inside a slot chip |
| Seating Label | Inter | 9pt | 600 | 1.0 | 0.3pt (uppercase) | "DINING ROOM" / "BAR" under time |
| Eyebrow | Inter | 12pt | 700 | 1.0 | 0.8pt (uppercase) | "SELECT A DATE" section eyebrow |
| Button | Inter | 16pt | 700 | 1.0 | 0.2pt | Primary/secondary buttons |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Caption | Inter | 12pt | 500 | 1.4 | 0pt | Fine print, policy text |

### Principles
- **Serif for the name, sans for the system**: the restaurant name and editorial titles are always the high-contrast display serif; everything functional (metadata, slots, buttons, nav) is Inter. This split *is* the brand.
- **Italic serif for editorial voice**: restaurant taglines/notes are italic Playfair — the "magazine writer" voice.
- **Typographic contrast replaces color**: weight and serif/sans contrast carry hierarchy on the black canvas — there is almost no decorative color.
- **Near-white, not pure white**: body text is `#F5F5F5` on black for a refined, non-glaring editorial feel.
- **Dynamic Type first-class**: display titles, restaurant names, body, metadata scale; tab labels, seating labels, slot times stay fixed (the slot grid is layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary Button ("Book Now")**
- Shape: Rounded rectangle, 8pt corner radius (crisp, not pill)
- Background: `#C73E3A` (Resy Red)
- Text: `#FFFFFF`
- Padding: 14pt vertical, 28pt horizontal (sticky/full-width variant: full-width, 16pt vertical)
- Font: Inter 16pt weight 700
- Pressed: background `#A8302D` + scale 0.98

**Secondary / Outline Button ("Add to Hit List")**
- Background: transparent
- Text: `#F5F5F5` dark / `#101010` light
- Border: 1pt `#9E9E9E` dark / `#6B6B6B` light
- Padding: 12pt vertical, 22pt horizontal
- Corner radius: 8pt
- Font: Inter 14pt weight 600
- Pressed: background `#1C1C1C` dark / `#EAEAEA` light

**Notify Button ("Notify Me")**
- Background: transparent
- Text + border: `#D99A2B` (Notify Amber), 1pt border
- Padding: 12pt vertical, 22pt horizontal
- Corner radius: 8pt
- Font: Inter 14pt weight 700
- Pressed: background `rgba(217,154,43,0.12)`

**Text Button ("View full menu" / "See all")**
- Font: Inter 14pt weight 600
- Color: `#F5F5F5` dark / `#101010` light
- Underline, 3pt underline offset (editorial link style)
- Pressed: 60% opacity

### Core Atoms

**Reservation Slot Chip (the core atom — Resy's signature)**
- Shape: rounded rectangle, 10pt corner radius
- Size: flexible width (≥ 70pt) × 56pt tall in a row/grid
- **Available**: transparent fill, 1pt `#C73E3A` border, time in Inter 14pt w700 `#F5F5F5`, seating label below in Inter 9pt w600 uppercase `#9E9E9E`
- **Primary/recommended**: solid `#C73E3A`, no border, white time + 82%-white seating label
- **Notify (sold out)**: transparent fill, 1pt `#262626` **dashed** border, "Notify" + time window in `#D99A2B`
- Pressed: scale 0.97 + soft haptic
- Seating types: "Dining Room", "Bar", "Counter", "Patio", "Chef's Counter"

**Reservation Date Strip**
- Horizontal row of day cells, 60pt tall, 10pt radius, 8pt gap
- Each cell: weekday Inter 10pt w600 uppercase `#9E9E9E` + date number Inter 17pt w700 `#F5F5F5`
- Default: `#121212` fill, 1pt `#262626` border
- Selected: solid `#C73E3A`, white text
- Scrolls horizontally; tap a "calendar" affordance to open a full month picker sheet

**Restaurant Card (list)**
- Layout: full-bleed photo (16:9), serif name overlaid or below, gold rating, meta line, a row of 3–4 slot chips
- Photo corner radius: 8pt
- Name: Playfair Display 18–20pt w700
- Rating: "★ 9.4" gold `#C9A24B` Inter 13pt w700
- Meta: Inter 14pt w400 `#9E9E9E`, dot-separated
- Embedded slot row: 3–4 red-outlined chips + "+N more"
- Divider: 0.5pt `#262626`

**Rating Mark**
- "★ 9.4" — single star glyph + numeric (Resy uses a /10 score), gold `#C9A24B`
- Inter 13pt w700
- Used on cards and detail page; gold is reserved for ratings + Hit List curation

**Editorial Tagline**
- Italic Playfair Display 15pt `#9E9E9E`, 1–2 lines
- The "magazine writer" voice describing the restaurant — a Resy signature

**Notify Chip / Row**
- Pill or row, transparent, 1pt `#D99A2B` border, leading 🔔 glyph
- "Notify when available" / "On the Notify list" Inter 13pt w700 `#D99A2B`
- The waitlist primitive — appears inline in the slot grid and as a dedicated tab

**Seating-Type Tag**
- Small uppercase label under a slot time: "DINING ROOM" / "BAR" / "COUNTER"
- Inter 9pt w600, `#9E9E9E` (82% white when slot is solid red)
- Resy books specific seatings, so the type is always shown

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `#000000` at 96% with blur, 0.5pt top border `#262626`
- Tabs (5): Search, Hit List, Reservations, Notify, Account
- Icon size: 21pt
- Active color: `#C73E3A` (uses bright `#E2504B` for clear contrast on pure black)
- Inactive: `#6A6A6A`
- Labels: Inter 10pt w600, always shown

**Top Nav (Restaurant detail)**
- Height: 44pt + safe area, transparent over hero photo (scrolls to opaque black)
- Leading: back chevron in a 32pt translucent dark circle over photo
- Trailing: heart (Hit List) + share, same translucent circle treatment
- Title: restaurant name appears Playfair Display 18pt w700 when scrolled past hero

**Search / Discover**
- Editorial home: large serif feature headline, curated collection rows ("The Hit List", "Booking up fast", "New on Resy"), each a horizontal card carousel

### Input Fields

**Search Bar ("Restaurants, cuisines, locations")**
- Height: 46pt
- Background: `#1C1C1C` dark / `#F4F4F4` light
- Border: none
- Corner radius: 8pt
- Leading `magnifyingglass` 16pt `#9E9E9E`
- Placeholder: Inter 14pt w500 `#9E9E9E`
- Focus: 1pt `#C73E3A` border

**Guest / Date / Time Selector**
- Inline pill/segment row above slots: "2 guests · Thu May 22 · Dinner"
- Tapping each opens a bottom sheet (guests stepper, calendar, daypart toggle)
- Red "Apply" button pinned bottom of each sheet

**Calendar Sheet**
- Month grid, selected date filled `#C73E3A` circle, unavailable dates dimmed
- Today marked with a thin red ring
- Red "Apply" button pinned bottom

### Distinctive Components

**Reservation Slot Grid (red-outline system)** — the product's heartbeat; available = red-outlined, primary = solid red, sold-out = dashed amber Notify (see Core Atoms).

**Editorial Restaurant Detail**
- Full-bleed photo carousel (with page dots), 220–260pt tall, translucent back/heart/share circles
- Serif restaurant name + gold "★ 9.4" + meta + italic editorial tagline
- "SELECT A DATE" eyebrow + date strip + guest/date/daypart context line + slot grid

**Notify (Waitlist) Surface**
- Dedicated Notify tab listing restaurants you're waitlisted for
- Each row: restaurant (serif name), requested date/time window, amber "On the list" status
- Push when a table opens; tapping books instantly

**The Hit List**
- Resy's signature curated/saved collection — editorial tile grid of bookmarked restaurants
- Gold accent on the bookmark/curation glyph

**Guest / Daypart Context Line**
- "2 guests · Thu May 22 · Dinner" above the slot grid
- Each token tappable to change the booking parameters

**Confirmation Card**
- Post-booking: serif restaurant name, date/time, party size, seating type, confirmation code
- Green confirmed header; "Add to Calendar" / "Modify" / "Cancel" / "Add to Hit List" actions
- Cancellation policy in caption text

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 9, 12, 16, 18, 20, 22, 24, 32, 40
- Slot chip gap: 9pt; date cell gap: 8pt
- Section top margin: 22pt
- Screen content inset: 18–20pt horizontal (generous editorial margins)

### Grid & Container
- iPhone: content at 18–20pt horizontal insets; hero photo full-bleed
- iPad: content at max-width 720pt centered, 32pt insets; restaurant list 2-column editorial grid
- Restaurant list: single column of editorial cards; slot chips wrap 4-up
- Slot grid: 4 chips per row on phone, wraps; equal flex widths

### Whitespace Philosophy
- **Black is the whitespace**: pure black gives photography and serif type room to breathe like a magazine spread
- **Editorial margins**: generous 18–20pt side insets and 22pt section spacing — never cramped
- **The slot grid is the focal point**: a clear eyebrow + date strip + context line lead the eye to the times
- **Restrained dividers**: 0.5pt `#262626` hairlines; only photos and slot chips have visible containers

### Border Radius Scale
- Square (0pt): Full-bleed hero photos, dividers
- Hairline (2pt): Tiny tags, the rating mark container
- Subtle (4pt): Small badges, inline chips
- Standard (8pt): Buttons, search bar, photo cards
- Slot (10pt): Reservation slot chips, date cells
- Sheet (16pt): Bottom-sheet top corners, modals
- Pill (999pt): Notify chip, daypart toggle only
- Circle (50%): Nav circles, avatars, selected-date marker

## 6. Depth & Elevation

Resy is editorial-flat. Depth comes from the pure-black canvas and photography, not heavy shadows.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | All content on black — cards, slots, rows |
| Lifted (Level 1) | `rgba(0,0,0,0.5) 0 2px 12px` + 0.5pt `#262626` border | Sheets, floating panels (border is the real cue) |
| Sheet (Level 2) | `rgba(0,0,0,0.6) 0 -4px 24px` + 1pt `#262626` top border | Calendar / guest / Notify sheets |
| Modal Overlay | `rgba(0,0,0,0.7)` | Dim behind sheets and modals |

**Shadow Philosophy**: on a pure-black canvas, drop-shadows are nearly invisible — so Resy uses **hairline borders** (`#262626`) as the primary elevation cue. Sheets and floating panels get a subtle shadow plus a 0.5–1pt border so they read as layered above the black. Photography and serif contrast carry visual weight; the UI itself stays radically flat and editorial. In the light inversion, a faint neutral shadow returns for cards/sheets.

### Motion
- **Book**: tapping the red CTA shows an inline spinner then a 300ms push to the confirmation card; success haptic on confirm
- **Slot select**: an available (outlined) slot animates its fill to solid red 150ms ease-out + scale 0.97 bounce + soft haptic; the Book button enables/updates
- **Date strip**: selecting a day fills it red 150ms; the slot grid cross-fades to that day's availability
- **Slot grid load**: chips fade-in + 4pt slide-up staggered 30ms, 200ms ease-out
- **Notify toggle**: the dashed slot/chip fills amber 150ms + a soft haptic; "On the list" confirms
- **Restaurant detail open**: hero photo shared-element-style expand, 300ms (iOS push); serif name fades up
- **Sheet present**: guest/calendar sheets slide up 300ms ease-out; backdrop dims to 0.7
- **Carousel**: photo pager swipes 1:1 with finger; page dots cross-fade
- **Haptic feedback**: soft impact on slot select, date select, Notify toggle, guest stepper; success on reservation confirmed

## 7. Do's and Don'ts

### Do
- Use pure black (`#000000`) as the canvas — Resy is dark-first by design, this is the brand
- Set restaurant names + editorial titles in a high-contrast display serif (Playfair Display / Resy serif)
- Use a clean sans (Inter) for everything functional — metadata, slots, buttons, nav
- Keep Resy Red (`#C73E3A`, bright `#E2504B`) as the single reservation color — Book, selected slot, active tab
- Render available slots as **red-outlined** chips and the primary slot as **solid red** — this is Resy's signature
- Show the seating type ("Dining Room" / "Bar" / "Counter") under every slot time
- Use dashed **amber** (`#D99A2B`) Notify chips when a slot is sold out; make Notify a first-class path
- Use italic serif for the editorial restaurant tagline — the magazine voice
- Use muted gold (`#C9A24B`) for the rating mark and the Hit List only
- Use hairline borders (`#262626`) as the elevation cue — shadows are invisible on black
- Use crisp 8–10pt radii — Resy's geometry is editorial, not pill-soft

### Don't
- Don't make the app light-first — black is the brand; the light inversion is secondary
- Don't set restaurant names in a sans — the serif name is the brand voice
- Don't introduce a second reservation color — red is the only booking color
- Don't fill available slots solid by default — available = outlined, primary = solid (the contrast is the signal)
- Don't omit the seating-type label — Resy books specific seatings, the type matters
- Don't use a generic gray for Notify — amber `#D99A2B` is the dedicated waitlist color
- Don't use pure white (`#FFFFFF`) for body text on black — `#F5F5F5` near-white is the editorial choice
- Don't add decorative color — typographic + photographic contrast carries hierarchy
- Don't use pill-soft buttons — Resy buttons are crisp 8pt rectangles
- Don't rely on drop-shadows on black — use hairline borders for layering
- Don't animate aggressively — motion is restrained 150–300ms ease-out

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Hero photo 200pt; 3 slot chips per row + "+N more"; 5 date cells visible |
| iPhone 13/14/15 | 390pt | Standard layout; 4 slot chips per row |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in transparent top nav |
| iPhone 15/16 Pro Max | 430pt | Hero photo 260pt; 5 slot chips per row; serif name 30pt |
| iPad (portrait) | 768pt | 2-column editorial restaurant grid; detail in split (photo + booking panel) |
| iPad (landscape) | 1024pt+ | List + detail master-detail; slot grid 6-up |

### Dynamic Type
- Display titles, restaurant names (serif), body, metadata: full scale
- Tab labels, seating labels, slot-time text: FIXED (the slot grid is layout-sensitive)
- Slot chip minimum width preserved; chips wrap to more rows at large scales; serif scales with reading size

### Orientation
- All screens support rotation
- iPad split: editorial restaurant list + detail/booking panel side-by-side
- Hero photo carousel rotates freely; slot grid reflows

### Touch Targets
- Reservation slot chip: ≥ 70pt × 56pt (full chip tappable)
- Date cell: ≥ 56pt wide × 60pt tall (full cell tappable)
- Tab bar icons: 21pt glyph, 44pt hit area
- Guest/date context tokens: 36pt+ tap targets
- Primary button: 48pt tall minimum

### Safe Area Handling
- Top: transparent nav respects safe area and Dynamic Island over hero photo
- Bottom: tab bar and any sticky Book button respect home indicator
- Keyboard: search and guest entry scroll above keyboard; sheets sit above keyboard
- Sides: 18–20pt editorial content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (brand): `#000000` (pure black, dark-first)
- Surface 1: `#121212`
- Surface 2: `#1C1C1C`
- Divider: `#262626`
- Canvas (light inversion): `#FFFFFF`
- Text primary: `#F5F5F5` dark / `#101010` light
- Text secondary: `#9E9E9E` dark / `#6B6B6B` light
- Resy Red (primary): `#C73E3A`
- Resy Red Bright (active tab / emphasis on black): `#E2504B`
- Resy Red Pressed: `#A8302D`
- Available-slot border: `#C73E3A` (1pt, transparent fill)
- Notify Amber (waitlist): `#D99A2B`
- Hit Gold (rating / Hit List): `#C9A24B`
- Confirmed green: `#4FA773`
- Error: `#E2504B` dark / `#C73E3A` light

### Example Component Prompts
- "Build the Resy reservation slot grid in SwiftUI on a pure-black background. A wrapping row of rounded-rect chips (10pt corner radius, ≥ 70pt wide, 56pt tall, 9pt gap). Available slot: transparent fill, 1pt `#C73E3A` border, time in Inter 14pt w700 `#F5F5F5`, seating label below in Inter 9pt w600 uppercase `#9E9E9E` ('DINING ROOM'). Primary slot: solid `#C73E3A`, no border, white time + 82%-white seating label. Sold-out slot: transparent, 1pt `#262626` *dashed* border, 'Notify' + time window in `#D99A2B`. Tap available → fill animates to solid red 150ms + scale 0.97 bounce + soft haptic."

- "Create the Resy editorial restaurant detail page: full-bleed photo carousel 240pt tall with page dots and 32pt translucent dark back/heart/share circles. Below on `#000000`: restaurant name in Playfair Display 27pt w700 `#F5F5F5`; a meta row '★ 9.4' in gold `#C9A24B` Inter 13pt w700, then 'Italian · $$$ · Williamsburg' Inter 14pt `#9E9E9E'; an italic editorial tagline in Playfair Display 15pt italic `#9E9E9E`. Then 'SELECT A DATE' eyebrow Inter 12pt w700 uppercase `#9E9E9E`, the date strip, a context line '2 guests · Thu May 22 · Dinner', the slot grid."

- "Render the Resy date strip: a horizontal row of 60pt-tall cells (10pt radius, 8pt gap). Each cell: weekday Inter 10pt w600 uppercase `#9E9E9E` over date number Inter 17pt w700 `#F5F5F5`, fill `#121212`, 1pt `#262626` border. Selected day: solid `#C73E3A`, white text. Scrolls horizontally; a trailing calendar icon opens a month sheet (16pt top radius, selected date a filled red circle)."

- "Build the Resy Notify chip: a pill (or slot variant) with transparent fill, 1pt `#D99A2B` border, a leading 🔔 glyph, text 'Notify when available' Inter 13pt w700 `#D99A2B`. Tapping fills it amber 150ms + soft haptic and switches to 'On the list'. Notify is a first-class waitlist path with its own bottom tab."

- "Build the Resy primary button: crisp rounded rectangle (8pt radius — NOT pill), background `#C73E3A`, white text Inter 16pt w700, 14pt vertical / 28pt horizontal padding. Pressed: background `#A8302D` + scale 0.98 + soft haptic."

- "Render the Resy bottom tab bar on pure black: 52pt + safe area, background `#000000` at 96% with blur, 0.5pt top border `#262626`. 5 tabs — Search, Hit List, Reservations, Notify, Account — 21pt icons, active `#E2504B` (bright red for contrast on black) solid fill, inactive `#6A6A6A`, Inter 10pt w600 labels always shown."

### Iteration Guide
1. Canvas is pure black `#000000` — dark-first by design, this IS the brand (light is a secondary inversion)
2. Restaurant names + editorial titles are a high-contrast display serif (Playfair Display); UI is Inter — the split is the brand
3. Resy Red `#C73E3A` (bright `#E2504B` on black) is the ONLY reservation color — Book, selected slot, active tab
4. Available slots are red-OUTLINED, the primary slot is SOLID red — the contrast is the signal (never fill all slots solid)
5. Always show the seating type ("Dining Room" / "Bar" / "Counter") under the slot time — Resy books specific seatings
6. Sold-out slots become dashed **amber** `#D99A2B` Notify chips — Notify (waitlist) is a first-class path with its own tab
7. Muted gold `#C9A24B` is the rating mark ("★ 9.4", /10) and the Hit List only
8. The booking spine is search → editorial detail (carousel + serif name + tagline) → date strip → slot → confirm
9. Use hairline `#262626` borders for elevation — drop-shadows are invisible on pure black; geometry is crisp 8–10pt
10. Motion is restrained 150–300ms ease-out; soft haptics on slot/date/Notify select, success on confirm
