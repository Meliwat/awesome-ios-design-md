# Design System Inspiration of Apple Wallet (iOS)

## 1. Visual Theme & Atmosphere

Apple Wallet is a vault rendered as a stage. The canvas is true black (`#000000`) — not Apple's usual dark gray — because the screen needs to disappear so the cards can do the entire performance. Cards stack vertically with each one peeking 80pt above the next, perfectly aligned on a vanishing-point spine, like a fanned-out billfold seen from above. When you tap a card, the rest tuck downward into the pocket; when you pull a card up, it lifts off the stack and grows to fill the screen with a 3D-ish parallax that the OS treats as part of the chrome itself. The whole app is essentially a single, very precise, very physical animation: cards as objects, glass as surface, edge-to-edge.

There is almost no UI chrome. The status bar floats over the cards (white text on the dark canvas), the "Wallet" title appears at the top in SF Pro Display 34pt Bold, and a single "+" button sits in the top-right inside a circle. That's it — no tab bar, no bottom sheet, no search bar. Every other surface in the app belongs to a card: the Apple Card's titanium-fade gradient (silver → black mirror), the credit/debit cards' brand-issued artwork, the boarding pass's airline color band, the transit pass's transit-authority palette. Apple does not impose a typography or color system on cards; it ships a manifest schema (PassKit) and lets every issuer render their own face. Wallet's contribution is the frame, the stack, and the physics.

When you tap a card to expand it, the supplementary content beneath is uniform: card number masked to last 4, latest transactions in a clean SF Pro Text list, "Card Number" / "Card Balance" / "Statement" rows in tightly spaced 17pt Regular. The bottom of an expanded card is a translucent toolbar with action glyphs (Apple Pay, Card Details, Statements). Tap the "..." button and a `.regularMaterial` sheet rises with full settings. The detail screens are quintessentially Apple HIG: grouped lists with separator hairlines at `#262629`, tinted SF Symbols at 22pt, system-blue links — clean, predictable, system-native. The drama is in the stack; the depth is in the cards; the chrome is invisible.

**Key Characteristics:**
- True black canvas (`#000000`) — not dark gray — to make the cards float
- Cards stack vertically with 80pt overlap, perfectly aligned on a vanishing-point spine
- "Wallet" title in SF Pro Display 34pt Bold, white, top-left
- Single "+" icon button top-right inside a 32pt circle (`rgba(255,255,255,0.12)` fill)
- No tab bar — entire app is the card stack and its expanded detail
- Cards are issuer-controlled artwork; Wallet only owns the frame, radius (10pt), and shadow
- Apple Card is the signature: titanium gradient `#E8E8EB` → `#3D3D3F`, Cupertino white logo, chip glyph
- Boarding-pass cards have a perforated middle separator (notch + dashed line)
- Transit passes have an animated balance counter at the top
- Card stack physics: spring damping 0.8, 0.5s expand, 0.4s collapse
- SF Pro Display (34pt headers) and SF Pro Text (17pt body) — system-native
- Detail screens are pure Apple HIG: grouped insets, separator hairlines, system blue tints

## 2. Color Palette & Roles

### Canvas, Surfaces & Dividers
- **True Black** (`#000000`): Primary canvas. Wallet uses pure black, not the system `systemBackground` dark mode (`#1C1C1E`), so the cards visually float.
- **Surface 1** (`#1C1C1E`): Expanded-card supplementary section background (transactions, card details panel).
- **Surface 2** (`#2C2C2E`): Pressed states, inset row backgrounds in detail sheets, scrub indicator track.
- **Glass Overlay** (`rgba(255,255,255,0.12)`): The "+" button background, the toolbar buttons on an expanded card.
- **Hairline Divider** (`#262629`): Separator lines between settings rows; sits between Surface 1 and the next row.

