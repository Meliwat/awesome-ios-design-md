# Design System Inspiration of Cash App (iOS)

## 1. Visual Theme & Atmosphere

Cash App is a fintech rendered as a pop-art club flyer. The canvas is near-black, the type is loud, the accent is one violently saturated green, and the most important screen in the app — the $-amount keypad — is essentially a 1990s ATM reimagined as a high-fashion poster. Where Venmo leans social, Cash App leans cultural: the brand has dressed Megan Thee Stallion in custom Cash Cards, paid YouTubers in $CASHTAG drops, and turned the act of sending money into a remix of Black culture, hip-hop typography, and DIY screen-print energy. The UI rejects every fintech cliche — no charts, no investing-app blues, no bank-app navy — and instead asks you to type a number, name a person, and hit Pay.

The accent is Cash Green (`#00D632`) — a near-traffic-light green that's deliberately too-bright on near-black canvas, the way a neon sign is too-bright in a dark bar. It's the only color that touches money: the Pay button, the $-amount display when valid, the boost glyphs on the Cash Card, and the Bitcoin and Stocks indicators when up. Every other accent is muted by comparison — the Cash Card itself is matte black with a custom Cashtag etched in a single color, the Card tab uses the user's chosen card art as the hero, and the Activity feed alternates between green (received) and gray (sent) with no other chromatic noise.

Typography is Cash Sans, an in-house geometric sans Block (the company) commissioned to replace their earlier reliance on Helvetica Now. It's slightly condensed, has a tall x-height, and carries a deliberate stencil-adjacent personality at display weights — the kind of letter you see on a basketball jersey or a vinyl record sleeve. At keypad sizes (the giant $-amount in the middle of the screen), Cash Sans Mono Bold renders at 72-96pt and dominates the entire viewport. The system is intentionally loud — Cash App does not whisper — and Cash Sans is built to scream legibly.

**Key Characteristics:**
- Near-black canvas (`#000000`) with `#0F0F0F` for slightly elevated surfaces — true black, not warm gray
- Cash Green (`#00D632`) is the single brand accent — Pay CTA, valid amount, success states, active tab glyph
- Giant $-amount keypad as the home screen — `$0` at 96pt+ Cash Sans Mono Bold, custom 0-9 keypad below
- Cash Card is matte black with the user's `$Cashtag` engraved in a chosen accent color — the card is the brand
- Bottom tab bar: 5 tabs as icon-only with NO labels (Money / Card / Pay / Activity / Investing-Bitcoin)
- The Pay tab is a custom dollar-sign glyph, larger than the other tabs, centered as the visual anchor
- All-caps, condensed, slightly oversized type for transaction headers and section labels
- Cash Sans across the board; Cash Sans Mono on amounts, $Cashtags, and the keypad
- Pure-black surfaces with hairline `#1A1A1A` dividers — no shadows, no blur, only contrast
- Boosts (Cash Card discounts) use custom icon art with neon-saturated palettes pulled from artists Block partners with

## 2. Color Palette & Roles

### Primary
- **Cash Green** (`#00D632`): THE brand accent — Pay CTA fill, valid-amount glow, success states, active tab glyph, Cashtag confirmation. Used aggressively against black.
- **Cash Green Pressed** (`#00B829`): Active/pressed state on the Pay button.
- **Cash Green Dim** (`#008C20`): Disabled Pay button, secondary green moments.

### Brand Identifiers
- **Cash Black** (`#000000`): Canvas — pure black, the matte-card finish.
- **Card Black** (`#0A0A0A`): Card surface emboss — a hair lighter than canvas to imply depth on the physical Cash Card render.
- **Bitcoin Orange** (`#F7931A`): Bitcoin tab glyph, BTC line in Investing.
- **Boost Red** (`#FF453A`): Decline states, Stocks down indicators.

