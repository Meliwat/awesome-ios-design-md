# Design System Inspiration of OpenTable (iOS)

## 1. Visual Theme & Atmosphere

OpenTable's iOS app is a clean, confident restaurant-reservation utility. The canvas is bright white (`#FFFFFF`), the chrome is minimal, and the single brand color — **OpenTable Red** (`#DA3743`) — is used with discipline: it appears on the primary "Reserve Now" button, the selected time-slot chip, the active tab, and almost nowhere else. Everything is engineered to move the diner from "I want to eat out" to a confirmed booking in as few taps as possible. The product's whole personality is *trust + speed*: real review stars, "Booked N times today" social proof, and a grid of tappable reservation times that turn a restaurant page into a one-tap booking.

The two signature UI moments are the **reservation time-slot grid** and the **restaurant detail page**. The time-slot grid is the product's heartbeat: a horizontal row (or grid) of pill/rounded-rect chips — `6:45`, `7:00`, `7:15`, `7:30` — each optionally annotated with a teal "+1,000 Points" reward for off-peak times. The selected/recommended slot is filled OpenTable Red; the rest are neutral surface with a hairline border. The restaurant detail page is the conversion surface: a full-bleed hero photo, the restaurant name in a heavy display weight, a gold star rating + review count, cuisine/price/neighborhood metadata, "Booked 38 times today" social proof, the time-slot grid, a party-size + date selector, and a sticky red "Reserve for 7:00 PM" bar pinned to the bottom. Search → restaurant list → restaurant detail → time slot → confirm is the spine.

The color system around the red is small and purposeful: a **gold review star** (`#E8A33D`) for ratings, a **teal Points accent** (`#1F8A8A`) for the OpenTable Dining Rewards / loyalty points program, and a **diner green** (`#2FA86A`) for positive availability and "Booked" social proof. Backgrounds are clean white with subtle gray surfaces; there is no decorative color. The loyalty system uses teal as "earn points / 1,000-point slot" and the gold star purely for restaurant ratings.

Typography is a clean, friendly grotesque sans (OpenTable uses a custom "OpenTable Sans" family; **Inter** is the closest free analog) with confident heavy weights for restaurant names and screen titles. On iOS it falls back to SF Pro. Hierarchy is utility-forward: large bold screen titles, a heavy restaurant name, readable metadata, prominent slot times, and a clear sticky CTA. The type system stays trustworthy and legible — it should read like a polished booking utility, not an editorial magazine.

**Key Characteristics:**
- Bright white canvas (`#FFFFFF`) light / neutral charcoal (`#121212`) dark — clean, minimal chrome
- A single disciplined brand color: OpenTable Red `#DA3743` (bright `#F2545B`) for Reserve, selected slot, active tab
- Reservation time-slot grid — rounded chips (`6:45` / `7:00` / `7:15`) with selected slot filled red — the product's heartbeat
- Restaurant detail page — hero photo + heavy name + gold stars + social proof + slot grid + sticky Reserve bar
- Gold review star `#E8A33D` for ratings; teal Points accent `#1F8A8A` for Dining Rewards; diner green `#2FA86A` for availability/social proof
- "Booked N times today" social-proof pill — trust + urgency
- Party-size + date selector chips as the booking parameters
- Sticky red "Reserve for {time}" bar pinned to the bottom of restaurant detail
- Clean grotesque sans (OpenTable Sans / Inter) with heavy restaurant names
- No decorative color — every hue carries a function (book / rate / earn / available)
- Minimal navigation chrome: 5-tab bottom bar — Discover / Saved / Reservations / Rewards / Profile

## 2. Color Palette & Roles