### Apple Card Specifics
The Apple Card is Wallet's signature object — a gradient titanium face.
- **Titanium Highlight** (`#E8E8EB`): Top-left of the card's diagonal gradient.
- **Titanium Midtone** (`#A8A8AD`): Center of the gradient.
- **Titanium Shadow** (`#3D3D3F`): Bottom-right of the gradient.
- **Apple Logo White** (`#FFFFFF`): The pure-white  glyph on the upper-right.
- **Chip Gold** (`#C7AC73`): The embossed chip glyph on the card face.
- **Daily Cash Red** (`#FF3B30`): The "Daily Cash" balance accent on the card-back panel.

### Common Card Brand Hints (issuer-controlled, but recognizable)
- **Chase Sapphire Blue** (`#1A2D4F`): Premium Chase card face.
- **AmEx Platinum** (`#A7B0B7`): AmEx Platinum metal face.
- **Visa Navy** (`#1A1F71`): Visa-prominent debit card art.
- **Mastercard Red/Orange** (`#EB001B` / `#FF5F00`): Mastercard interlocking-circles glyph.

### Text
- **Pure White** (`#FFFFFF`): Primary text on the canvas — "Wallet" title, the issuer name on a card face.
- **Card Last4 White** (`#FFFFFF` @ 90% opacity): The masked card number on a card face.
- **Detail Text Primary** (`#FFFFFF`): Body text inside an expanded card / detail sheet.
- **Detail Text Secondary** (`#A0A0A5`): Captions, "Latest Transactions" timestamps, balance dates.
- **Detail Text Tertiary** (`#636368`): Footnotes, "as of 9:42 AM today" stamps.

### Semantic
- **System Blue** (`#0A84FF`): Links, "View Statements", "See All Transactions" — pure HIG system blue (iOS dark mode).
- **Success Green** (`#30D158`): "Payment successful" toast, transit-balance positive indicator.
- **Warning Amber** (`#FF9F0A`): Low transit balance, expiring card.
- **Error Red** (`#FF453A`): Failed payment, declined card, low Apple Cash balance.