### Canvas, Surfaces & Dividers
- **Canvas Black** (`#000000`): Primary canvas — every screen.
- **Surface 1** (`#0F0F0F`): Elevated surfaces — bottom sheets, cards, the Activity row press state.
- **Surface 2** (`#1A1A1A`): Pressed states, input field fill, keypad button press.
- **Divider** (`#1F1F1F`): Hairline dividers between Activity rows and list items — barely visible by design.
- **Hairline** (`#2A2A2A`): More visible divider used sparingly on sheets.

### Text
- **Text Primary** (`#FFFFFF`): Pure white — every primary text, amounts, names, tab glyphs.
- **Text Secondary** (`#9E9E9E`): Metadata — "Sent to", date stamps, balance subtitle.
- **Text Tertiary** (`#6E6E6E`): Disabled labels, placeholder text.
- **Text Muted Green** (`#005C16`): The faint green ghost when the amount field is empty showing "$0".

### Semantic
- **Success Green** (`#00D632`): Same as brand — money received, payment sent confirmed.
- **Error Red** (`#FF453A`): Insufficient funds, declined card, validation errors.
- **Warning Amber** (`#FFA300`): Pending state on Activity rows, identity verification needed.

### Light Mode
Cash App ships a light mode primarily for accessibility — but the brand identity lives in the dark. Light mode flips canvas to white, primary text to black, and surfaces to warm gray, while Cash Green stays identical.
- **Light Canvas** (`#FFFFFF`)
- **Light Surface 1** (`#F5F5F5`)
- **Light Surface 2** (`#EEEEEE`)
- **Light Text Primary** (`#000000`)
- **Light Text Secondary** (`#6E6E6E`)
- **Cash Green (light)** (`#00B829`): Slightly darkened to maintain AA contrast on white.

## 3. Typography Rules

### Font Family
- **Primary**: `Cash Sans` (proprietary, drawn for Block by an in-house type team — released ~2020)
- **Mono**: `Cash Sans Mono` — tabular numerals, used for all $-amounts, $Cashtags, and the keypad
- **Weights**: Light (300), Regular (400), Medium (500), Bold (700), Black (900)
- **Fallback stack**: `-apple-system, BlinkMacSystemFont, 'Helvetica Now', 'SF Pro Text', sans-serif` — Cash Sans is closest to Helvetica Now and SF Pro at body sizes
- **Mono fallback**: `'SF Mono', 'Roboto Mono', 'Menlo', monospace`
- **Google Fonts substitute**: `Inter` for Cash Sans, `JetBrains Mono` for Cash Sans Mono

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Amount Hero (Keypad) | Cash Sans Mono | 96pt | 700 (Bold) | 1.0 | -2pt | The giant $-amount on the Pay screen — shrinks as digits add |
| Amount Receive (Confirmation) | Cash Sans Mono | 64pt | 700 | 1.0 | -1.5pt | "Pay $20" confirmation modal |
| Balance Display | Cash Sans Mono | 48pt | 700 | 1.0 | -1pt | The balance shown on Money tab |
| Screen Title | Cash Sans | 28pt | 900 (Black) | 1.1 | -0.5pt | "Money", "Card", "Activity" — set in BLACK weight, often all-caps for headers |
| Section Header | Cash Sans | 22pt | 700 | 1.15 | -0.3pt | "Recent activity", "Boosts" |
| Cashtag Display | Cash Sans Mono | 22pt | 500 (Medium) | 1.2 | 0pt | The user's "$jackdorsey" shown on their profile and Cash Card |
| Card Label | Cash Sans | 16pt | 500 | 1.3 | 0pt | Activity row name, "From John Smith" |
| Body | Cash Sans | 15pt | 400 | 1.4 | 0pt | About text, settings descriptions |
| Metadata | Cash Sans | 13pt | 400 | 1.3 | 0pt | "Yesterday at 3:42pm", row subtitles |
| Button Label | Cash Sans | 17pt | 700 | 1.0 | 0pt | "Pay", "Request", "Add Cash" |
| Tab Label | Cash Sans | 0pt | — | — | — | NO LABELS on tab bar — icons only |
| All-Caps Section | Cash Sans | 11pt | 700 | 1.2 | 1.5pt | "BOOSTS", "RECEIVED", "PENDING" — small caps headers |
| Cashtag in body | Cash Sans Mono | 15pt | 500 | 1.3 | 0pt | "$johndoe" rendered inline in messages |