### Primary (Interactive)
- **OpenTable Red** (`#DA3743`): Primary interactive color — "Reserve Now" button, selected/recommended time slot, active tab.
- **OpenTable Red Bright** (`#F2545B`): Dark-mode CTA / emphasis variant, active-tab tint on dark.
- **OpenTable Red Pressed** (`#B92C37`): Pressed state for primary buttons and slots.
- **Red Tint Surface** (`#FCEBEC`): 8% red wash behind selected filter rows / focused booking fields (light).

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray** (`#F6F6F7`): Card backgrounds, grouped rows, search field fill.
- **Surface Raised** (`#FBFBFC`): Subtle elevated panels.
- **Surface Pressed** (`#ECECEE`): Pressed/selected rows.
- **Divider** (`#E4E4E7`): Hairline dividers between list rows and around slot chips.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#121212`): Primary dark canvas — clean neutral charcoal.
- **Dark Surface 1** (`#1C1C1E`): Cards, grouped rows, elevated surfaces.
- **Dark Surface 2** (`#262629`): Pressed rows, unselected slot chips.
- **Dark Divider** (`#2E2E31`): Hairline dividers.

### Text
- **Text Primary Light** (`#1A1A1C`): Primary body text — near-black neutral.
- **Text Secondary Light** (`#6B6B70`): Metadata, cuisine/price, captions.
- **Text Tertiary Light** (`#9A9A9F`): Disabled, very low-emphasis.
- **Text Primary Dark** (`#EDEDED`): Primary body on dark.
- **Text Secondary Dark** (`#A2A2A6`): Secondary on dark.
- **Text Tertiary Dark** (`#6E6E73`): Disabled on dark.

### Brand / Functional Accents

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Gold Review Star | `#E8A33D` | `#E8A33D` |
| Points Teal (Dining Rewards) | `#1F8A8A` | `#2FA8A8` |
| Diner Green (availability / social proof) | `#2FA86A` | `#3FBF7C` |
| Notify / Waitlist Amber | `#E08A2F` | `#F0A04A` |

### Semantic
- **Success Green** (`#2FA86A` light / `#3FBF7C` dark): Reservation confirmed, table available.
- **Error Red** (`#D6342F` light / `#F2545B` dark): Booking failed, no availability.
- **Warning Amber** (`#E08A2F`): Almost fully booked, waitlist only.
- **Info Teal Tint** (`#E4F2F2` light / `#0F2424` dark): Points-earned / rewards background.

## 3. Typography Rules