### Apple Pay UI Tinting
When you double-press the side button to invoke Apple Pay, the canvas locks to true black and the card face becomes the entire payment sheet. The "Hold near reader" copy is white SF Pro Text 17pt. The Face ID glyph is white. After authentication, the green check is `#30D158`. Apple does not theme this — it is brand-neutral on purpose.

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro Display` (titles 20pt+) / `SF Pro Text` (body 17pt and below)
- **Fallback stack**: `-apple-system, BlinkMacSystemFont, 'Helvetica Neue', sans-serif`
- **Web/marketing Google Fonts substitute**: `Inter` at -0.4 letter-spacing for titles approximates SF Pro Display
- **Card faces**: Each issuer ships its own typography inside the PassKit manifest; Wallet does NOT override. Apple Card uses SF Pro at a custom weight; Chase uses Whitney; Delta uses Whitney Italic. Wallet only specifies the masked card-number style.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Wallet Title | SF Pro Display | 34pt | 700 (Bold) | 1.1 | 0.36pt | Large nav title on the main stack screen |
| Detail Sheet Title | SF Pro Display | 28pt | 700 | 1.15 | 0.35pt | "Card Details", "Latest Transactions" sheets |
| Section Header | SF Pro Text | 13pt | 600 (Semibold) | 1.2 | -0.08pt | UPPERCASE — "PAYMENTS", "LATEST TRANSACTIONS" |
| Card Face Issuer Name | SF Pro Display | 17pt | 600 | 1.1 | 0pt | "Apple Card", "Chase Sapphire" on the card face |
| Card Face Last4 | SF Mono | 17pt | 500 (Medium) | 1.0 | 0.5pt | The "•••• 4521" masked number on the card face |
| Card Face Holder Name | SF Pro Text | 13pt | 500 | 1.0 | 0.3pt | "MELIWAT" — embossed-feeling caps |
| Card Balance Hero | SF Pro Display | 40pt | 700 | 1.0 | 0.4pt | Apple Card balance "$1,284.20" |
| Body | SF Pro Text | 17pt | 400 (Regular) | 1.3 | -0.4pt | Transaction descriptions, settings labels |
| Footnote | SF Pro Text | 13pt | 400 | 1.3 | -0.08pt | "Aug 12, 2025 · Visa ••• 4521" timestamps |
| Caption | SF Pro Text | 11pt | 400 | 1.2 | 0.07pt | "as of 9:42 AM today" stamps |
| Transaction Amount | SF Pro Text | 17pt | 500 | 1.0 | 0pt | "$24.50" — right-aligned in transaction row |
| Daily Cash Amount | SF Pro Display | 22pt | 700 | 1.0 | 0pt | "$8.12" daily cash callout |
| Action Button | SF Pro Text | 17pt | 600 | 1.0 | -0.4pt | "Pay Now", "Card Number", "Statement" |
| System Tab Label | SF Pro Text | 10pt | 500 | 1.0 | 0pt | (Not used in Wallet — there's no tab bar) |

### Principles
- **SF Pro Display for ≥20pt, SF Pro Text for ≤17pt**: Apple's documented switch-over — Wallet follows it rigidly.
- **Tabular numerals everywhere there's a number**: `font-variant-numeric: tabular-nums` on every amount, transaction value, balance, last4 — so dollar columns align perfectly.
- **Negative tracking on body**: -0.4pt at 17pt is the Apple system body default; Wallet inherits it. Do not flatten to 0.
- **Section headers are UPPERCASE 13pt Semibold**: Standard iOS grouped-list pattern — "PAYMENTS", "LATEST TRANSACTIONS", "TRIP DETAILS".
- **No proprietary face on the card frame**: Wallet's frame is SF Pro. The card *face* may use Whitney, Helvetica, Apple Color Emoji — that is the issuer's territory.

## 4. Component Stylings

### The Card (the hero component)

**Card Frame (the universal envelope)**
- Width: full content width minus 16pt horizontal margins (≈358pt on iPhone 15)
- Height: 220pt for credit/debit, 280pt for boarding pass, 200pt for store loyalty
- Corner radius: 10pt (fixed across all card types — Wallet's signature radius)
- Drop shadow: `rgba(0, 0, 0, 0.5) 0 8px 24px` — long, dark, surprisingly heavy because the canvas is true black
- Inner highlight: 0.5pt `rgba(255, 255, 255, 0.08)` top hairline for a subtle glass edge

**Apple Card (the signature object)**
- Background: linear gradient 135° from `#E8E8EB` (top-left) → `#A8A8AD` (mid) → `#3D3D3F` (bottom-right)
- Subtle conic noise overlay at 6% opacity to suggest brushed titanium
- Apple logo `` glyph: 24pt, white `#FFFFFF`, positioned top-right with 16pt inset
- Chip glyph: 26 × 20pt rounded rect `#C7AC73`, positioned top-left with 16pt inset
- Cardholder name: bottom-left, SF Pro Text 13pt Medium uppercase `#1A1A1A` (dark on titanium)
- Card number: hidden by default; appears as `•••• ••••• ••••• 4521` only on the detail screen
- Daily Cash chip: bottom-right, 28pt circle filled `#FF3B30` with white `$` glyph

**Standard Credit/Debit Card (e.g. Chase Sapphire)**
- Background: issuer-controlled gradient (Chase Sapphire Blue `#1A2D4F` → `#0E1B30`)
- Issuer name: top-left, SF Pro Display 17pt Semibold white
- Network logo: top-right (Visa, Mastercard, AmEx) at native brand colors
- Cardholder name: bottom-left, SF Pro Text 13pt Medium uppercase white @ 90%
- Card number: bottom-right, SF Mono 17pt Medium `•••• 4521` with 0.5pt letter-spacing

**Boarding Pass Card**
- Two-section card with a perforated separator at ~70% height (8pt notch on both edges + 1pt dashed line)
- Upper section: airline color band background, large flight number SF Pro Display 28pt Bold white, "BOS → SFO" route in SF Pro Display 34pt Heavy
- Lower section: white background (`#FFFFFF`), boarding group / seat / zone in SF Pro Text 17pt with labels in 11pt Caption uppercase
- QR/barcode strip: bottom row, full width

