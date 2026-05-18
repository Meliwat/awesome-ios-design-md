# Design System Inspiration of Vrbo (iOS)

## 1. Visual Theme & Atmosphere

Vrbo's iOS app is a whole-home vacation-rental marketplace, and its entire visual language is built around one differentiator: you book the *entire place*, never a shared room. Where a generic stays app might foreground hosts and rooms, Vrbo foregrounds **the property** — big, edge-to-edge, swipeable photography that takes over the top of every listing. Open a listing and the first thing you see is a full-bleed photo gallery (≈290pt tall), a photo counter ("1 / 42"), and a share + save row floating over the image. The product wants you to feel the house before you read a word about it. The aesthetic is clean, trustworthy, and family-oriented: a bright white canvas (`#FFFFFF`), confident Vrbo Blue (`#245ABC`) for every action, generous card spacing, and a calm, uncluttered chrome that lets the photography do the selling.

The signature surface is the **listing detail screen with its photo gallery + sticky booking bar**. The gallery is swipeable with a progress-dot indicator and a "see all photos" grid affordance. Below it: trust badges (Premier Host, Instant Book), the property title, location, a single gold star with the review score, a row of property facts (Guests / Bedrooms / Bathrooms / "Whole home"), and the description. Pinned to the bottom is a **booking bar** — the nightly price in heavy type, the selected dates, and a high-contrast blue "Book now" button that stays visible as you scroll. This price-forward, photo-first, always-bookable layout is the core of Vrbo.

The other recognizable surfaces are the **map with price pins** (every property shown as a rounded price bubble; the selected one flips to gold) and the **trip board** — Vrbo's saved-property collections, organized into named trips ("Summer 2026"), shown as horizontal cards with a thumbnail, property facts, and the saved-to label. Search → results list/map → listing detail → trip board is the loop the whole app revolves around.

Color is disciplined. Vrbo Blue is the primary action color: the "Book now" button, links, selected states, the active tab, and map pins. Sky Blue (`#1D6FB8`) is a supporting shade for secondary links and accents. There is exactly one warm color — a **gold review star** (`#F2B01E`) — used for ratings and the selected map pin; nothing else competes with the blue. Success/error follow standard semantics (green confirmation, red errors). Everything else is white, near-black text, and hairline dividers, so the photography and the price always win the eye.

Typography is Apple's **SF Pro** — Vrbo ships no custom typeface; it leans on the iOS system font and Dynamic Type. Hierarchy is commerce-shaped: a heavy price hero, a strong listing title, clear section headers, readable body, and small bold badges. Price is the loudest text on every screen — Vrbo is a marketplace, and the number you'll pay is always near the visual top of the value hierarchy.

**Key Characteristics:**
- Bright white canvas (`#FFFFFF`) light / cool blue-charcoal (`#101317`) dark — clean, trustworthy, family-oriented
- **Whole-home framing everywhere** — "Entire home", "Whole home", never a shared room
- **Full-bleed swipeable photo gallery** leads every listing (≈290pt) with photo counter + dot progress
- **Sticky booking bar** — heavy price + dates + always-visible blue "Book now" CTA
- Vrbo Blue (`#245ABC`) primary action; Sky Blue (`#1D6FB8`) support; one gold review star (`#F2B01E`)
- Trust badges — Premier Host, Instant Book — small, bold, colored chips
- **Map with rounded price pins** — default blue, viewed gray, selected gold
- **Trip boards** — named saved-property collections shown as horizontal cards
- Big photo cards in results list — image-forward, price + rating below
- SF Pro system font + Dynamic Type; price is the loudest type on every screen
- Minimal chrome: a 5-tab bottom bar (Search / Trips / Inbox / Notifications / Account), translucent bars

## 2. Color Palette & Roles

### Primary (Interactive)
- **Vrbo Blue** (`#245ABC`): The primary action color — "Book now" button, primary CTAs, active tab tint, selected states, default map pin.
- **Vrbo Blue Pressed** (`#1B4794`): Pressed/active state for primary buttons.
- **Vrbo Blue Bright** (`#4F8BF0`): Dark-mode variant — links, outline buttons, pins, active tab on the dark canvas where `#245ABC` is too dim.
- **Sky Blue** (`#1D6FB8`): Supporting accent — secondary links, info chips, map accents.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas — search, results, listing body.
- **Surface Gray** (`#F3F5F8`): Grouped section background, search field, filter chips.
- **Surface Pressed** (`#E7EBF1`): Pressed/selected rows and cards.
- **Divider** (`#E1E5EB`): Hairline separators between facts, sections, list rows.
- **Photo Scrim** (`rgba(16,19,23,0.55)`): Gradient over gallery photos for legible overlay controls.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#101317`): Primary dark canvas — a cool blue-charcoal, not pure black.
- **Dark Surface 1** (`#181C22`): Cards, grouped section background, nav bar.
- **Dark Surface 2** (`#21262E`): Pressed rows, filter chips, trip-board cards.
- **Dark Divider** (`#2A3038`): Hairline separators on dark.

