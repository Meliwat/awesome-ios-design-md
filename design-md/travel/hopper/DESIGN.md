# Design System Inspiration of Hopper (iOS)

## 1. Visual Theme & Atmosphere

Hopper's iOS app is a clean, confident white canvas (`#FFFFFF`) built around a single emotional idea: *should you buy this flight now, or wait?* Everything in the interface is engineered to answer that question with color. The signature mechanic is the price-prediction calendar — a month grid where each day cell is tinted by Hopper's prediction: green means "good price, buy", red means "expensive, wait", with a smooth heatmap in between. This is a decision app dressed as a travel app; the chrome stays light and quiet so the green/red signal can shout.

The accent system is a playful, friendly red. Hopper Red (`#FA4747`) — the bunny-mascot red — owns the primary CTA ("Watch this trip", "Book"), the brand bunny illustrations, and the "wait" end of the prediction scale. A confident green (`#34C759`) carries the "buy now" verdict and price-drop confirmations. Beyond red, green, and the friendly bunny black, the palette is grayscale: white surfaces, a soft `#F5F5F7` for grouped cards, `#E5E5EA` hairline dividers. The personality comes from the bunny — Hopper's mascot pops up in empty states, prediction confidence callouts, and celebratory moments — but the data UI itself stays disciplined.

Typography is a rounded, approachable sans (Hopper uses a custom geometric face in production; substitute "Inter"). The hierarchy is compact and data-forward: 12-28pt with weights at 400 / 600 / 700. The biggest, boldest type moment is the predicted-price headline on a fare card — a large 28pt bold number paired with a colored confidence pill ("Buy — prices won't drop"). Body copy and metadata recede into gray so the prediction always reads first.

**Key Characteristics:**
- Clean white canvas (`#FFFFFF`) with friendly Hopper Red (`#FA4747`) as the brand accent
- Price-prediction calendar heatmap — green=buy / red=wait cells, the core mechanic
- Buy-green (`#34C759`) vs wait-red (`#FA4747`) as a semantic decision pair
- Watch-price toggle — a pill switch that arms price tracking for a route
- Fare card with prediction confidence — large price + colored verdict pill
- Bunny mascot — playful illustrations in empty states and celebratory moments
- Bottom tab bar (Home / Watches / Trips / Profile) with red active state
- Haptics on calendar date selection, watch-toggle arm, and price-drop alerts

## 2. Color Palette & Roles

### Primary
- **Hopper Red** (`#FA4747`): Primary CTA, brand bunny red, active tab indicator, "wait" verdict, watch-toggle armed state.
- **Red Pressed** (`#E03A3A`): Active/pressed state for red CTAs.
- **Buy Green** (`#34C759`): "Buy now" verdict, price-drop confirmation, "good price" calendar cells.

### Calendar Heatmap (Prediction Scale)
- **Buy (Best)** (`#34C759`): Cheapest predicted days — strong green fill.
- **Buy (Good)** (`#A8E6B8`): Below-average price days — light green tint.
- **Neutral** (`#F5F5F7`): Average / unpredicted days — surface gray.
- **Wait (High)** (`#FFC7C7`): Above-average price days — light red tint.
- **Wait (Worst)** (`#FA4747`): Most expensive predicted days — strong red fill.

### Canvas & Surface
- **Canvas White** (`#FFFFFF`): Primary canvas behind all content.
- **Surface** (`#F5F5F7`): Grouped card backgrounds, neutral calendar cells, segmented controls.
- **Divider** (`#E5E5EA`): Hairline dividers between list rows and sections.
- **Surface Pressed** (`#EBEBF0`): Pressed state on light list rows and cards.

### Text
- **Text Primary** (`#1D1D1F`): Headlines, predicted prices, primary UI text.
- **Text Secondary** (`#6E6E73`): Route metadata, dates, captions, helper copy.
- **Text Tertiary** (`#A1A1A6`): Disabled labels, placeholders, low-emphasis hints.
- **Bunny Black** (`#1D1D1F`): Mascot silhouette outline, icon glyphs at full emphasis.

### Semantic
- **Watch On** (`#FA4747`): Armed watch-toggle track + bell.
- **Watch Off** (`#E5E5EA`): Disarmed watch-toggle track.
- **Price Drop** (`#34C759`): "Price dropped $40" badge and alert accent.
- **Error Red** (`#D70015`): Form validation, booking failure (a deeper, non-brand red).

