# Design System Inspiration of Marriott Bonvoy (iOS)

## 1. Visual Theme & Atmosphere

Marriott Bonvoy's iOS app is a premium hotel-booking and loyalty product, and its visual language is engineered to feel like a high-end travel concierge in your pocket. It is not a discount-forward marketplace — it is a *membership*. The base palette is a deep, restrained Bonvoy navy (`#16264A`) and a near-black (`#1C1C1C`), with **gold** (`#B3852A`) used sparingly and deliberately to mark **member value**: your points balance, your elite tier, points-redemption rates, and the gold "Book" call-to-action. The discipline is the point — gold never decorates; it signals "this is what your loyalty is worth." The result is an app that reads as expensive, calm, and trustworthy: big hotel hero photography, generous whitespace, serif-adjacent confidence in the headings, and a quiet, premium chrome.

The signature surface is the **hotel detail screen with the Bonvoy points panel + points-aware rate select**. Open a hotel and you see a tall full-bleed hero photo with the sub-brand eyebrow (THE RITZ-CARLTON, W HOTELS, ST. REGIS, COURTYARD, etc.) in gold small-caps, the hotel name in heavy type, location, and a star/review row. Below, a **navy member panel** shows your elite tier, your points balance in large numerals, and a gold progress bar toward your next free night. Then the **rate select**: a stack of rate cards — Member Rate (with the points you'll earn), Points Redemption (priced in points, "no cash due"), Flexible, Prepaid — where the *selected* rate gets a gold border and tint. Pinned to the bottom: a booking bar with the nightly price, the points you'll earn, and the gold "Book" button. This points-and-tier-aware booking flow is the heart of Bonvoy.

The other recognizable surfaces are the **Mobile Key** (a navy card with a gold key glyph: "Skip the front desk · Tap to unlock · Room 1408") and the **elite tier badges** (Silver / Gold / Platinum / Titanium / Ambassador) shown as small-caps pills across the account and stay screens. Search → hotel detail → rate select → confirmation → Mobile Key is the loop the app revolves around, and loyalty is woven through every step.

Color is disciplined to communicate hierarchy of value. Navy is structure (panels, secondary CTAs, the brand frame). Gold is *earned value* (points, tiers, redemption, the primary "Book" CTA). Standard semantics handle success (a muted green confirmation) and error (a restrained red). Everything else is near-black, soft grays, and hairline dividers — the photography and the points number always win the eye. The app deliberately avoids bright consumer accents; it wants to feel like a luxury hotel lobby, not a deal site.

Typography is Apple's **SF Pro** — Marriott Bonvoy ships no custom typeface in-app; it leans on the iOS system font and Dynamic Type, with a distinctive use of **letter-spaced small-caps eyebrows** (sub-brand names, tier names) that evoke luxury hospitality wordmarks. Hierarchy is value-shaped: a heavy points balance, a confident hotel name, gold small-caps eyebrows, readable body, and clear rate-card typography. The points number and the tier name are among the most prominent, intentional type moments on every screen.

**Key Characteristics:**
- Deep Bonvoy navy (`#16264A`) / near-black (`#1C1C1C`) base — premium, restrained, concierge-like
- **Gold (`#B3852A`) reserved for member value** — points balances, elite tiers, redemption, the "Book" CTA — never decoration
- **Big hotel hero photography** leading every property with a gold small-caps sub-brand eyebrow
- **Bonvoy points panel** — elite tier + points balance in large numerals + gold progress-to-next-free-night bar
- **Points-aware rate select** — Member / Points Redemption / Flexible / Prepaid cards; selected = gold border + tint
- **Mobile Key** — navy card, gold key glyph, "tap to unlock, Room 1408" — skip the front desk
- **Elite tier badges** — Silver / Gold / Platinum / Titanium / Ambassador as small-caps pills
- Sticky booking bar — nightly price + points earned + gold "Book" button
- SF Pro system font + Dynamic Type; letter-spaced small-caps eyebrows for sub-brands and tiers
- Calm premium chrome: navy panels, hairline dividers, minimal shadows, photography-forward

## 2. Color Palette & Roles

### Primary (Member Value — Gold)
- **Bonvoy Gold** (`#B3852A`): The value color — points balances, elite-tier eyebrows, points-redemption labels, the primary "Book" CTA. Used sparingly to mark loyalty worth.
- **Bonvoy Gold Pressed** (`#8F6A1F`): Pressed/active state for the gold CTA.
- **Bonvoy Gold Bright** (`#D2A23E`): Dark-mode variant — points numerals, tier eyebrows, gold CTA on the dark canvas where `#B3852A` is too dim.

### Structure (Navy)
- **Bonvoy Navy** (`#16264A`): Member panels, secondary CTAs ("Use points"), the brand frame, Mobile Key card. Constant across light/dark.
- **Near-Black** (`#1C1C1C`): Alternative deep base for headers and immersive screens.
- **Navy Line** (`#243A66`): Borders on navy panels, dividers within the member surface.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas — search, hotel body, account.
- **Surface Cream** (`#F6F4EF`): Grouped section background, rate-card rest state — a warm off-white that pairs with gold.
- **Surface Pressed** (`#ECE9E1`): Pressed/selected rows.
- **Divider** (`#E3E1DA`): Hairline separators.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#121214`): Primary dark canvas — near-black, slightly warm.
- **Dark Surface 1** (`#1A1A1D`): Cards, grouped section background, nav bar.
- **Dark Surface 2** (`#232327`): Pressed rows, rate cards at rest.
- **Dark Divider** (`#2E2E33`): Hairline separators on dark.

### Text
- **Text Primary Light** (`#1A1A1C`): Hotel names, points, body — near-black.
- **Text Secondary Light** (`#5C5C63`): Location, metadata, descriptions.
- **Text Tertiary Light** (`#8E8E96`): Disabled, low-emphasis.
- **Text Primary Dark** (`#EDEDEF`): Primary text on dark.
- **Text Secondary Dark** (`#A0A0A8`): Secondary text on dark.
- **On-Navy Text** (`#FFFFFF` / `rgba(255,255,255,0.7)`): Text inside navy panels.

### Elite Tier Accents
| Tier | Accent | Treatment |
|------|--------|-----------|
| Member (base) | `#5C5C63` | Neutral gray small-caps |
| Silver Elite | `#C7C7CE` on `#2A2A2E` | Cool silver pill |
| Gold Elite | `#D2A23E` on `rgba(179,133,42,0.16)` | Gold pill |
| Platinum Elite | `#9FB6E0` on `rgba(36,58,102,0.4)` | Navy-blue pill |
| Titanium Elite | `#D2A23E` on `#16264A` | Navy + gold |
| Ambassador Elite | `#D2A23E` on `#16264A` with gold border | Navy + gold border (top tier) |

### Semantic
- **Success Green** (`#2E8B57`): Booking confirmed, "Free cancellation", points-credited toast — muted, not bright.
- **Error / Destructive Red** (`#C7453B`): Failed payment, unavailable dates, cancel reservation — restrained.
- **Warning Amber** (`#C8901C`): "Only 2 rooms left at this rate", rate-change notices.
- **Link** (`#16264A` light / `#D2A23E` dark): Inline links — navy on light, gold on dark.
- **Points Earned Accent** (`#B3852A` / `#D2A23E`): The "+2,180 pts" earn line on rate cards and the booking bar.

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro` (Apple system font) — Marriott Bonvoy ships no custom typeface in-app; it uses the iOS system font with Dynamic Type. SF Pro Display for large titles, points balances, and hotel names; SF Pro Text for body and rate cards.
- **Eyebrow Treatment**: SF Pro Text in **letter-spaced uppercase small-caps** for sub-brand names (THE RITZ-CARLTON, W HOTELS) and elite tiers (TITANIUM ELITE) — evokes luxury hospitality wordmarks.
- **Fallback Stack (non-Apple / web mirror)**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Inter', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Not user-switchable**: The face is always the system font; only Dynamic Type size scales.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | SF Pro Display | 32pt | 800 | 1.15 | -0.4pt | Top-level screen header ("Stays", "Account") |
| Points Balance | SF Pro Display | 26pt | 800 | 1.2 | -0.3pt | Member panel points numerals |
| Hotel Name | SF Pro Display | 22pt | 700 | 1.2 | -0.3pt | Hotel name on detail / hero |
| Section Header | SF Pro Text | 18pt | 700 | 1.3 | -0.2pt | "Choose your rate", "Your stay" |
| Body | SF Pro Text | 16pt | 400 | 1.5 | 0pt | Descriptions, policy text |
| Rate Title | SF Pro Text | 15pt | 600 | 1.3 | 0pt | "Member Rate", "Points Redemption" |
| Price / Rate Amount | SF Pro Display | 17pt | 800 | 1.2 | 0pt | Per-night price on rate cards / booking bar |
| Meta / Subtitle | SF Pro Text | 14pt | 400 | 1.35 | 0pt | Location, review count, rate description |
| Eyebrow (small-caps) | SF Pro Text | 11pt | 700 | 1.0 | 1.2pt | Sub-brand + elite-tier labels — uppercase |
| Points Earn Line | SF Pro Text | 11pt | 700 | 1.0 | 0.3pt | "+2,180 pts" in gold |
| Button | SF Pro Text | 16pt | 700 | 1.0 | 0pt | Primary / secondary buttons |
| Tab Label | SF Pro Text | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Caption | SF Pro Text | 12pt | 500 | 1.2 | 0pt | Fine print, progress legends |

### Principles
- **Points and tier are loud and intentional**: The points balance is an 800-weight numeral; the tier eyebrow is letter-spaced small-caps gold — these are among the most prominent type moments because loyalty is the product.
- **Eyebrows evoke luxury wordmarks**: Sub-brand and tier labels are uppercase, tightly tracked, small — the hospitality-brand cue.
- **System font, always**: SF Pro + Dynamic Type; never override the system face.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: Regular body, medium for tabs/captions, semibold for rate titles, bold for headers, extra-bold for points/prices/titles.
- **Hierarchy via weight + the gold/navy split, not bright color**: Gold marks value, navy marks structure; text emphasis comes from size and weight.
- **Dynamic Type first-class**: Titles, points balance, hotel name, headers, body all scale. Eyebrows, tab labels, points-earn lines stay fixed (layout-sensitive small-caps).

## 4. Component Stylings

### Buttons

**Primary Button ("Book" / "Reserve" / "Confirm")**
- Shape: Rounded rectangle, 8pt corner radius (slightly tighter than consumer apps — premium restraint)
- Background: `#B3852A` (Bonvoy Gold) / `#D2A23E` (dark mode)
- Text: `#1C1206` (deep brown-black for contrast on gold), SF Pro Text 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal (full-width in the booking bar)
- Pressed: background `#8F6A1F` + scale 0.98 + light haptic
- Disabled: background `#B3852A` at 35% opacity

**Secondary / Navy Button ("Use points" / "View rates")**
- Background: `#16264A` (Bonvoy Navy)
- Text: `#FFFFFF`, SF Pro Text 15pt weight 700
- Padding: 13pt vertical, 24pt horizontal
- Corner radius: 8pt
- Pressed: background `#1F3157` + scale 0.98

**Outline Button ("Add to trip" / "Map")**
- Background: transparent
- Text: `#16264A` light / `#D2A23E` dark
- Border: 1.5pt `#16264A` / `#D2A23E`
- Padding: 12pt vertical, 22pt horizontal
- Corner radius: 8pt
- Font: SF Pro Text 15pt weight 700

**Text Button ("View all 64 photos" / hotel link)**
- Font: SF Pro Text 15pt weight 700
- Color: `#16264A` light / `#D2A23E` dark
- Underlined
- Pressed: 0.6 opacity

### Bonvoy Points Panel (the core component)

The signature loyalty surface — a navy panel that shows what the member's points are worth.

- Background: `#16264A` (Bonvoy Navy), 12pt corner radius, 1pt `#243A66` border
- Padding: 16pt all
- Top row: leading = elite-tier eyebrow (11pt w700 small-caps gold `#D2A23E`) + member name (13pt `rgba(255,255,255,0.7)`); trailing = points balance (26pt w800 white) + "POINTS" caption (11pt `rgba(255,255,255,0.6)`)
- Progress bar: 6pt tall, track `rgba(255,255,255,0.14)`, fill a gold gradient `#B3852A → #D2A23E`, rounded
- Footer: "21,550 points to your next free night · Redeem from 50,000" in 11pt `rgba(255,255,255,0.6)`

### Rate Select Cards

A stack of selectable rate options on the hotel detail screen.

- Each card: 14pt padding, 10pt corner radius, 1pt `#E3E1DA` / `#2E2E33` border, surface `#F6F4EF` / `#1A1A1D`
- **Selected** card: 1pt `#B3852A` gold border + a 10% gold tint background
- Leading: rate name (15pt w600) + description (12pt secondary, e.g. "Free cancellation · Earn 2,180 pts")
- Trailing: price (17pt w800) + a gold points line ("+2,180 pts" or "68,000 points / night" 11pt w700 gold)
- Rate types: Member Rate, Points Redemption (priced in points), Flexible, Prepaid (Non-Refundable)

### Hotel Hero

- Full-bleed hero photo, ≈256pt tall, square top corners, bottom scrim gradient
- Overlay top: back / share / save circular 36pt buttons (`rgba(18,18,20,0.55)` + blur)
- Overlay bottom: sub-brand eyebrow (gold small-caps 11pt w700 tracked 1.4px) + hotel name (white 22-23pt w800) + location (white 70%)
- On scroll past the hero, a solid compact nav fades in (back + hotel name + share/save)

### Mobile Key

- Navy card (`#16264A`), 14pt corner radius, 1pt `#243A66` border, 16pt padding
- Leading: a 48pt gold-tint rounded tile with a gold key/lock glyph (`#D2A23E`)
- Content: title "Mobile Key ready" (14pt w700 white) + subtitle "Skip the front desk · Tap your phone to unlock" (12pt `rgba(255,255,255,0.65)`) + room line "ROOM 1408 · CHECK-IN 3:00 PM" (11pt w700 gold small-caps)

### Elite Tier Badge

- Small-caps pill, 6pt × 12pt padding, 999pt radius, 11pt w700 tracked 0.6px uppercase
- Color per tier (see Color §2): Silver cool-gray, Gold gold-tint, Platinum navy-blue, Titanium/Ambassador navy + gold (Ambassador adds a gold border)

### Navigation

**Hotel Detail Nav (over hero)**
- Translucent circular overlay buttons (back / share / save) over the hero photo + scrim
- On scroll, a solid compact bar fades in: back chevron + hotel name (truncated) + share/save

**Bottom Tab Bar**
- Height: ≈68pt incl. home indicator
- Tabs: Home, Search, Trips, Mobile Key, Account
- Icon size: 22pt
- Active color: `#B3852A` / `#D2A23E` (gold — loyalty is the spine of the app); inactive: `#8E8E96`
- Labels: SF Pro Text 10pt w500, always shown
- Background: blurred translucent, 0.5pt top divider
- Mobile Key tab may show a gold dot when a key is active

### Input Fields

**Search Bar**
- Pill or rounded-rect, ≈54pt tall, `#F6F4EF` / `#232327` background, 10pt radius
- Two lines: "Where to?" (15pt w700) and "Add dates · 1 room, 2 guests" (12pt secondary)
- Leading location glyph; trailing filter glyph

**Date / Room & Guest Picker**
- Inline calendar; selected range filled `#16264A` at 12% with navy endpoints
- Room/guest steppers with navy-outline `−` / `+` round buttons
- A "Use points" toggle that re-prices rates in points when on (gold accent)

### Distinctive Components

**Bonvoy Points Panel** — see core component. The loyalty heart of the app.

**Points-Aware Rate Cards** — cash + points priced side by side; selected gets the gold border.

**Mobile Key Card** — navy + gold, "skip the front desk". The premium-stay payoff.

**Elite Tier Badge** — small-caps pills marking Silver → Ambassador status everywhere.

**Sub-Brand Eyebrow** — gold letter-spaced small-caps (THE RITZ-CARLTON, ST. REGIS) above hotel names — the luxury wordmark cue.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 18, 24, 32, 40, 48
- Screen content inset: 18pt horizontal
- Card vertical gap: 12pt (rate cards), 16pt (sections)
- Section gap: 24pt; member panel sits 16pt below the star/review row

### Grid & Container
- iPhone: content at 18pt horizontal insets; hero and map full-bleed
- iPad: hotel detail uses a split (hero + member panel | sticky rate/booking panel); search results in a 2-column grid
- Rate cards: full-width stacked list

### Whitespace Philosophy
- **Premium calm**: generous vertical rhythm; the app should feel like a luxury lobby, not a cluttered deal site
- **Points are always near the top of value hierarchy**: the member panel sits high on the hotel screen; the booking bar always shows points earned
- **Photography-forward**: the hero is tall and full-bleed; chrome recedes
- **Gold is scarce by design**: navy/near-black dominate; gold is reserved for what the member earned

### Border Radius Scale
- Square (0pt): full-bleed hero top, immersive headers
- Subtle (4pt): inner image frames, small chips
- Standard (8pt): primary/secondary buttons, rate cards (tight, premium)
- Comfortable (10pt): grouped cards, rate-card containers, search bar
- Large (14pt): member panel, Mobile Key card, bottom sheets
- Extra (20pt): hero modal sheets
- Pill (999pt): tier badges, filter chips
- Circle (50%): hero overlay buttons, avatars

## 6. Depth & Elevation

Marriott Bonvoy is restrained and premium — elevation is quiet and functional, never glossy.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, hotel body, settings cells |
| Card (Level 1) | `rgba(0,0,0,0.06) 0 2px 8px` | Rate cards, results cards (subtle lift) |
| Panel (Level 1.5) | 1pt `#243A66` border, no shadow | Member panel, Mobile Key card (navy panels use a border, not a shadow) |
| Bar (Level 2) | Backdrop blur + 1pt hairline divider | Booking bar, nav bar, tab bar |
| Floating (Level 3) | `rgba(0,0,0,0.16) 0 4px 16px` | Context menus, popovers, map peek |
| Sheet (Level 4) | `rgba(0,0,0,0.2) 0 -4px 24px` | Date picker, filters, rate-detail sheets |
| Modal Overlay | `rgba(0,0,0,0.45)` scrim | Dim behind sheets and full-screen galleries |

**Shadow Philosophy**: The navy member panel and Mobile Key card use a hairline navy border rather than a drop shadow — they should feel inset and crafted, not floating. Rate/results cards get a very soft lift so they read as tappable. On dark mode shadows nearly vanish, so floating panels add a 1pt `#2E2E33` border. Photography never carries a shadow.

### Motion
- **Points balance count-up**: on stay completion or screen entry, the points numeral counts up to the value over 600ms ease-out; a brief gold shimmer sweeps the number
- **Progress-bar fill**: the gold gradient bar animates width from 0 → target over 500ms ease-out on the member panel appearing
- **Rate select**: tapping a rate card animates the gold border in + tint over 180ms; the booking-bar price/points cross-dissolve to the new rate over 250ms
- **Mobile Key unlock**: tapping the key triggers a gold ring pulse + a success haptic; the card briefly flips to "Unlocked"
- **Hero parallax**: the hero photo translates at 0.5× scroll speed as the body scrolls over it
- **Card appear**: fade + 8pt slide-up, 60ms stagger, 220ms ease-out
- **Push navigation**: native iOS push, 300ms horizontal slide
- **Sheet present**: slides up 300ms ease-out; scrim fades 250ms
- **Tab switch**: instant; active glyph crossfades to gold in 120ms
- **Haptics**: soft impact on Book, success haptic on Mobile Key unlock and booking confirmed, selection feedback on rate select and date endpoints

## 7. Do's and Don'ts

### Do
- Use a deep Bonvoy navy (`#16264A`) / near-black (`#1C1C1C`) base — premium and restrained
- Reserve gold (`#B3852A`) for member value only — points, tiers, redemption, the "Book" CTA; brighten to `#D2A23E` on dark
- Lead every hotel with a tall full-bleed hero + a gold letter-spaced small-caps sub-brand eyebrow
- Show the Bonvoy points panel high on the hotel screen — tier + balance + gold progress-to-free-night
- Price rate cards in both cash and points; give the selected card a gold border + tint
- Surface the Mobile Key as a navy + gold card ("skip the front desk")
- Render elite tiers as small-caps pills (Silver / Gold / Platinum / Titanium / Ambassador)
- Always show points earned in the sticky booking bar alongside the cash price
- Use SF Pro + Dynamic Type — respect the user's text-size setting globally
- Keep chrome calm: navy panels with hairline borders, soft card lifts, photography-forward
- Make the points balance and tier among the most prominent intentional type moments

### Don't
- Don't use gold as decoration — it must mean "earned value" (points / tier / redemption / Book)
- Don't introduce a bright consumer accent — Bonvoy is navy + disciplined gold; nothing else competes
- Don't shadow the navy member panel or Mobile Key — they use a hairline navy border, not a drop shadow
- Don't bury points — the member panel sits high; the booking bar always shows points earned
- Don't ship a custom typeface — Bonvoy uses the system font; respect Dynamic Type
- Don't use the deep navy `#16264A` for thin links on dark — switch to gold `#D2A23E`
- Don't render sub-brand or tier labels in sentence case — they are uppercase, tightly tracked small-caps
- Don't over-round corners — buttons/cards are tight (8pt) for a premium, restrained feel
- Don't animate aggressively — motion is calm; the loudest moment is the points count-up
- Don't use pure black (`#000000`) for body text — `#1A1A1C` near-black is correct
- Don't hide the elite tier — it belongs visible on the member panel and account

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Hero ≈220pt; member panel stacks tier above balance; compact booking bar |
| iPhone 13/14/15 | 390pt | Standard layout; hero ≈256pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in the compact scroll nav |
| iPhone 15/16 Pro Max | 430pt | Hero ≈320pt; larger points numeral; bigger rate cards |
| iPad (portrait) | 768pt | Split: hero + member panel / sticky rate+booking panel |
| iPad (landscape) | 1024pt+ | 2-column results; persistent rate panel beside the hero |

### Dynamic Type
- Large titles, points balance, hotel name, section headers, body: full scale
- Eyebrows (sub-brand / tier small-caps), tab labels, points-earn lines, progress legends: FIXED (layout-sensitive)
- Rate cards grow vertically with text; price/points stay aligned

### Orientation
- All screens support rotation
- iPad uses split-view: hero + member panel and a persistent rate/booking panel
- Hero supports landscape (wider photo, same scrim + eyebrow)

### Touch Targets
- Hero overlay buttons: 36pt visual, 44pt hit
- Rate cards: full-card tap to select, ≥ 44pt tall
- Tab bar icons: 22pt glyph, 49pt hit
- Mobile Key card: full-card tap to unlock, ≥ 56pt tall
- Book button: full-width bar button, ≥ 50pt tall

### Safe Area Handling
- Top: hero extends under the status bar; overlay controls inset to the safe area
- Bottom: booking bar and tab bar respect the home indicator
- Keyboard: search and form fields scroll above the keyboard
- Sides: 18pt content inset; hero and map full-bleed including safe areas

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF` / cream surface `#F6F4EF`
- Canvas (dark): `#121214` / surface `#1A1A1D`
- Bonvoy Navy (structure): `#16264A` (constant) · navy line `#243A66`
- Near-black: `#1C1C1C`
- Bonvoy Gold (value): `#B3852A` light / `#D2A23E` dark · pressed `#8F6A1F`
- Text primary: `#1A1A1C` light / `#EDEDEF` dark
- Text secondary: `#5C5C63` light / `#A0A0A8` dark
- Divider: `#E3E1DA` light / `#2E2E33` dark
- Success: `#2E8B57`
- Error / destructive: `#C7453B`
- Link: `#16264A` light / `#D2A23E` dark

### Example Component Prompts
- "Build the Bonvoy points panel in SwiftUI: a `#16264A` navy card, 14pt corner radius, 1pt `#243A66` border, 16pt padding. Top row: leading VStack with an elite-tier eyebrow 'TITANIUM ELITE' (SF Pro 11pt w700, tracking 1.2, `#D2A23E`) and member name 'A. Morgan · Member' (13pt `rgba(255,255,255,0.7)`); trailing VStack right-aligned with points '128,450' (SF Pro Display 26pt w800 white) and 'POINTS' (11pt `rgba(255,255,255,0.6)`). Below: a 6pt progress bar, track `rgba(255,255,255,0.14)`, fill a `#B3852A → #D2A23E` gradient at 64%. Footer: '21,550 points to your next free night' 11pt `rgba(255,255,255,0.6)`. Animate the bar 0 → target over 500ms and count the points up over 600ms."

- "Create a Bonvoy rate-select card: 14pt padding, 10pt corner radius, 1pt `#2E2E33` border, surface `#1A1A1D`. Leading: rate name 'Member Rate' SF Pro 15pt w600 `#EDEDEF` + 'Free cancellation · Earn 2,180 pts' 12pt `#A0A0A8`. Trailing right-aligned: '$545' SF Pro Display 17pt w800 + '+ 2,180 pts' 11pt w700 `#D2A23E`. When selected: 1pt `#B3852A` border + a 10% gold tint background; animate the border/tint in over 180ms."

- "Build the Bonvoy hotel hero: a full-bleed photo ≈256pt, square top corners, bottom scrim gradient to `rgba(18,18,20,0.7)`. Overlay top: back / share / save circular 36pt buttons (`rgba(18,18,20,0.55)` + blur) inset to the safe area. Overlay bottom-leading: a gold small-caps eyebrow 'THE RITZ-CARLTON' (11pt w700 tracked 1.4 `#D2A23E`), hotel name 'The Ritz-Carlton, Half Moon Bay' (white 23pt w800), location 'Half Moon Bay, California · Luxury' (`rgba(255,255,255,0.82)` 13pt)."

- "Render the Bonvoy Mobile Key card: a `#16264A` navy card, 14pt radius, 1pt `#243A66` border, 16pt padding, horizontal layout. Leading: a 48pt rounded tile `rgba(210,162,62,0.16)` with a gold lock glyph `#D2A23E`. Content: 'Mobile Key ready' 14pt w700 white, 'Skip the front desk · Tap your phone to unlock' 12pt `rgba(255,255,255,0.65)`, and 'ROOM 1408 · CHECK-IN 3:00 PM' 11pt w700 `#D2A23E` tracked 0.5. On tap: gold ring pulse + success haptic."

- "Create the Bonvoy sticky booking bar: pinned bottom, ≈76pt + safe area, `rgba(18,18,20,0.97)` + 20pt blur, 1pt `#2E2E33` top divider. Leading: '$545' SF Pro Display 19pt w800 `#EDEDEF` + '/ night' 13pt `#A0A0A8`, and 'Earn 2,180 Bonvoy points' 12pt w700 `#D2A23E`. Trailing: a 'Book' button — `#B3852A` fill, `#1C1206` text 15pt w700, 8pt radius, pressed `#8F6A1F` + scale 0.98."

- "Render the Bonvoy bottom tab bar: ≈68pt incl. home indicator, tabs Home / Search / Trips / Mobile Key / Account, 22pt glyphs, active tint `#D2A23E` (gold — loyalty is the spine) inactive `#8E8E96`, labels SF Pro 10pt w500 always shown, translucent blurred background with a 0.5pt `#2E2E33` top divider, and a gold dot on Mobile Key when a key is active."

### Iteration Guide
1. Base is deep Bonvoy navy `#16264A` / near-black `#1C1C1C`; dark canvas `#121214` — premium, restrained, NOT a deal site
2. Gold `#B3852A` means earned value ONLY — points, tiers, redemption, the Book CTA; brighten to `#D2A23E` on dark
3. The Bonvoy points panel (tier + balance + gold progress bar) sits high on the hotel screen — loyalty is the heart
4. Rate cards are priced in cash AND points; the selected card gets a gold border + tint
5. Every hotel leads with a tall full-bleed hero + a gold letter-spaced small-caps sub-brand eyebrow
6. The Mobile Key is a navy + gold card ("skip the front desk") — the premium-stay payoff
7. Elite tiers (Silver / Gold / Platinum / Titanium / Ambassador) are small-caps pills, visible on the member panel
8. The points balance and tier are among the loudest, most intentional type moments (800-weight numeral, tracked small-caps)
9. Typography is SF Pro + Dynamic Type — no custom typeface; eyebrows are uppercase, tightly tracked
10. Keep it calm — navy panels with hairline borders (not shadows), tight 8pt corners, photography-forward; loudest motion is the points count-up