### Principles
- **Mono for money, sans for everything else**: Every $-amount, every $Cashtag, every Bitcoin price uses Cash Sans Mono with tabular numerals — this is non-negotiable. The kerning is consistent so amounts align visually in a vertical list.
- **Black weight (900) for screen titles**: "Money", "Card", "Activity" are set in the heaviest available weight — Cash Sans Black — at 28pt. This is the visual signature.
- **Tight negative tracking at hero sizes**: -2pt at 96pt down to 0pt at body — Cash Sans is condensed and the negative tracking pulls digits together for the keypad's poster-like density.
- **All-caps for micro-headers**: "RECEIVED", "PENDING", "BOOSTS" are set in 11pt Bold all-caps with +1.5pt letter spacing — a deliberate retro/streetwear gesture.
- **Pure white on pure black for primary**: No off-white, no warm gray. Cash App's contrast is hard and uncompromising — this is core to the aesthetic.

## 4. Component Stylings

### Buttons

**Primary Pay CTA (the Pay button)**
- Background: `#00D632`
- Text: `#000000` (yes — black text on green, NOT white)
- Font: Cash Sans 17pt weight 700
- Padding: 16pt vertical, 32pt horizontal
- Corner radius: 30pt (effectively a pill at 60pt button height)
- Height: 60pt minimum on the Pay screen
- Pressed: background `#00B829`, scale 0.97
- Disabled (amount = 0 or invalid): background `#1A1A1A`, text `#6E6E6E`
- Haptic: `.notificationOccurred(.success)` on send confirmation

**Request Button (twin of Pay)**
- Background: `#1A1A1A` (Surface 2)
- Text: `#FFFFFF`
- Font: Cash Sans 17pt weight 700
- Same dimensions as Pay
- Pressed: background `#2A2A2A`

**Secondary Pill Button ("Add Cash", "Cash Out", "Add to Bitcoin")**
- Background: `#1A1A1A`
- Text: `#FFFFFF`, Cash Sans 15pt weight 500
- Padding: 10pt vertical, 18pt horizontal
- Corner radius: 500pt (full pill)
- Height: 36pt

**Keypad Button (0-9, decimal, backspace)**
- Container: 96pt × 64pt (3-column grid)
- Background: transparent (only press state shows fill)
- Text: `#FFFFFF`, Cash Sans Mono 32pt weight 400
- Pressed: background `#1A1A1A`, scale 0.96
- Haptic: `.impactOccurred(.light)` per tap
- The "0" button spans 2 columns (twice as wide); decimal is below the "9"; backspace is to the right of "0" with a custom glyph

**Boost Button (Cash Card)**
- Pill container with custom artist-designed glyph (e.g., a coffee cup for Starbucks Boost, a fork for a restaurant Boost)
- Background: `#0F0F0F`
- Border: 1pt `#1F1F1F`
- Padding: 12pt vertical, 16pt horizontal
- Corner radius: 16pt
- Glyph is the visual — text "Activate Boost" sits below in Cash Sans 13pt Medium `#FFFFFF`

### Cards & Containers