### Dark Mode (Limited Use)
Hopper's iOS app is light-first. A dark variant exists for system-dark contexts; the heatmap and brand red/green stay identical — they carry the meaning.
- **Dark Canvas** (`#0F0F0F`)
- **Dark Surface** (`#1C1C1E`)
- **Dark Text Primary** (`#FFFFFF`)
- **Dark Text Secondary** (`#A0A0A0`)
- **Red** (`#FA4747`) / **Green** (`#34C759`) — unchanged across themes

## 3. Typography Rules

### Font Family
- **Primary**: Hopper's proprietary geometric sans (rounded, friendly)
- **Substitute**: `Inter` — closest free face for the approachable geometric tone
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Non-Latin**: System fallback per-script — Hopper ships globally

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Predicted Price | Inter | 28pt | 700 | 1.1 | -0.5pt | The big fare number on a fare card |
| Screen Title (Large) | Inter | 28pt | 700 | 1.15 | -0.4pt | Home / Watches large nav title |
| Section Header | Inter | 22pt | 700 | 1.2 | -0.3pt | "Your watches", "When to book" |
| Route / Card Title | Inter | 17pt | 700 | 1.25 | -0.1pt | "SFO → JFK" on a fare card |
| Card Title | Inter | 16pt | 600 | 1.3 | -0.1pt | Secondary cards, list titles |
| Body | Inter | 15pt | 400 | 1.45 | 0pt | Prediction explanation, descriptions |
| Button (Primary) | Inter | 16pt | 700 | 1.0 | 0pt | Red "Watch this trip", "Book" |
| Verdict Pill | Inter | 13pt | 700 | 1.0 | 0.2pt | "BUY", "WAIT" colored pill label |
| Meta / Count | Inter | 13pt | 400 | 1.3 | 0pt | "Nonstop · 5h 42m", "Oct 12 – 19" |
| Calendar Day | Inter | 14pt | 600 | 1.0 | 0pt | Day number inside a heatmap cell |
| Calendar Price | Inter | 10pt | 600 | 1.0 | 0pt | Tiny price under the day number |
| Tab Label | Inter | 11pt | 600 | 1.0 | 0.2pt | Bottom tab bar labels |

### Principles
- **Weights concentrated at 400 / 600 / 700**: regular, semibold, bold — no thin, no black
- **The predicted price is the hero**: 28pt bold, always paired with a colored verdict pill
- **Numbers dominate**: prices, durations, dates carry the screen; copy is supporting gray
- **Negative tracking on big prices/titles**: -0.5pt on the price number for a tight, confident feel
- **Color, not weight, signals the verdict** — green/red pill does the talking

## 4. Component Stylings

### Buttons

**Primary CTA ("Watch this trip" / "Book")**
- Background: `#FA4747`
- Text: `#FFFFFF`
- Padding: 16pt vertical, 28pt horizontal
- Corner radius: 14pt
- Font: Inter 16pt weight 700
- Pressed: background `#E03A3A`, scale 0.98, with `.impactOccurred(.medium)` haptic
- Disabled: background `#E5E5EA`, text `#A1A1A6`

**Secondary Button ("See all flights")**
- Background: `#F5F5F7`
- Text: `#1D1D1F`
- Padding: 14pt vertical, 24pt horizontal
- Corner radius: 14pt
- Font: Inter 15pt weight 600
- Pressed: background `#EBEBF0`

**Watch-Price Toggle**
- Track: 52pt × 32pt pill; off `#E5E5EA`, on `#FA4747`
- Knob: 28pt white circle with `rgba(0,0,0,0.12) 0 2px 4px` shadow
- Bell icon appears in the knob when armed
- Switch animation: 220ms spring + success haptic on arm

**Text Button ("Why this prediction?")**
- Font: Inter 15pt weight 600
- Color: `#FA4747`
- No underline, 44pt hit area

### Cards & Containers