### Font Family
- **Brand / UI**: `OpenTable Sans` (OpenTable's custom grotesque sans) — clean, friendly, confident. **Inter** (by Rasmus Andersson, SIL OFL) is the closest free analog and the recommended substitute.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **iOS**: SF Pro is the system fallback when OpenTable Sans / Inter aren't bundled — keep the clean, trustworthy tone.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Inter | 32pt | 800 | 1.2 | -0.5pt | "Discover", "Reservations" |
| Restaurant Name | Inter | 26pt | 800 | 1.2 | -0.4pt | Detail-page restaurant title |
| Section Header | Inter | 22pt | 700 | 1.3 | -0.3pt | "Available tonight", "Select a time" |
| Card / List Title | Inter | 18pt | 700 | 1.3 | -0.2pt | Restaurant card name in list |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Descriptions, paragraph copy |
| List Subtitle | Inter | 15pt | 600 | 1.35 | 0pt | "Booked 38 times today" |
| Meta | Inter | 14pt | 400 | 1.4 | 0pt | "Steakhouse · $$$$ · Downtown" |
| Slot Time | Inter | 14pt | 700 | 1.2 | 0pt | "7:00" inside time-slot chip |
| Points Tag | Inter | 12pt | 700 | 1.0 | 0.3pt | "+1,000 POINTS" reward annotation |
| Button | Inter | 16pt | 700 | 1.0 | 0pt | Primary/secondary buttons |
| Pill / Chip | Inter | 13pt | 600 | 1.0 | 0.1pt | Filter chips, party-size, date |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Caption | Inter | 12pt | 500 | 1.4 | 0pt | Fine print, policy text |

### Principles
- **Utility-forward hierarchy**: the restaurant name and the slot times are the boldest elements — the user is here to pick a place and a time.
- **One family, many weights**: a single grotesque sans (OpenTable Sans / Inter) at 400 / 500 / 600 / 700 / 800 carries the whole product — body regular, subtitles semibold, names and CTAs bold/extrabold.
- **Slot times read big**: time-slot chips use a heavy 14pt so a grid of times is instantly scannable.
- **Neutral near-black text**: body text is `#1A1A1C`, clean and high-contrast — this is a trust utility.
- **Dynamic Type first-class**: screen titles, restaurant names, body, metadata scale; tab labels, points tags, slot times stay fixed (the slot grid is layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary Button ("Reserve Now" / "Reserve for 7:00 PM")**
- Shape: Fully rounded pill, 999pt corner radius
- Background: `#DA3743` (OpenTable Red) light / `#F2545B` dark
- Text: `#FFFFFF`
- Padding: 14pt vertical, 28pt horizontal (sticky variant: full-width, 16pt vertical, 50pt tall)
- Font: Inter 16pt weight 700
- Pressed: background `#B92C37` + scale 0.98
- Sticky variant: full-width, pinned bottom above tab bar, "Reserve for {time}"

**Secondary / Outline Button ("Add to Favorites" / "Notify Me")**
- Background: transparent
- Text: `#1A1A1C` light / `#EDEDED` dark
- Border: 1pt `#6B6B70` light / `#A2A2A6` dark
- Padding: 12pt vertical, 22pt horizontal
- Corner radius: 999pt (pill)
- Font: Inter 14pt weight 600
- Pressed: background `#F6F6F7` light / `#262629` dark

**Pill / Filter Button (cuisine, "Outdoor", party-size, date)**
- Background: `#F6F6F7` light / `#262629` dark (selected: `#FCEBEC` / red border)
- Text: `#1A1A1C` / `#EDEDED` (selected: `#DA3743` / `#F2545B`)
- Border: 1pt `#E4E4E7` / `#2E2E31` (selected: 1.5pt `#DA3743`)
- Padding: 10pt vertical, 18pt horizontal
- Corner radius: 999pt
- Font: Inter 13pt weight 600

**Text Button ("See all 412 reviews" / "View menu")**
- Font: Inter 14pt weight 600
- Color: `#DA3743` light / `#F2545B` dark
- No underline; chevron `›` optional trailing
- Pressed: 60% opacity

### Core Atoms

**Reservation Time-Slot Chip (the core atom)**
- Shape: rounded rectangle, 10pt corner radius
- Size: flexible width (≥ 64pt) × 52pt tall in a grid/row
- Default: background `#F6F6F7` / `#262629`, 1pt `#E4E4E7` / `#2E2E31` border
- Selected/recommended: background `#DA3743` / `#F2545B`, no border, white text
- Time text: Inter 14pt w700, centered
- Optional points annotation: "+1,000" Inter 9–12pt w700 in teal `#1F8A8A` (white at 85% when selected) below the time
- Pressed: scale 0.97 + soft haptic

**Restaurant Card (list)**
- Layout: leading photo (full-width hero on cards, or thumbnail in compact list), name, gold-star rating + review count, cuisine/price/neighborhood meta, a row of 3–4 time-slot chips
- Photo corner radius: 14pt
- Name: Inter 18pt w700
- Star rating: gold `#E8A33D` filled stars + numeric (e.g. "4.7") + "(412)" in `#6B6B70`
- Meta: Inter 14pt w400 `#6B6B70`, dot-separated
- Embedded slot row: 3–4 chips, "+N more"
- Divider: 0.5pt `#E4E4E7`

**Star Rating**
- 5 stars, filled `#E8A33D`, partial fill for fractional
- Trailing numeric rating Inter 14pt w600 + review count "(412)" Inter 14pt w400 `#6B6B70`
- Used on cards and detail page only — gold is reserved for ratings

**Social-Proof Pill ("Booked 38 times today")**
- Pill, background `rgba(47,168,106,0.16)` (diner-green tint), text `#2FA86A`
- Leading 🔥 / flame glyph for urgency
- Inter 11pt w700
- Signals popularity + urgency on the detail page

**Party-Size / Date Selector Chip**
- Pill, `#F6F6F7` / `#262629`, 1pt border
- Leading icon (person.2 / calendar) `#6B6B70`
- Label: "2 people" / "May 21" Inter 13pt w600
- Tap opens a stepper sheet (party size) or calendar (date)

**Points / Rewards Chip**
- Pill, background `rgba(31,138,138,0.16)`, 1pt `rgba(31,138,138,0.4)` border
- Leading ★ glyph teal
- Text: "1,000 Dining Points" Inter 13pt w700 `#1F8A8A`
- Used for OpenTable Dining Rewards balance and 1,000-point slot rewards

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `#FFFFFF` / `#121212` at 94% with blur, 0.5pt top border `#E4E4E7` / `#2E2E31`
- Tabs (5): Discover, Saved, Reservations, Rewards, Profile
- Icon size: 21pt
- Active color: `#DA3743` light / `#F2545B` dark (icon fills solid)
- Inactive: `#9A9A9F` light / `#6E6E73` dark
- Labels: Inter 10pt w600, always shown

**Top Nav (Restaurant detail)**
- Height: 44pt + safe area, transparent over hero photo (scrolls to opaque white/charcoal)
- Leading: back chevron in a 32pt translucent dark circle over photo
- Trailing: heart (save) + share, same translucent circle treatment
- Title: restaurant name appears Inter 17pt w700 when scrolled past hero

**Search / Filter Bar (Discover)**
- Full-width pill search field (see Input Fields) + horizontal filter chip row (cuisine, time, party, "Outdoor", "Top rated")

### Input Fields

**Search Bar ("Date · Time · 2 people" / "Location, restaurant, cuisine")**
- Height: 48pt
- Background: `#F6F6F7` / `#1C1C1E`
- Border: 0.5pt `#E4E4E7` / `#2E2E31`
- Corner radius: 999pt (pill)
- Leading `magnifyingglass` 16pt `#6B6B70` / `#A2A2A6`
- Placeholder/value: Inter 14pt w500 `#6B6B70` / `#A2A2A6`
- Focus: 1.5pt `#DA3743` border

**Party-Size Stepper Sheet**
- Bottom sheet with − / + stepper, count Inter 22pt w700 centered
- Quick chips: 1, 2, 3, 4, 5, 6, "7+"
- Red "Apply" pill pinned bottom

**Date / Time Picker Sheet**
- Calendar grid (selected date filled red circle) + time wheel
- Today highlighted; unavailable dates dimmed
- Red "Apply" pill pinned bottom

### Distinctive Components

**Reservation Time-Slot Grid** — the product's heartbeat; a row/grid of rounded chips with the recommended slot filled red and teal points annotations (see Core Atoms).

**Restaurant Detail Page**
- Full-bleed hero photo (carousel), 168–220pt tall, translucent back/save/share circles
- Heavy restaurant name + gold-star rating + meta + social-proof pill
- "Select a time" header + date/party context line + slot grid
- Party-size + date selector chips
- Sticky red "Reserve for {time}" bar pinned bottom

**Notify Me / Waitlist Row**
- When no slots are available: an amber `#E08A2F` "Notify me if a table opens" row with a bell glyph
- Replaces the slot grid; tapping joins the waitlist

**Booked Social Proof + Recently Viewed**
- "Booked N times today" pill (diner-green tint) drives urgency
- Detail page also surfaces "Recently viewed" restaurant carousel

**Dining Rewards Points Surface**
- Rewards tab shows accumulated Dining Points balance in a teal hero
- 1,000-point slots are flagged inline in the slot grid with "+1,000" teal annotation
- "Redeem for a $X Dining Reward" rows

**Confirmation Card**
- Post-booking: a card with restaurant name, date/time, party size, confirmation number, "Add to Calendar" + "Modify" + "Cancel" actions
- Green check header for confirmed state

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 9, 12, 14, 16, 20, 24, 28, 32, 40
- Slot chip gap: 9pt (tight grid so multiple times fit a row)
- Section top margin: 20pt
- Screen content inset: 16–18pt horizontal

### Grid & Container
- iPhone: content at 16pt horizontal insets; hero photos full-bleed
- iPad: content at max-width 720pt centered, 24pt insets; restaurant list in 2-column grid
- Restaurant list: single column of cards; slot chips wrap in a 4-up row
- Slot grid: 4 chips per row on phone, wraps; equal flex widths

### Whitespace Philosophy
- **The slot grid is the focal point**: it gets clear space above ("Select a time" + context line) and a tight, scannable chip layout
- **Photography leads the detail page**: a generous hero photo sits above all booking controls
- **The sticky Reserve bar never crowds**: pinned with 12–16pt padding above the tab bar, always reachable
- **Clean dividers, not boxes**: list rows separated by 0.5pt hairlines; only photos and slot chips have visible containers

### Border Radius Scale
- Square (0pt): Full-bleed hero photos, dividers
- Subtle (4pt): Small badges, inline tags
- Standard (8–10pt): Time-slot chips, thumbnails
- Comfortable (14pt): Restaurant photo cards
- Sheet (20pt): Bottom-sheet top corners, modals
- Pill (999pt): Buttons, filter chips, search bar, points chip, party/date selectors
- Circle (50%): Nav circles, avatars, selected-date marker

## 6. Depth & Elevation

OpenTable uses restrained, neutral elevation — clean shadows that lift cards and the sticky CTA without visual noise.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, page content, dividers |
| Raised (Level 1) | `rgba(0,0,0,0.05) 0 2px 8px` | Restaurant cards, slot grid container |
| Floating (Level 2) | `rgba(0,0,0,0.10) 0 -2px 16px` | Sticky Reserve bar |
| Sheet (Level 3) | `rgba(0,0,0,0.14) 0 -4px 24px` | Party/date sheets, filter sheet |
| Modal Overlay | `rgba(0,0,0,0.4)` | Dim behind sheets and modals |

**Shadow Philosophy**: shadows are neutral gray and subtle — OpenTable is a clean utility, not a skeuomorphic app. Cards get a light raise so the list reads as discrete results; the sticky Reserve bar gets a top shadow so it clearly floats above the scrolling content and stays the obvious next action. On dark mode, shadows nearly vanish, so a 0.5pt `#2E2E31` top divider becomes the elevation cue on the sticky bar and sheets.

### Motion
- **Reserve**: tapping the red CTA shows an inline spinner then a 300ms push to the confirmation card; success haptic on confirm
- **Slot select**: tapping a slot animates its fill to red 150ms ease-out + scale 0.97 bounce-back + soft haptic; the Reserve bar label updates to the chosen time
- **Slot grid load**: chips fade-in + 4pt slide-up staggered 30ms each, 200ms ease-out
- **Filter chip toggle**: border/fill cross-fades 150ms; result list cross-fades
- **Restaurant detail open**: hero photo shared-element-style expand, 300ms (iOS push)
- **Party/date sheet**: slides up 300ms ease-out; backdrop dims simultaneously
- **Search focus**: search pill expands and reveals recent searches 200ms ease-out
- **Pull to refresh (Discover)**: OpenTable mark spinner
- **Haptic feedback**: soft impact on slot select, filter toggle, party stepper; success on reservation confirmed

## 7. Do's and Don'ts

### Do
- Use bright white (`#FFFFFF`) in light, clean charcoal (`#121212`) in dark — minimal chrome
- Keep OpenTable Red (`#DA3743`, bright `#F2545B`) as the single booking color — Reserve, selected slot, active tab
- Make the time-slot grid the focal point of the restaurant page — it's the product's heartbeat
- Fill the selected/recommended slot solid red; keep the rest neutral with a hairline border
- Annotate 1,000-point slots with a teal "+1,000" — it's the rewards hook for off-peak times
- Use the gold star (`#E8A33D`) only for review ratings
- Use the teal accent (`#1F8A8A`) only for Dining Rewards / points
- Show "Booked N times today" social proof for trust + urgency
- Pin a sticky red "Reserve for {time}" bar on the detail page
- Use one grotesque sans (OpenTable Sans / Inter) with heavy restaurant names and CTAs
- Use fully-rounded pills for buttons, filters, and the search bar

### Don't
- Don't introduce a second brand action color — red is the only booking color
- Don't use decorative color — every hue must carry a function (book / rate / earn / available)
- Don't use the gold star for anything but ratings, or teal for anything but points
- Don't make the slot grid secondary — never bury the times below the fold without context
- Don't use pure black (`#000000`) for body text — `#1A1A1C` neutral near-black is correct
- Don't let the red CTA disappear on dark — use `#F2545B` so it stays legible on `#121212`
- Don't add heavy borders around list rows — use 0.5pt hairlines; only photos/slots get containers
- Don't crowd the sticky Reserve bar — it must always be reachable with breathing room
- Don't use sharp rectangular buttons — OpenTable buttons are pills
- Don't animate aggressively — motion is clean 150–300ms ease-out
- Don't hide the points annotation on reward slots — it's the loyalty incentive

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Hero photo 150pt; 3 slot chips per row + "+N more" |
| iPhone 13/14/15 | 390pt | Standard layout; 4 slot chips per row |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in transparent top nav |
| iPhone 15/16 Pro Max | 430pt | Hero photo 220pt; 5 slot chips per row |
| iPad (portrait) | 768pt | 2-column restaurant grid; detail in split (photo + booking panel) |
| iPad (landscape) | 1024pt+ | List + detail master-detail; slot grid 6-up |

### Dynamic Type
- Screen titles, restaurant names, body, metadata: full scale
- Tab labels, points tags, slot-time text: FIXED (the slot grid is layout-sensitive)
- Slot chip minimum width preserved; chips wrap to more rows at large scales

### Orientation
- All screens support rotation
- iPad split: restaurant list + detail/booking panel side-by-side
- Hero photos rotate freely; sticky Reserve bar stays pinned bottom

### Touch Targets
- Time-slot chip: ≥ 64pt × 52pt (full chip tappable)
- Tab bar icons: 21pt glyph, 44pt hit area
- Party/date selector chip: 40pt tall, full-chip tap
- Restaurant card: full-card tap to detail
- Primary buttons: 48–50pt tall minimum

### Safe Area Handling
- Top: transparent nav respects safe area and Dynamic Island over hero photo
- Bottom: sticky Reserve bar and tab bar respect home indicator
- Keyboard: search and party/date entry scroll above keyboard; sheets sit above keyboard
- Sides: 16–18pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Surface gray (light): `#F6F6F7`
- Canvas (dark): `#121212`
- Surface (dark): `#1C1C1E`
- Divider: `#E4E4E7` light / `#2E2E31` dark
- Text primary: `#1A1A1C` light / `#EDEDED` dark
- Text secondary: `#6B6B70` light / `#A2A2A6` dark
- OpenTable Red (primary): `#DA3743`
- OpenTable Red Bright (dark CTA / accent): `#F2545B`
- OpenTable Red Pressed: `#B92C37`
- Red tint surface: `#FCEBEC` light / red-08 dark
- Gold review star: `#E8A33D`
- Points teal (Dining Rewards): `#1F8A8A`
- Diner green (availability / social proof): `#2FA86A`
- Notify amber: `#E08A2F`
- Error: `#D6342F` light / `#F2545B` dark

### Example Component Prompts
- "Build the OpenTable reservation time-slot grid in SwiftUI: a wrapping row of rounded-rect chips (10pt corner radius, ≥ 64pt wide, 52pt tall, 9pt gap). Default chip: background `#F6F6F7` (dark `#262629`), 1pt `#E4E4E7` border, time in Inter 14pt w700 `#1A1A1C` centered. Recommended/selected chip: solid `#DA3743` (dark `#F2545B`), no border, white time. If a slot earns 1,000 points, add a second line '+1,000' in Inter 9pt w700 teal `#1F8A8A` (white 85% when selected). Tap: fill animates to red 150ms + scale 0.97 bounce + soft haptic; updates the sticky Reserve bar label."

- "Create the OpenTable restaurant detail page: full-bleed hero photo 200pt tall with a 32pt translucent dark back circle and heart/share circles. Below: restaurant name Inter 26pt w800 `#1A1A1C`; a row of 5 gold `#E8A33D` stars + '4.7' + '(412)' in `#6B6B70`; meta 'Steakhouse · $$$$ · Downtown' Inter 14pt `#6B6B70`; a diner-green-tint pill '🔥 Booked 38 times today' Inter 11pt w700 `#2FA86A`. Then 'Select a time' Inter 22pt w700, a context line 'Tonight · Wed May 21 · Party of 2', the slot grid, party/date selector chips. Sticky bottom bar: full-width `#DA3743` pill 50pt tall, 'Reserve for 7:00 PM' Inter 16pt w700 white, top shadow `rgba(0,0,0,0.10) 0 -2px 16px`."

- "Render the OpenTable restaurant card: 14pt-radius hero photo, name Inter 18pt w700, gold `#E8A33D` star rating + '4.7 (412)', meta line `#6B6B70`, then an embedded row of 4 time-slot chips with a trailing '+6 more'. 0.5pt `#E4E4E7` divider below."

- "Build the OpenTable party-size selector: a pill chip (background `#F6F6F7` / `#262629`, 1pt `#E4E4E7` border) with a leading person.2 icon `#6B6B70` and '2 people' Inter 13pt w600. Tapping opens a bottom sheet (20pt top radius) with a − / + stepper, count Inter 22pt w700 centered, quick chips 1–6 and '7+', and a red `#DA3743` 'Apply' pill pinned bottom."

- "Build the OpenTable primary button: fully-rounded pill (999pt radius), background `#DA3743` (dark `#F2545B`), white text Inter 16pt w700, 14pt vertical / 28pt horizontal padding. Pressed: background `#B92C37` + scale 0.98 + soft haptic."

- "Render the OpenTable bottom tab bar: 52pt + safe area, background `#FFFFFF` / `#121212` at 94% with blur, 0.5pt top border. 5 tabs — Discover, Saved, Reservations, Rewards, Profile — 21pt icons, active `#DA3743` (dark `#F2545B`) solid fill, inactive `#9A9A9F` (dark `#6E6E73`), Inter 10pt w600 labels always shown."

### Iteration Guide
1. Canvas is bright white `#FFFFFF` in light, clean charcoal `#121212` in dark — minimal chrome
2. Text primary is neutral near-black `#1A1A1C` — NOT pure black; one grotesque sans, heavy names
3. OpenTable Red `#DA3743` (bright `#F2545B` for dark) is the ONLY booking color — Reserve, selected slot, active tab
4. The reservation time-slot grid is the product's heartbeat — selected slot solid red, rest neutral with hairline
5. Annotate 1,000-point slots with a teal "+1,000" — the rewards hook for off-peak booking
6. Gold star `#E8A33D` is ratings only; teal `#1F8A8A` is Dining Rewards points only
7. "Booked N times today" social proof drives trust + urgency on the detail page
8. The booking spine is search → list → detail (hero + slot grid + party/date) → sticky Reserve → confirmation
9. Buttons, filters, and the search bar are fully-rounded pills
10. Motion is clean 150–300ms ease-out; soft haptics on slot select / filter / stepper, success on confirm