**Cash Card Render (the hero on Card tab)**
- Aspect ratio: 1.586:1 (real credit card ratio)
- Background: `#000000` (matte black — the physical card is matte)
- Border: 1pt `#1F1F1F` (a hairline edge to imply the card thickness)
- Corner radius: 12pt
- $Cashtag etched on front: Cash Sans Mono 24pt weight 700 in the user's chosen accent color (Cash green, neon pink, white, etc.) — positioned bottom-left with 24pt inset
- Cash App "$" logo: bottom-right, 32pt, in the same accent color
- Shadow: NONE — the card sits flat on the black canvas; the slight border is the only depth cue
- Tap: scale 0.99 briefly

**Activity Row**
- Height: 64pt minimum
- Background: `#000000`
- Leading: 40pt circular avatar (initial-letter fallback in a randomly-assigned bright color)
- Middle: Name in Cash Sans 16pt weight 500 `#FFFFFF` → "Sent to [Name]" / "From [Name]" in Cash Sans 13pt weight 400 `#9E9E9E`
- Trailing: $-amount in Cash Sans Mono 17pt weight 500 — `#00D632` if received (with "+"), `#FFFFFF` if sent (with "-")
- Divider: 0.5pt `#1F1F1F` bottom hairline
- Tap: background flashes `#0F0F0F`, scale 1.0 (no scale; just background)

**Balance Tile (Money tab top)**
- Container: full-width, 24pt horizontal padding, no card chrome
- Layout: "Cash balance" label in Cash Sans 11pt weight 700 all-caps `#9E9E9E` letter-spaced +1.5pt → balance "$1,247.92" in Cash Sans Mono 48pt weight 700 `#FFFFFF` with the cents at 24pt
- Below: two pill buttons side-by-side, "Add Cash" and "Cash Out" (Surface 2 pill style)

**Boost Card**
- Stack of stylized illustrated cards in the Cash Card tab — each features a custom artist illustration as the hero (e.g., a hand-drawn coffee cup, a vinyl record)
- Container: rounded rectangle 16pt radius, full width on the Cash Card tab
- Background: dark gradient or solid accent matching the boost's brand partner
- Text overlay: boost name in Cash Sans 22pt weight 700 white, percentage discount in Cash Sans 32pt weight 900
- Tap: subtle scale 0.98

### Navigation

**No Top Nav Bar (almost ever)**
- Cash App's home screens have NO traditional nav bar — the screen title (e.g., "Money") sits in the content area as a 28pt Black-weight word, left-aligned, with the user's profile avatar in the top-right corner
- 16pt top safe-area inset before the title begins