**Fare Card (with prediction)**
- Background: `#FFFFFF`, border 1pt `#E5E5EA`, corner radius 16pt
- Shadow: `rgba(0,0,0,0.06) 0 4px 16px`
- Top row: route "SFO → JFK" (17pt w700) + airline + "Nonstop · 5h 42m" (13pt `#6E6E73`)
- Predicted price: 28pt w700 `#1D1D1F`, e.g. "$247"
- Verdict pill: colored pill — green "BUY · prices won't drop" or red "WAIT · likely cheaper soon"
- Confidence row: a 4-segment confidence bar + "Hopper is 87% confident"
- CTA: full-width red "Watch this trip" or "Book now"

**Watch List Row**
- Height: 80pt, background `#FFFFFF`, divider `#E5E5EA`
- Leading bunny/route icon, route title 16pt w600, dates 13pt `#6E6E73`
- Trailing: current price + delta chip ("↓ $40" in green) + watch bell
- Tap: push to fare detail; pressed background `#F5F5F7`

**Prediction Confidence Bar**
- 4 rounded segments, 6pt tall, 4pt gap
- Filled segments: verdict color (green for buy, red for wait)
- Empty segments: `#E5E5EA`
- Label below: "Hopper is 87% confident — based on 1B+ trips"

**Bunny Empty State**
- Centered bunny illustration (~120pt) in Hopper Red
- Headline 17pt w700, body 15pt w400 `#6E6E73`
- CTA pill below — keeps the playful brand voice in dead-end screens

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `rgba(255,255,255,0.96)` with `.regularMaterial` blur
- Tabs: Home, Watches, Trips, Profile
- Icon size: 24pt
- Active: `#FA4747` icon + label (red is the active indicator)
- Inactive: `#6E6E73`
- Labels: Inter 11pt w600, always shown
- Active icon: filled SF Symbol; inactive: outlined
- Hairline top border `#E5E5EA`

**Top Nav (Home)**
- Height: 52pt + safe area
- Title left (28pt w700 `#1D1D1F`), profile avatar right (28pt circle)
- Origin → destination search card pinned below: white, 1pt `#E5E5EA` border, 16pt radius
- Compact + blurred on scroll

**Search Card (Trip Builder)**
- White card, 16pt radius, 1pt `#E5E5EA`
- Stacked rows: "From", "To", "Dates", "Travelers" — each a 56pt tappable row with a hairline divider
- Swap-route circular button (red) on the From/To divider

### Input Fields

**Search Field (City picker)**
- Background: `#F5F5F7`
- Height: 52pt
- Corner radius: 14pt
- Leading SF Symbol `magnifyingglass` 18pt `#6E6E73`
- Placeholder: "City or airport", 15pt w400 `#6E6E73`
- Focus: background `#FFFFFF`, 1.5pt border `#FA4747`

**Filter Chip**
- Background: `#F5F5F7` default / `#FA4747` selected
- Text: `#1D1D1F` default / `#FFFFFF` selected
- Padding: 8pt vertical, 16pt horizontal
- Corner radius: 999pt
- Font: Inter 14pt weight 600
- Examples: "Nonstop", "1 stop", "Mornings", "Under $300"

### Distinctive Components

**Price-Prediction Calendar Heatmap**
- Month grid, 7 columns; each day a rounded-8pt cell, ~44pt tall
- Cell fill follows the prediction scale: `#34C759` (best buy) → `#A8E6B8` → `#F5F5F7` (neutral) → `#FFC7C7` → `#FA4747` (worst wait)
- Day number 14pt w600; tiny predicted price 10pt w600 beneath it
- Selected day: 2pt `#1D1D1F` ring around the cell
- Legend strip above the grid: a green→red gradient bar labeled "Buy" ⟶ "Wait"
- Tap a day: cell scales 1.0 → 1.05 → 1.0, light haptic, fare updates below

**Fare Verdict Pill**
- Buy: background `#34C759`, white text "BUY"
- Wait: background `#FA4747`, white text "WAIT"
- Padding: 5pt vertical, 12pt horizontal, 999pt radius, 13pt w700 uppercase
- Sits beside the predicted price; the single most important glance on the screen

**Watch-Price Toggle Card**
- A card with route summary + the watch toggle on the right
- When armed: card border tints red, a "Watching — we'll alert you" line appears with the bunny