### Text
- **Text Primary Light** (`#15181D`): Listing titles, price, body — near-black, not pure black.
- **Text Secondary Light** (`#5A6470`): Location, metadata, descriptions, captions.
- **Text Tertiary Light** (`#8A93A0`): Disabled, very low-emphasis.
- **Text Primary Dark** (`#EDEFF2`): Primary text on dark canvas.
- **Text Secondary Dark** (`#A3AAB4`): Secondary text on dark canvas.

### Accent & Badges
| Role | Color | Use |
|------|-------|-----|
| Gold Review Star | `#F2B01E` | Rating stars + selected map pin — the only warm color |
| Premier Host Badge | `#245ABC` text on `rgba(36,90,188,0.12)` | Top-tier host trust badge |
| Instant Book Badge | `#1F9D57` text on `rgba(31,157,87,0.12)` | Book-without-request badge |
| Map Pin (default) | `#245ABC` fill, white text | Unselected property pin |
| Map Pin (viewed) | `#F3F5F8` / `#21262E` fill, secondary text | Already-viewed pin |
| Map Pin (selected) | `#F2B01E` fill, `#1A1206` text | Currently-selected property |

### Semantic
- **Success Green** (`#1F9D57`): Booking confirmed, Instant Book badge, "Free cancellation" labels.
- **Error / Destructive Red** (`#E04444`): Failed payment, unavailable dates, remove-from-trip.
- **Warning Amber** (`#E8920C`): "Only 2 dates left", price-change notices.
- **Link** (`#245ABC` light / `#4F8BF0` dark): Inline links, "See all photos", host profile.
- **Discount Strikethrough** (`#8A93A0`): Original price struck through when a deal applies.

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro` (Apple system font) — Vrbo ships no custom typeface; it uses the iOS system font with Dynamic Type. SF Pro Display for large titles and price heroes, SF Pro Text for body.
- **Fallback Stack (non-Apple / web mirror)**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Inter', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Not user-switchable**: The face is always the system font; only Dynamic Type size scales.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | SF Pro Display | 32pt | 800 | 1.15 | -0.4pt | Top-level screen header ("Search", "Trips") |
| Price Hero | SF Pro Display | 26pt | 800 | 1.2 | -0.3pt | Nightly price in the booking bar / detail |
| Listing Title | SF Pro Display | 21pt | 700 | 1.25 | -0.3pt | Property title on detail screen |
| Section Header | SF Pro Text | 18pt | 700 | 1.3 | -0.2pt | "What this place offers", "Where you'll be" |
| Body | SF Pro Text | 16pt | 400 | 1.5 | 0pt | Description, policy text |
| Card Title | SF Pro Text | 15pt | 600 | 1.3 | 0pt | Results-card primary line |
| Fact Value | SF Pro Text | 16pt | 700 | 1.2 | 0pt | "8 Guests", "4 Bedrooms" |
| Meta / Subtitle | SF Pro Text | 14pt | 400 | 1.35 | 0pt | Location, review count, dates |
| Badge | SF Pro Text | 12pt | 700 | 1.0 | 0.2pt | Premier Host, Instant Book chips |
| Button | SF Pro Text | 16pt | 700 | 1.0 | 0pt | Primary / secondary buttons |
| Tab Label | SF Pro Text | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Caption | SF Pro Text | 12pt | 500 | 1.2 | 0pt | Photo counter, dot legends, fine print |

### Principles
- **Price is loud**: The nightly price is one of the heaviest type styles on every screen — 800 weight, near the visual top of the value hierarchy. Vrbo is a marketplace.
- **System font, always**: Vrbo never overrides SF Pro — respect Dynamic Type and the user's preferred text size globally.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: Regular body, medium for tabs/captions, semibold for card titles, bold for headers/facts/badges, extra-bold for price and titles.
- **Hierarchy via weight + size, not color**: Color is reserved for the blue and the gold star. Text hierarchy comes from size and weight.
- **Dynamic Type first-class**: Titles, price, headers, body all scale. Tab labels, badges, photo counter stay fixed (layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary Button ("Book now" / Reserve / Continue)**
- Shape: Rounded rectangle, 10pt corner radius
- Background: `#245ABC` (Vrbo Blue) — stays the deep brand blue even on dark
- Text: `#FFFFFF`, SF Pro Text 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal (full-width in the booking bar)
- Pressed: background `#1B4794` + scale 0.98 + light haptic
- Disabled: background `#245ABC` at 35% opacity, text at 60%