**Bottom Tab Bar (the signature)**
- Height: 56pt + safe area
- Background: `#000000` (pure black)
- Top divider: 0.5pt `#1F1F1F`
- 5 tabs: Money / Card / Pay / Activity / Bitcoin (or Investing depending on user config)
- ALL ICONS ONLY — NO LABELS, ever
- Icon size: 28pt (larger than typical iOS for legibility without labels)
- Pay tab is the CENTER tab and uses a distinct visual: a large "$" glyph rendered slightly bigger (32pt) — sometimes inside a circle on the older designs
- Active state: glyph fills with Cash Green (`#00D632`)
- Inactive: white outline glyphs `#FFFFFF` at full opacity (Cash App keeps inactive tabs prominent — they don't dim them)
- Tap haptic: `.selectionChanged()`

**Modal Sheet (Confirm Payment, Profile, Settings)**
- Slides up from bottom over a dimmed black overlay (`rgba(0,0,0,0.8)`)
- Top corners: 24pt radius
- Background: `#0F0F0F` (Surface 1)
- Drag indicator: 4pt × 36pt pill at top, `#2A2A2A`
- Dismiss: swipe down or tap outside

### Distinctive Components

**The $-Amount Keypad (Pay tab)**
- The Pay tab is essentially a single screen: enormous "$0" at the top half, 3×4 numeric keypad at the bottom half, Request and Pay buttons below.
- Top half: "$" sign in Cash Sans Mono 64pt weight 700 `#9E9E9E` (faded), "0" or current amount in Cash Sans Mono 96pt weight 700 `#FFFFFF`
- As digits are added, the amount auto-shrinks down to 72pt → 60pt at 4-5 digits to keep the number on one line
- When valid (amount > $0), the digits glow: a subtle text-shadow `#00D632` at 8pt blur
- Below: keypad (3×4 grid: 1-9, then a column of "." / "0" / backspace)
- Below keypad: two big buttons — "Request" (gray) and "Pay" (green)
- Above keypad: the recipient field — initially "$Cashtag, email, or phone" prompt; once a recipient is selected, shows their avatar + Cashtag pill

**$Cashtag**
- Always rendered in Cash Sans Mono with the "$" prefix
- Brand context: a $Cashtag is a username starting with "$" — Cash App's invention. Examples: `$johndoe`, `$cashapp`.
- Inline in messages or names: 15pt weight 500 in the user's accent color or default `#00D632`
- On profile / card: 22-24pt weight 500-700 in the user's chosen accent

**Profile Avatar**
- Circular, 40-80pt depending on context
- If no photo: a solid bright color (Cash App randomly assigns one from a curated palette of saturated colors — pink, orange, blue, green, purple) with the first letter of the user's name in Cash Sans 22pt weight 700 white centered
- The random avatar colors are deliberately loud — this is part of the visual signature on the Activity feed

**Boost Glyph (Cash Card tab)**
- Each Boost has a custom illustrated glyph — these are commissioned artwork, not stock icons
- Examples: a hand-drawn coffee mug for a coffee boost, a stylized basketball for a sports merch boost, a vinyl record for a music boost
- The glyphs feel hand-drawn, slightly imperfect, very NOT corporate iconography
- This is intentional brand differentiation — Cash App icons read as DIY art, not banking software

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64, 96
- Standard horizontal margin: 24pt — generous, even on small phones, because the type is loud
- Section vertical gap: 32pt between major sections; 24pt between subsections
- The keypad uses a 4pt gap between keys

### Grid & Container
- Content width: full device width minus 24pt horizontal margins on most screens; full-bleed on the keypad
- Single-column always — Cash App never uses 2-col grids; the whole app is vertical-list driven
- The Cash Card tab does use a horizontal scroll for boost categories at the top

### Whitespace Philosophy
- **The amount needs room**: the Pay screen reserves the upper 40% of the viewport for the $-amount alone. This is intentional — the number is the product.
- **Activity rows breathe**: 64pt minimum row height with 16pt vertical padding — generous, scrolling feels heavy and confident
- **No 8-density lists**: Cash App never crams rows. The vibe is "fewer things, bigger".

### Border Radius Scale
- Square (0pt): rare — sometimes on inline tags
- Small (4pt): badges, tiny pills
- Medium (12pt): Cash Card render, Boost cards
- Comfortable (16pt): bottom sheets at the corners, modal containers
- Large (24pt): full modal sheet top corners
- Pill (500pt): all primary CTAs (Pay, Request, Add Cash) when set wide enough; "Add Cash" + "Cash Out" pills
- Circle (50%): avatars, ring confirmations on payment success

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Everything by default — Cash App is FLAT |
| Inset (Level 0.5) | Solid `#0F0F0F` fill on black | The only "depth" Cash App uses — a slightly-lighter surface, no shadow |
| Sheet (Level 1) | `rgba(0,0,0,0.85)` overlay behind sheet | Modal sheets dim everything beneath |

**Shadow Philosophy**: Cash App uses essentially NO shadows. Depth is communicated through fill-color differences (Canvas Black `#000000` → Surface 1 `#0F0F0F` → Surface 2 `#1A1A1A`), not light. This is core to the brand: the app should feel like ink on matte paper, not glass over a glow. The only exception is the dim overlay behind modal sheets.

### Motion
- **Amount keypad tap**: digit drops in with a 200ms spring (response 0.3, damping 0.7), digits already in place shift left to make room. `.impactOccurred(.light)` haptic per tap.
- **Pay button send**: 250ms spring scale 1.0 → 0.97 → 1.0, then on success the screen flashes Cash Green for 300ms before the confirmation sheet slides up. `.notificationOccurred(.success)` haptic.
- **Cash Card flip (tap on card)**: 600ms spring 3D rotation around Y-axis, reveals card back with details
- **Activity row press**: background fades to `#0F0F0F` over 150ms, scale stays 1.0
- **Tab switch**: glyph fills with Cash Green via a quick 200ms color tween, `.selectionChanged()` haptic
- **Boost activation**: scale 1.0 → 1.05 → 1.0 spring with a confetti particle burst, `.notificationOccurred(.success)`

## 7. Do's and Don'ts

### Do
- Use `#000000` pure black as the canvas — not warm gray, not `#111`
- Use Cash Green `#00D632` as the ONLY accent — Pay button, success states, active tab glyph
- Set the $-amount in Cash Sans Mono at 96pt+ on the Pay screen — this is the signature
- Use Cash Sans Black (weight 900) for screen titles like "Money", "Card", "Activity"
- Render every $-amount and every $Cashtag in Cash Sans Mono with tabular numerals
- Keep the tab bar icon-only with NO labels — and make the Pay (center) tab visually larger
- Use random saturated avatar background colors (pink, orange, blue, green, purple) for users without photos
- Use commissioned hand-drawn illustration for Boost glyphs — not stock icons
- Put black text on the green Pay button (not white) — the contrast is the signature
- Make the keypad large — 96pt × 64pt buttons in a 3×4 grid, "0" double-width

### Don't
- Don't use blue or navy anywhere — Cash App is anti-bank-app blue
- Don't add shadows — Cash App is flat; depth is fill-color contrast only
- Don't use `#111` or warm dark grays as canvas — pure `#000000` is core
- Don't dim inactive tab icons — Cash App keeps them at full white opacity
- Don't add tab bar labels — this is a hard rule
- Don't use a serif or humanist sans — Cash Sans is geometric, slightly condensed, urban
- Don't render amounts in proportional type — always Cash Sans Mono with tabular numerals
- Don't use the $Cashtag without the "$" — the dollar sign is part of the brand
- Don't crowd the Pay screen — the giant amount needs 40% of the viewport
- Don't use chart-y Bitcoin orange anywhere except the Bitcoin tab and BTC line — keep accents disciplined

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Keypad amount caps at 72pt; balance display at 40pt |
| iPhone 13/14/15 | 390pt | Standard 96pt amount; full keypad |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected; profile avatar moves below it |
| iPhone 15/16 Pro Max | 430pt | Amount can render at 108pt; keypad scales 110pt × 72pt |
| iPad | 768pt+ | Two-column layout: keypad and amount on left, recipient browser on right; rarely used |

### Dynamic Type
- Most labels honor Dynamic Type
- The $-amount on Pay screen does NOT scale — it's auto-sized based on digit count; user scaling is overridden because layout is critical
- Tab glyphs are fixed 28pt (Pay glyph 32pt)
- Activity row $-amounts honor scaling but clamp at 22pt max

### Orientation
- Portrait only — Cash App locks to portrait everywhere

### Touch Targets
- Keypad buttons: 96pt × 64pt — well above 44pt minimum
- Tab bar icons: 28pt glyph in a 56pt-wide hit area
- Pay/Request buttons: 60pt height
- Activity row: full-width 64pt hit area

### Safe Area Handling
- Top: 16pt inset below safe-area top before the screen title
- Bottom: tab bar pins above the home indicator
- The Pay screen's keypad sits directly above the tab bar with only 8pt gap

## 9. Quick Reference Cheat Sheet

### Color Reference
- Canvas: `#000000`
- Surface 1: `#0F0F0F`
- Surface 2: `#1A1A1A`
- Divider: `#1F1F1F`
- Text Primary: `#FFFFFF`
- Text Secondary: `#9E9E9E`
- Text Tertiary: `#6E6E6E`
- Cash Green: `#00D632`
- Cash Green Pressed: `#00B829`
- Bitcoin Orange: `#F7931A`
- Error Red: `#FF453A`
- Warning Amber: `#FFA300`

### Example Component Prompts
- "Build the Cash App Pay screen: pure black canvas with a giant $-amount in Cash Sans Mono 96pt weight 700 white at the top half. The '$' sign sits at 64pt in `#9E9E9E` and the digits glow with a subtle `#00D632` text-shadow when amount > 0. Below the amount, a 3×4 keypad (1-9 in a 3-col grid, then a row of '.', '0' double-width, and a backspace glyph). Each key is 96pt × 64pt, transparent background, 32pt Cash Sans Mono digits. Below the keypad: two side-by-side buttons — 'Request' (Surface 2 `#1A1A1A` background, white text) and 'Pay' (`#00D632` background, BLACK text — yes, black on green). Both 60pt tall, 30pt corner radius."
- "Create the Cash App bottom tab bar: 56pt tall plus safe area, pure black `#000000` background with a 0.5pt `#1F1F1F` top divider. Five tabs (Money, Card, Pay, Activity, Bitcoin) — ICONS ONLY, no labels. Icons are 28pt; the Pay tab uses a custom 32pt '$' glyph and is centered as the anchor. Active state fills the glyph with Cash Green `#00D632`. Inactive glyphs stay full white at no opacity reduction."
- "Render a Cash Card: 1.586:1 aspect ratio matte black surface, 12pt corner radius, 1pt `#1F1F1F` hairline border. Bottom-left: `$Cashtag` in Cash Sans Mono 24pt weight 700 in the user's chosen accent color (default `#00D632`). Bottom-right: Cash App '$' logo in the same accent, 32pt. NO shadow — the card sits flat."
- "Style an Activity row: 64pt tall black row. Leading 40pt circular avatar (use a random saturated color like `#FF6B9D` with the first letter of the name in Cash Sans 22pt 700 white centered if no photo). Middle: name in Cash Sans 16pt weight 500 white above 'Sent to [Name]' in Cash Sans 13pt weight 400 `#9E9E9E`. Trailing: $-amount in Cash Sans Mono 17pt weight 500 — `#00D632` with '+' prefix if received, white with '-' if sent. 0.5pt `#1F1F1F` bottom divider."
- "Build a Balance Tile on the Money tab: 'CASH BALANCE' label in Cash Sans 11pt weight 700 all-caps with +1.5pt letter spacing in `#9E9E9E`, then directly below '$1,247.92' in Cash Sans Mono 48pt weight 700 white (with the cents '.92' at 24pt). Below, two pill buttons side by side: 'Add Cash' and 'Cash Out', each `#1A1A1A` background, white Cash Sans 15pt weight 500 text, 36pt tall, full pill radius."

### Iteration Guide
1. Canvas is `#000000` pure black — not warm, not gray
2. Cash Green `#00D632` is the ONLY accent — Pay CTA, success states, active tab glyph
3. Cash Sans Mono on every $-amount and every $Cashtag — tabular numerals always
4. Screen titles are Cash Sans Black (weight 900) at 28pt — "Money", "Card", "Activity"
5. The Pay screen's giant amount is 96pt Cash Sans Mono Bold and dominates the upper half
6. Tab bar is icon-only — no labels, ever — and the center Pay tab is visually larger
7. Pay button has BLACK text on green, not white — this contrast is the signature
8. Cash Card is matte black with the user's $Cashtag in a chosen accent — no shadow
9. Activity row avatars use random saturated colors (pink, orange, purple) with white initial letter
10. No shadows anywhere — depth is fill-color contrast (`#000` → `#0F0F0F` → `#1A1A1A`)