**Bunny Mascot**
- Hopper Red rounded bunny, used in empty states, "price dropped" celebration sheets, and onboarding
- Never recolored away from `#FA4747`; expresses the friendly brand voice around hard data

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 56
- Standard margin: 16pt horizontal, 20pt between cards, 24pt between sections
- Calendar cell gap: 4pt; grid horizontal inset 16pt

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Calendar: 7-column equal grid, 4pt gutter, square-ish 44pt cells
- Fare cards: single column, full-width, 20pt vertical gap
- Watch list: full-width rows with hairline dividers

### Whitespace Philosophy
- **Data gets room**: the predicted price and verdict pill sit in a generous 24pt-padded card block
- **Calendar is dense but legible**: 44pt cells with 4pt gaps pack a month while staying tappable
- **Quiet chrome**: nav and list rows are tight so the heatmap and price hero dominate

### Border Radius Scale
- Square (0pt): hairline dividers
- Cell (8pt): calendar day cells
- Control (14pt): primary/secondary buttons, search fields
- Card (16pt): fare cards, search card, watch rows
- Comfortable (20pt): bottom sheets, celebration modals
- Full Pill (999pt): verdict pills, filter chips, watch toggle
- Circle (50%): avatars, swap-route button, toggle knob

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, canvas, calendar cells |
| Card (Level 1) | `rgba(0,0,0,0.06) 0 4px 16px` | Fare cards, search card lifted off white |
| Toggle Knob (Level 2) | `rgba(0,0,0,0.12) 0 2px 4px` | Watch-toggle knob |
| Sheet (Level 3) | `rgba(0,0,0,0.16) 0 -8px 32px` | Bottom sheets, price-drop celebration |
| Sticky Nav | `rgba(0,0,0,0.06) 0 1px 0` + blur | Search card / nav on scroll |
| Tab Bar Material | `.regularMaterial` over `rgba(255,255,255,0.96)` | Bottom tab bar over scrolling content |

**Shadow Philosophy**: Shadows are soft, low-opacity, and used sparingly to lift the fare card off the white canvas (0.06 opacity, 16pt blur). The calendar cells are intentionally flat — their *color* is the signal, not their elevation. The only crisp shadow is the toggle knob so the watch switch feels physical. Everything else stays flat and bright.

### Motion
- **Calendar date select**: tapped cell scales 1.0 → 1.05 → 1.0 over 200ms, light haptic, fare card below cross-fades to the new prediction
- **Calendar color shift**: when switching months/filters, cell fills tween color over 300ms (the heatmap "breathes")
- **Watch toggle arm**: knob springs across 220ms, track color crossfades to red, success haptic, bunny waves
- **Price-drop alert**: celebration sheet slides up with the bunny + a green "↓ $40" counter ticking down
- **Verdict pill**: subtle scale-in 1.0 → 1.06 → 1.0 when a new prediction loads
- **CTA press**: scale 0.98 spring (damping 0.8) + medium haptic

## 7. Do's and Don'ts

### Do
- Use `#FFFFFF` as the canvas — keep the data-decision UI clean and bright
- Drive the calendar with the prediction heatmap: green=buy, red=wait, smooth tint between
- Pair every predicted price with a colored verdict pill (green "BUY" / red "WAIT")
- Use Hopper Red (`#FA4747`) for the primary CTA, active tab, and the bunny
- Use buy-green (`#34C759`) only for positive verdicts and price drops
- Keep the bunny mascot the friendly voice in empty/celebration states — always `#FA4747`
- Make calendar cells flat — color is the signal, not shadow
- Show prediction confidence as a segmented bar with an explicit percentage
- Keep metadata (durations, dates, stops) in `#6E6E73` gray

### Don't
- Don't tint the canvas — keep it pure `#FFFFFF`
- Don't use red and green decoratively — they are a strict semantic pair (wait vs buy)
- Don't bury the verdict — the green/red pill must be the strongest glance on the fare card
- Don't add shadows to calendar cells — they should read as a flat heatmap
- Don't recolor the bunny — it is always Hopper Red
- Don't use thin or black font weights — stay at 400 / 600 / 700
- Don't use the deep error red (`#D70015`) for "wait" — wait is the friendlier brand red
- Don't shrink calendar cells below ~44pt — they must stay tappable
- Don't crowd the predicted price — it gets the 28pt bold + room to breathe

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Calendar cells shrink to ~40pt; tiny price hidden below day number |
| iPhone 13/14/15 | 390pt | Standard 44pt heatmap cells with price labels |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | Larger 48pt cells, predicted price scales to 32pt |
| iPad | 768pt+ | Two-month calendar side by side, fare detail in a split layout |