**Transit Pass (e.g. NYC OMNY, London Oyster)**
- Background: transit-authority blue (`#1B4DD2` for OMNY, `#003E9C` for Oyster)
- "Transit" label top-left in SF Pro Text 11pt Semibold uppercase white @ 70%
- Balance hero: SF Pro Display 28pt Bold white, animated counter on entry/exit
- Tap-to-pay glyph: bottom-right, NFC waves icon white 24pt

### Card Stack (the home screen)

**Stack Layout**
- Vertical column of cards, each card peeking 80pt above the one beneath it
- Vanishing-point alignment: every card is the same width, perfectly horizontally centered
- Stack background: `#000000` true black, no other content
- Maximum visible: 5 card peeks at once; the rest scroll out of frame
- Scroll behavior: rubber-band at the top (the stack springs back); pull-to-add at very bottom

**Card Peek Behavior**
- A peek shows only the top 80pt of the card — typically the issuer name, network logo, and a hint of the gradient
- The 80pt peek includes the top 16pt of the card's inner content padding
- Each peek has its own drop shadow that fades down into the card beneath it for a stacked-paper effect

**Card Tap → Expand**
- On tap, all other cards animate downward into the pocket simultaneously (`.easeInOut`, 0.4s)
- The tapped card grows from its peek state to full card size (`.spring(response: 0.5, damping: 0.8)`)
- Below the expanded card, the supplementary content (transactions, balance, actions) cross-fades in at +200ms delay
- Tap haptic: `.impactOccurred(.medium)` on the card; `.selectionChanged()` if scrolling reveals a new card

**Pull to Collapse**
- Drag down on the expanded card to collapse it back into the stack
- Drag threshold: 60pt
- Rubber-band resistance after 100pt
- Haptic on release: `.impactOccurred(.soft)`

### Detail Sheets & Lists

**Grouped List Row (Card Details, Settings)**
- Background: `#1C1C1E` (Surface 1)
- Padding: 16pt vertical, 16pt horizontal
- Left: row label in SF Pro Text 17pt Regular white
- Right: row value in SF Pro Text 17pt Regular `#A0A0A5` (or system blue `#0A84FF` for tappable rows)
- Disclosure indicator: 13pt `chevron.right` SF Symbol `#636368`, right-aligned
- Separator: 0.5pt `#262629` hairline, inset 16pt from left

**Transaction Row**
- Height: 60pt
- Left: 32pt circular merchant logo or category icon
- Middle: merchant name in SF Pro Text 17pt Regular white + date in SF Pro Text 13pt Regular `#A0A0A5` (stacked)
- Right: amount in SF Pro Text 17pt Medium white + Daily Cash earned in SF Pro Text 11pt Regular `#FF3B30` (stacked, right-aligned)
- Background: `#1C1C1E`, separator `#262629` hairline

**Action Toolbar (bottom of an expanded card)**
- Height: 56pt
- Background: `.regularMaterial` blur over `#000000`
- Buttons: 3 horizontally arranged, each 44pt × 44pt circular container `rgba(255,255,255,0.12)` background
- Glyph: SF Symbol white 18pt — `creditcard.fill`, `list.bullet.rectangle`, `ellipsis`
- Tap haptic: `.selectionChanged()`

### Buttons

**"+" Add Card Button (top-right)**
- Size: 32pt × 32pt circle
- Background: `rgba(255,255,255,0.12)` (Glass Overlay)
- Glyph: 16pt SF Pro Display weight Bold `+` white centered, or `plus` SF Symbol
- Tap: `.impactOccurred(.soft)` haptic, opens a sheet for "Add Card"

**Apple Pay Button (in checkout flows)**
- The system `PKPaymentButton` — not custom
- Background: pure `#000000` (light scheme inverts to white)
- Text: ` Pay` in SF Pro Display 17pt Semibold white
- Corner radius: 8pt
- Height: 50pt