**Secondary / Outline Button ("Contact host" / Save)**
- Background: transparent
- Text: `#245ABC` light / `#4F8BF0` dark
- Border: 1.5pt `#245ABC` / `#4F8BF0`
- Padding: 12pt vertical, 22pt horizontal
- Corner radius: 10pt
- Font: SF Pro Text 15pt weight 700
- Pressed: background `#245ABC` at 8% opacity

**Pill Button (Filters / Sort / Map toggle)**
- Background: `#F3F5F8` light / `#21262E` dark
- Text: `#15181D` / `#EDEFF2`
- Border: 1pt `#E1E5EB` / `#2A3038`
- Padding: 10pt vertical, 18pt horizontal
- Corner radius: 999pt (full pill)
- Font: SF Pro Text 14pt weight 600
- Active filter: blue fill `#245ABC`, white text, count badge

**Text Button ("See all 42 photos" / host link)**
- Font: SF Pro Text 15pt weight 600
- Color: `#245ABC` light / `#4F8BF0` dark
- Underlined
- Pressed: 0.6 opacity

**Gallery Overlay Button (back / share / save)**
- Circular, 36pt diameter
- Background: `rgba(16,19,23,0.55)` + 8pt backdrop blur
- Glyph: 18pt white; save heart fills `#FFFFFF` (or `#E04444` when saved)

### Photo Gallery (the core component)

The signature Vrbo element — a full-bleed, swipeable photo carousel at the top of every listing.

- **Height**: ≈290pt on iPhone (≈360pt on Pro Max), full screen width, square top corners
- **Swipe**: horizontal paging, 1:1 finger tracking, snap with 250ms spring
- **Progress**: a dot row at the bottom — active dot widens to a pill (`#FFFFFF`), inactive `rgba(255,255,255,0.45)`
- **Photo counter**: bottom-right pill "1 / 42" — `rgba(16,19,23,0.7)` bg, white 12pt w600, with a small grid glyph
- **Overlay controls**: back / share / save buttons float top, over a top-to-bottom scrim gradient for legibility
- **Tap**: opens a full-screen photo grid ("See all 42 photos")

### Sticky Booking Bar

- Pinned to the bottom of the listing detail, ≈76pt tall + safe area
- Background: `rgba(16,19,23,0.97)` (dark) + 20pt backdrop blur, 1pt top divider
- Leading: price hero (26pt w800) + "/ night" + the selected dates as an underlined tappable line
- Trailing: full "Book now" primary button
- Stays visible as the page scrolls; the price updates live when dates change

### Listing Cards (results list)

- Photo-forward: a 4:3 (or 16:10) image with rounded 14pt corners and a save heart top-right
- Below image: title (15pt w600), location (14pt secondary), a gold-star rating row, and price ("$486 night", price in 16pt w700)
- Whole-home label always present ("Entire home")
- Tap target = full card; 90% width in carousels, full width in lists

### Map Price Pins