### Dynamic Type
- Predicted price, route titles, body: full scale
- Verdict pill, calendar day/price labels: fixed (layout-sensitive grid)
- Tab labels: fixed
- Prediction explanation body: scales with line wrap

### Orientation
- Home / Watches / Trips: **portrait-locked**
- Calendar: **portrait-locked** (grid is tuned for portrait width)
- Fare detail: **portrait-locked**

### Touch Targets
- Primary CTA: 48pt+ tall
- Calendar day cell: ~44pt — full cell tappable
- Watch toggle: 52×32pt track, 44pt effective hit
- Tab bar icons: 24pt glyph, 44pt effective hit

### Safe Area Handling
- Top: nav + search card respect the safe area / Dynamic Island
- Bottom: tab bar floats above the home indicator with blur
- Sides: 16pt content insets; calendar grid respects the same insets

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface: `#F5F5F7`
- Divider: `#E5E5EA`
- Text primary: `#1D1D1F`
- Text secondary: `#6E6E73`
- Hopper Red (action / wait): `#FA4747`
- Red pressed: `#E03A3A`
- Buy green: `#34C759`
- Heatmap buy→wait: `#34C759` · `#A8E6B8` · `#F5F5F7` · `#FFC7C7` · `#FA4747`
- Error red: `#D70015`

### Example Component Prompts
- "Create a SwiftUI Hopper price-prediction calendar: a 7-column month grid of rounded-8pt cells (~44pt). Each cell's fill follows a prediction scale — strong green `#34C759` for cheapest, light green `#A8E6B8`, neutral `#F5F5F7`, light red `#FFC7C7`, strong red `#FA4747` for most expensive. Show the day number in Inter 14pt weight 600 and a tiny predicted price 10pt weight 600 beneath. Selected day gets a 2pt `#1D1D1F` ring. A green→red gradient legend bar labeled 'Buy ⟶ Wait' sits above the grid."
- "Build a Hopper fare card: white card, 16pt radius, 1pt `#E5E5EA` border, soft shadow. Route 'SFO → JFK' 17pt weight 700 + 'Nonstop · 5h 42m' 13pt `#6E6E73`. Predicted price '$247' 28pt weight 700 `#1D1D1F`. A green verdict pill 'BUY · prices won't drop' (`#34C759` bg, white text, 13pt w700 uppercase, full pill). A 4-segment confidence bar + 'Hopper is 87% confident'. Full-width red 'Watch this trip' button (`#FA4747`, white, 14pt radius)."
- "Design a Hopper watch-price toggle: a 52×32pt pill switch, off track `#E5E5EA`, on track `#FA4747`, a 28pt white knob with a soft shadow that shows a small bell icon when armed. 220ms spring animation, success haptic on arm."
- "Create a Hopper fare verdict pill component with two states: 'BUY' on `#34C759` and 'WAIT' on `#FA4747`, both white 13pt weight 700 uppercase, 5pt vertical / 12pt horizontal padding, full-pill radius — placed next to the 28pt predicted price."
- "Build a Hopper bunny empty state: a centered Hopper-Red bunny illustration (~120pt), headline 'No watches yet' 17pt weight 700 `#1D1D1F`, body 'Watch a trip and we'll tell you when to book' 15pt weight 400 `#6E6E73`, and a red 'Watch a trip' CTA pill below."

### Iteration Guide
1. Canvas is pure `#FFFFFF` — clean, bright, data-forward
2. The calendar heatmap is the core mechanic: green=buy, red=wait, smooth tint between (`#34C759`→`#FA4747`)
3. Every predicted price gets a colored verdict pill — green "BUY" or red "WAIT"
4. Hopper Red (`#FA4747`) = primary action + active tab + bunny; buy-green (`#34C759`) = positive verdict only
5. Calendar cells are flat — color is the signal, never shadow
6. The bunny mascot is the friendly voice; always Hopper Red, never recolored
7. Predicted price is the hero: 28pt bold, -0.5pt tracking, room to breathe
8. Keep metadata gray (`#6E6E73`): durations, dates, stop counts