**System Blue Link**
- SF Pro Text 17pt Regular `#0A84FF`
- No underline, no background
- Used inside detail sheets ("See All Transactions", "Get Help with This Charge")

**"Pay Now" Button (Apple Card payment flow)**
- Full-width minus 32pt margins
- Background: `#FFFFFF`
- Text: "Pay Now" SF Pro Display 17pt Semibold `#000000`
- Corner radius: 14pt
- Height: 54pt

## 5. Layout Principles

### Spacing System
- Base unit: 4pt (Apple HIG default)
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 56, 64
- Card horizontal margin: 16pt on iPhone — cards always have breathing room from the screen edge
- Card peek overlap: 80pt fixed (Wallet's signature interval)
- Detail sheet horizontal margin: 16pt
- Section gap between grouped lists: 32pt

### Grid & Container
- The home screen is essentially gridless — it is a Z-axis stack on a single vertical column
- Detail sheets use the Apple HIG inset-grouped table style: full-width hairlines, 16pt content insets, 32pt between sections
- Boarding passes and event tickets use a horizontal photo-carousel style when there are multiple of the same kind

### Whitespace Philosophy
- **Black is the breathing room**: Wallet does not need whitespace because the canvas is true black — the cards are floating objects, and the void between them is the space
- **Cards never touch the screen edge**: 16pt minimum margin to give the card its drop shadow room to bloom
- **The stack interval is sacred**: 80pt of peek; never compress this for "showing more cards" — the stack reads as a stack because of this rhythm

### Border Radius Scale
- Card frame (10pt): the universal Wallet card radius — never larger
- Detail sheet (14pt): rounded rectangle top corners when the sheet rises (matches iOS standard sheet)
- Action toolbar buttons (50%): full circle
- Apple Pay button (8pt): system default
- "Pay Now" CTA (14pt): rounded rectangle, matches iOS sheet corner
- Grouped list group (10pt): standard inset-grouped row group corner

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Detail-sheet body, grouped-list rows |
| Card (Level 1) | `rgba(0,0,0,0.5) 0 8px 24px` + 0.5pt `rgba(255,255,255,0.08)` inner top highlight | Every card on the stack |
| Stack Shadow (Level 1.5) | Layered shadow per card peek — each card casts its own onto the card below | Card stack only |
| Floating (Level 2) | `rgba(0,0,0,0.6) 0 12px 32px` | An expanded card lifted off the stack |
| Sheet (Level 3) | `rgba(0,0,0,0.5) 0 -8px 32px` + `.regularMaterial` over `#000000` | Detail sheets, action toolbars |
| Apple Pay Sheet (Level 4) | Full-screen, no shadow — it is the canvas | Apple Pay payment authentication |

**Shadow Philosophy**: Wallet's shadows are dark and long because the canvas is true black. A 0.08 opacity shadow that works on white would be invisible here. Wallet shadows sit around 0.5 opacity with 24-32pt blur. The signature stacked-cards effect comes entirely from these heavy shadows + the inner top highlight on each card creating the illusion of paper-thin objects with glass edges.

### Motion
- **Card expand**: spring `response: 0.5, damping: 0.8` — slightly bouncy but contained; the card grows from 80pt peek to full 220pt body in ~500ms; rest of stack tucks down `.easeInOut` 0.4s in parallel
- **Card collapse**: same physics reversed; haptic on release `.impactOccurred(.soft)`
- **Card stack scroll**: standard iOS scroll physics with overshoot rubber-band; haptic `.selectionChanged()` every 80pt
- **Double-side-button → Apple Pay**: card slams to center, screen turns true black, Face ID glyph fades in, success → green check, haptic `.notificationOccurred(.success)`
- **Transit balance update**: animated counter on tap-in/tap-out, scrubs from old → new over 800ms with `.easeOut`
- **Daily Cash chip pulse**: subtle 1200ms breathing pulse, scale 1.0 → 1.04 → 1.0 (only on the Apple Card face)
- **Boarding pass flip**: tap the QR code to flip the card 180° around the Y-axis with a 600ms `.easeInOut` for the gate-agent scanner

## 7. Do's and Don'ts

### Do
- Use `#000000` true black for the canvas — not `#1C1C1E` or system dark mode background — so cards float
- Stack cards with an 80pt peek interval; this is the signature visual
- Keep the card corner radius at exactly 10pt across every card type
- Apply heavy shadows (`rgba(0,0,0,0.5)` at 24pt blur) — cards on black need heavy shadows to lift
- Add a 0.5pt inner top highlight on every card face for that paper-thin glass-edge feel
- Use SF Pro Display for ≥20pt titles, SF Pro Text for ≤17pt body — never the wrong axis
- Apply tabular numerals on every dollar amount, last4, transaction value, balance
- Let each card's face be issuer-controlled — Wallet's job is the frame, not the brand
- Use system blue `#0A84FF` for tappable links inside detail sheets — pure HIG, never custom
- Render the Apple Card with the titanium gradient (`#E8E8EB` → `#3D3D3F`) — this is the signature object
- Use `.impactOccurred(.medium)` haptic when expanding a card; `.soft` when collapsing
- Animate the card expand with a `response: 0.5, damping: 0.8` spring — bouncy but contained

### Don't
- Don't use a tab bar — Wallet has none, ever; the stack is the entire navigation
- Don't use dark gray `#1C1C1E` as the canvas; it kills the floating-card effect
- Don't tile cards in a grid — Wallet is always a vertical stack with vanishing-point alignment
- Don't theme card faces from Wallet's side — each issuer ships its own face in the PassKit manifest
- Don't use a smaller card radius than 10pt; don't go above 14pt either — boarding passes use exactly 10pt too
- Don't use the iOS standard `system-yellow` star or `system-red` heart on a Wallet card — Wallet does not have those affordances
- Don't add a search bar to the top of the stack — Wallet's stack is short by design (typical user has 4-8 cards)
- Don't use SF Mono for anything except the masked card number on the card face — body never uses mono
- Don't expand two cards simultaneously — the stack physics breaks
- Don't use soft, low-opacity shadows — on a true-black canvas they disappear; heavy is correct

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Card width = 343pt, stack peek = 72pt (compressed) |
| iPhone 13/14/15 | 390pt | Card width = 358pt, stack peek = 80pt (standard) |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected — title sits below it |
| iPhone 15/16 Pro Max | 430pt | Card width = 398pt, stack peek = 80pt |
| iPad | 768pt+ | Cards in a 2-col grid with 24pt gap; selected card expands into a side panel |

### Dynamic Type
- Wallet title (34pt): full Dynamic Type scaling up to xxxLarge
- Detail sheet body (17pt): full scaling
- Card face typography: FIXED — the card is artwork, never reflows for Dynamic Type
- Transaction amounts: scale moderately, layout-tested up to xLarge

### Orientation
- Portrait-locked everywhere — the card stack does not exist in landscape
- Boarding-pass QR scan can rotate to landscape full-bleed (so the scanner can read it horizontally)

### Touch Targets
- Card peek: full 80pt tap area for the card body
- "+" button: 32pt visual, 44pt tap area
- Apple Pay double-click: side button gesture, not on-screen
- Action toolbar buttons: 44pt × 44pt circular

### Safe Area Handling
- Top: safe area honored — title starts at safe-area top + 16pt
- Bottom: stack scrolls into the home indicator area but the last card always has 24pt clearance above the indicator
- Sides: 16pt minimum to give cards their shadow room

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#000000` (true black)
- Surface 1: `#1C1C1E`
- Surface 2: `#2C2C2E`
- Glass Overlay: `rgba(255,255,255,0.12)`
- Hairline: `#262629`
- Text Primary: `#FFFFFF`
- Text Secondary: `#A0A0A5`
- Text Tertiary: `#636368`
- System Blue: `#0A84FF`
- Success Green: `#30D158`
- Error Red: `#FF453A`
- Apple Card titanium gradient: `#E8E8EB` → `#A8A8AD` → `#3D3D3F`
- Chip Gold: `#C7AC73`
- Daily Cash Red: `#FF3B30`

### Example Component Prompts
- "Build the Apple Wallet card stack: vertical column on a `#000000` true-black canvas. Each card is full-width minus 16pt margins, 220pt tall when expanded, and overlaps the card beneath it with an 80pt peek. Every card has a 10pt corner radius, a heavy `rgba(0,0,0,0.5) 0 8px 24px` drop shadow, and a 0.5pt `rgba(255,255,255,0.08)` inner top highlight for a glass edge. The 'Wallet' title sits in the top-left in SF Pro Display 34pt Bold white; a 32pt `rgba(255,255,255,0.12)` circular button with a 16pt `+` glyph sits top-right."
- "Render the Apple Card: 220pt tall, 10pt corner radius, full-width minus 16pt margins. Background is a 135° linear gradient from `#E8E8EB` (top-left) through `#A8A8AD` to `#3D3D3F` (bottom-right) — titanium. Top-right corner: 24pt white  glyph, 16pt inset. Top-left corner: 26 × 20pt rounded rectangle in `#C7AC73` for the chip. Bottom-left: 'MELIWAT' in SF Pro Text 13pt Medium uppercase `#1A1A1A`. Bottom-right: 28pt circle filled `#FF3B30` with a white `$` glyph for the Daily Cash indicator. Subtle conic noise overlay at 6% opacity for brushed-metal feel."
- "Build a Wallet transaction row inside an expanded card detail: 60pt tall, `#1C1C1E` background, 0.5pt `#262629` separator at the bottom. Left: 32pt circular merchant logo. Middle stacked: merchant name in SF Pro Text 17pt Regular white above the date in SF Pro Text 13pt Regular `#A0A0A5`. Right stacked: amount in SF Pro Text 17pt Medium white above 'Daily Cash $0.12' in SF Pro Text 11pt Regular `#FF3B30`. All numbers use `font-variant-numeric: tabular-nums`."
- "Animate the card expand: on tap of a card in the stack, the tapped card grows from its 80pt peek to its full 220pt body using SwiftUI `.spring(response: 0.5, dampingFraction: 0.8)`. All other cards translate downward into the pocket using `.easeInOut(duration: 0.4)`. After 200ms the supplementary content (balance, transactions, action toolbar) cross-fades in beneath the expanded card. Haptic: `UIImpactFeedbackGenerator(style: .medium).impactOccurred()` on tap."
- "Design the Apple Pay invocation: when the user double-clicks the side button, the foreground card slams to center of the screen at full size, the rest of the UI fades to `#000000` true black, and below the card a Face ID glyph (rounded square with viewfinder corners, 64pt, white) appears with 'Use Face ID to confirm' in SF Pro Text 17pt Regular white centered. On success, a green check `#30D158` 64pt circle replaces the Face ID glyph, haptic `.notificationOccurred(.success)`, then 'Hold near reader' in SF Pro Display 22pt Semibold white at the bottom."

### Iteration Guide
1. Canvas is `#000000` true black — not dark gray, not system background
2. Cards stack vertically with an 80pt peek; never grid, never carousel
3. Every card frame is 10pt corner radius and casts a heavy `rgba(0,0,0,0.5)` shadow
4. Apple Card is the signature: titanium gradient, white , gold chip, red `$` Daily Cash chip
5. Detail sheets are pure Apple HIG — grouped lists with `#262629` hairlines, system blue links
6. SF Pro Display ≥20pt, SF Pro Text ≤17pt — Apple's documented switchover, never violated
7. Tabular numerals on every amount, last4, balance — dollar columns must align
8. No tab bar, no search bar — the stack is the whole app
9. Heavy shadows (0.5 opacity, 24pt blur) — required to make cards float on true black
10. Each card's face is issuer-controlled — Wallet only owns the frame, the stack, the physics