- Rounded pill bubbles anchored on the map
- Default: `#245ABC` fill, white 14pt w700 price
- Viewed: neutral surface fill, secondary text (so you can tell what you've seen)
- Selected: `#F2B01E` gold fill, `#1A1206` text, slight scale-up + shadow
- Tapping a pin opens a peek card at the bottom of the map

### Trip Board

- Vrbo's saved-property collections, organized into named trips
- Each saved property: a horizontal card — 64pt thumbnail, property name (14pt w700), facts line (location · BR · sleeps), and a gold "★ 4.9 · Saved to '{Trip}'" tag
- Boards are organized in a tab; add/remove via the save heart anywhere in the app

### Navigation

**Listing Nav (over gallery)**
- No solid bar — translucent circular overlay buttons (back / share / save) float over the photo with a scrim
- On scroll past the gallery, a solid compact bar fades in: back chevron + property title (truncated) + share/save

**Bottom Tab Bar**
- Height: ≈68pt incl. home indicator
- Tabs: Search, Trips, Inbox, Notifications, Account
- Icon size: 22pt
- Active color: `#245ABC` / `#4F8BF0` (filled glyph); inactive: `#8A93A0`
- Labels: SF Pro Text 10pt w500, always shown
- Background: blurred translucent, 0.5pt top divider
- Badge: red `#E04444` dot on Inbox/Notifications when unread

### Input Fields

**Search Bar**
- Pill-shaped, ≈54pt tall, `#F3F5F8` / `#21262E` background, 999pt radius
- Two lines: "Where to?" (15pt w700) and "Add dates · Add guests" (12pt secondary)
- Leading magnifier glyph; trailing filter glyph in a 36pt circle

**Date / Guest Picker**
- Inline calendar with range selection: selected range filled `#245ABC` at 12% with endpoints solid blue
- Guest stepper rows with `−` / `+` round buttons (blue outline)

### Distinctive Components

**Full-Bleed Photo Gallery** — see core component. The product's first impression.

**Sticky Booking Bar** — always-visible price + Book now. The commerce anchor.

**Trust Badge Row** — Premier Host (blue) + Instant Book (green) chips above the listing title.

**Trip Board Card** — named saved-property collections; Vrbo's wishlist equivalent.

**Whole-Home Banner** — a small reassurance chip ("You'll have the entire place to yourself") reinforcing the core differentiator.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 18, 24, 32, 40, 48
- Screen content inset: 18pt horizontal (slightly wider than 16 — Vrbo gives photos and cards room)
- Card vertical gap: 16pt
- Section gap: 24pt; fact row padded 14pt top/bottom between dividers

### Grid & Container
- iPhone: content at 18pt horizontal insets; gallery and map are full-bleed
- iPad: results in a 2-column grid; listing detail uses a split (gallery+facts | sticky booking panel)
- Results list cards: full width; carousels cap cards at ~90% width with a peek of the next

### Whitespace Philosophy
- **Photography wins**: the gallery is full-bleed and tall; chrome is minimal so the house sells itself
- **Price is always reachable**: the booking bar pins the price + CTA on screen at all times
- **Trust is surfaced early**: badges sit above the title; reviews are immediately visible with the gold star
- **Color is scarce**: white/blue-charcoal dominates; blue and the single gold star are the only saturated pixels

### Border Radius Scale
- Square (0pt): full-bleed gallery top, map
- Subtle (4pt): inner photo frames, small chips
- Small (6pt): trust badges, code/policy blocks
- Standard (10pt): primary/secondary buttons, grouped cards
- Comfortable (14pt): listing photo cards, bottom sheets
- Large (20pt): hero cards, modal sheets
- Pill (999pt): search bar, filter chips, map price pins
- Circle (50%): gallery overlay buttons, avatars

## 6. Depth & Elevation

Vrbo is mostly flat with functional elevation — photography and price carry the visual weight, not shadows.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, listing body, settings cells |
| Card (Level 1) | `rgba(0,0,0,0.08) 0 2px 8px` | Results photo cards, trip-board cards |
| Bar (Level 2) | Backdrop blur + 1pt hairline divider | Booking bar, nav bar, tab bar |
| Floating (Level 3) | `rgba(0,0,0,0.16) 0 4px 16px` | Map peek card, context menus, popovers |
| Sheet (Level 4) | `rgba(0,0,0,0.2) 0 -4px 24px` | Date picker, filters, share sheets |
| Modal Overlay | `rgba(0,0,0,0.45)` scrim | Dim behind sheets and full-screen photo grid |

**Shadow Philosophy**: Cards get a soft lift so they read as tappable over white; the booking bar and nav use translucency + a hairline divider rather than a heavy shadow. On dark mode shadows nearly vanish, so floating panels add a 1pt `#2A3038` border as the elevation cue. Photos never carry a shadow — they are full-bleed and edge-anchored.

### Motion
- **Gallery swipe**: 1:1 finger tracking; release snaps to the nearest photo with a 250ms spring (damping 0.85); the active progress dot animates width over 200ms
- **Booking bar price update**: when dates change, the price number cross-dissolves + counts to the new value over 250ms
- **Save heart**: tap scales 1.0 → 1.25 → 1.0 over 280ms with a soft haptic; fill transitions to `#E04444`
- **Map pin select**: pin scales 1.0 → 1.12, recolors to gold over 180ms; peek card slides up 250ms ease-out
- **Card appear (results)**: fade + 8pt slide-up, 60ms stagger per card, 220ms ease-out
- **Push navigation**: native iOS push, 300ms horizontal slide
- **Sheet present**: slides up 300ms ease-out; scrim fades 250ms
- **Tab switch**: instant; active glyph crossfades fill in 120ms
- **Haptics**: soft impact on Book now, light tick on save and tab change, selection feedback on date range endpoints

## 7. Do's and Don'ts

### Do
- Use a bright white canvas (`#FFFFFF`) light / cool blue-charcoal (`#101317`) dark — clean and trustworthy
- Lead every listing with a full-bleed, swipeable photo gallery + photo counter + progress dots
- Keep the booking bar sticky — price (heavy) + dates + blue "Book now" always visible
- Use Vrbo Blue (`#245ABC`) for all primary actions; brighten to `#4F8BF0` on dark for links/pins/outline
- Reinforce the whole-home promise ("Entire home", "Whole home") on cards and the detail screen
- Use exactly one warm color — the gold review star (`#F2B01E`) — for ratings and the selected map pin
- Surface trust badges (Premier Host blue, Instant Book green) above the listing title
- Render map properties as rounded price pins — blue default, gray viewed, gold selected
- Make price one of the loudest type styles on every screen (800 weight)
- Use SF Pro + Dynamic Type — respect the user's text-size setting globally
- Keep chrome minimal: translucent bars, hairline dividers, soft card lifts only

### Don't
- Don't introduce a second accent color — Vrbo is blue with one gold star; nothing else competes
- Don't shadow photos — the gallery is full-bleed and edge-anchored, never a floating card
- Don't bury the price — it must stay visible in the sticky booking bar as the user scrolls
- Don't frame listings as shared rooms — Vrbo is whole-home; the copy and badges must say so
- Don't ship a custom typeface — Vrbo uses the system font; respect Dynamic Type
- Don't use the deep brand blue `#245ABC` for thin links on dark — switch to `#4F8BF0`
- Don't tint the gold star or recolor it per theme — it's constant across light and dark
- Don't over-decorate with gradients or heavy shadows — let photography and price carry the weight
- Don't animate aggressively — motion is calm, 180-300ms ease-out/spring
- Don't use pure black (`#000000`) for body text — `#15181D` near-black is correct
- Don't hide trust badges below the fold — they belong above the title

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Gallery ≈250pt; single-column cards; compact booking bar |
| iPhone 13/14/15 | 390pt | Standard layout; gallery ≈290pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in the compact scroll nav |
| iPhone 15/16 Pro Max | 430pt | Gallery ≈360pt; larger price hero; bigger cards |
| iPad (portrait) | 768pt | 2-column results grid; detail = gallery+facts / sticky booking panel |
| iPad (landscape) | 1024pt+ | 3-column results; persistent map split-view |

### Dynamic Type
- Large titles, price hero, listing title, section headers, body: full scale
- Tab labels, trust badges, photo counter, progress-dot legends: FIXED (layout-sensitive)
- Cards grow vertically with text; photo aspect ratio is preserved

### Orientation
- All screens support rotation
- iPad uses split-view: results list/map + listing detail; full-screen photo grid in landscape becomes a multi-column grid
- Gallery supports landscape (wider photos, same paging)

### Touch Targets
- Gallery overlay buttons: 36pt visual, 44pt hit
- Map price pins: pin body is the tap target, ≥ 44pt effective
- Tab bar icons: 22pt glyph, 49pt hit
- Listing card: full-card tap; save heart 36pt visual, 44pt hit
- Book now: full-width bar button, ≥ 50pt tall

### Safe Area Handling
- Top: gallery extends under the status bar; overlay controls inset to the safe area
- Bottom: booking bar and tab bar respect the home indicator
- Keyboard: search and message fields scroll above the keyboard
- Sides: 18pt content inset; gallery and map are full-bleed including safe areas

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#101317`
- Surface gray: `#F3F5F8` light / `#21262E` dark
- Divider: `#E1E5EB` light / `#2A3038` dark
- Text primary: `#15181D` light / `#EDEFF2` dark
- Text secondary: `#5A6470` light / `#A3AAB4` dark
- Vrbo Blue: `#245ABC` light / `#4F8BF0` dark (links/pins/outline)
- Vrbo Blue pressed: `#1B4794`
- Sky Blue (support): `#1D6FB8`
- Gold review star: `#F2B01E` (constant across themes)
- Success: `#1F9D57`
- Error / destructive: `#E04444`
- Premier Host badge: `#245ABC` on `rgba(36,90,188,0.12)`
- Instant Book badge: `#1F9D57` on `rgba(31,157,87,0.12)`

### Example Component Prompts
- "Build the Vrbo photo gallery in SwiftUI: a full-bleed `TabView` with `.page` style, ≈290pt tall, square top corners. Add a bottom progress-dot row where the active dot widens to an 18pt pill (`#FFFFFF`), inactive `rgba(255,255,255,0.45)`. Bottom-right: a photo counter pill '1 / 42' (`rgba(16,19,23,0.7)` bg, white SF Pro 12pt w600, leading grid glyph). Float back / share / save circular 36pt buttons (`rgba(16,19,23,0.55)` + blur) over a top-to-bottom scrim. Tap opens a full-screen photo grid."

- "Create the Vrbo sticky booking bar: pinned bottom, ≈76pt + safe area, `rgba(16,19,23,0.97)` + 20pt blur, 1pt `#2A3038` top divider. Leading: price '$486' SF Pro Display 26pt w800 `#EDEFF2` + '/ night' 13pt secondary + an underlined 'Jun 14 – 21 · 7 nights' tappable line. Trailing: a full 'Book now' button — `#245ABC` fill, white 15pt w700, 10pt radius, pressed `#1B4794` + scale 0.98."

- "Render a Vrbo results card: a 4:3 photo with 14pt corners and a save heart top-right (white, `#E04444` when saved). Below: title SF Pro 15pt w600, location 14pt `#A3AAB4`, a gold-star (`#F2B01E`) rating row '4.9 · 186', and price '$486 night' with the number in 16pt w700. Soft card lift `rgba(0,0,0,0.08) 0 2px 8px`. Always include an 'Entire home' label."

- "Build a Vrbo map price pin set: rounded pill bubbles. Default `#245ABC` fill + white 14pt w700 price; viewed `#21262E` fill + `#A3AAB4` text; selected `#F2B01E` fill + `#1A1206` text with a 1.12 scale and a soft shadow. On select, animate scale + recolor over 180ms and slide a peek card up from the bottom over 250ms ease-out."

- "Create a Vrbo trip-board card: horizontal layout — 64pt rounded thumbnail, property name SF Pro 14pt w700 `#EDEFF2`, a facts line 'Outer Banks, NC · 4 BR · Sleeps 8' 12pt `#A3AAB4`, and a gold tag '★ 4.9 · Saved to "Summer 2026"' 11pt w700 `#F2B01E`. Card background `#21262E`, 1pt `#2A3038` border, 12pt radius."

- "Render the Vrbo bottom tab bar: ≈68pt incl. home indicator, tabs Search / Trips / Inbox / Notifications / Account, 22pt glyphs, active tint `#4F8BF0` (filled glyph) inactive `#8A93A0`, labels SF Pro 10pt w500 always shown, translucent blurred background with a 0.5pt `#2A3038` top divider, and a red `#E04444` unread dot on Inbox/Notifications."

### Iteration Guide
1. Canvas is bright white (`#FFFFFF`) light, cool blue-charcoal `#101317` dark — clean, trustworthy, NOT pure black
2. Every listing leads with a full-bleed, swipeable photo gallery + counter + progress dots — photography sells
3. The booking bar is sticky — heavy price + dates + blue "Book now" stays on screen as you scroll
4. Vrbo Blue `#245ABC` is the primary action; brighten to `#4F8BF0` on dark for links/pins/outline
5. Whole-home is the differentiator — say "Entire home" / "Whole home" on cards and detail
6. Exactly one warm color — the gold review star `#F2B01E` — used for ratings and the selected map pin only
7. Trust badges (Premier Host blue, Instant Book green) sit above the listing title, never buried
8. Price is one of the loudest type styles everywhere (800 weight) — Vrbo is a marketplace
9. Typography is SF Pro + Dynamic Type — no custom typeface, respect the user's text size
10. Keep it calm — translucent bars, hairline dividers, soft card lifts; photos never carry a shadow
