# Design System Inspiration of Panera (iOS)

## 1. Visual Theme & Atmosphere

Panera's iOS app feels like walking into a warm bakery-café. The canvas is a soft warm white (`#FFFFFF` with cream-tinted cards at `#F4EFE1`), never a clinical pure-white tech surface, because the brand is built on the idea of "fresh, warm, real food." The defining visual is **food photography**: every menu item — soups, sandwiches, salads, baked goods — is shown as a large, appetizing, full-color photo on a rounded card. Photography is the hero; the UI chrome recedes so the food does the selling. The single brand color is **Panera Green** (`#4C8B2B`, brightening toward `#6BBE45` for emphasis and dark mode), used for the primary "Add to Order" / "Checkout" buttons, active tabs, the MyPanera rewards progress arc, and reward tags — and almost nowhere else. Action is signaled by this one green; everything else is warm neutral.

The two signature UI moments are the **MyPanera rewards card** and the **menu-to-cart order flow**. The rewards card sits near the top of Home as a green gradient banner showing the member's progress toward their next reward (a progress bar or coin-counter), Unlimited Sip Club renewal status, and unlockable freebies. It is the loyalty engine that drives repeat visits. The order flow is the core transaction: browse a category, tap a photo card, land on an item-detail screen with a big hero photo, a quantity stepper, customization rows (bread choice, add-ons, "No tomatoes"), calorie counts, and a sticky green "Add to Order" bar. From there a cart with pickup/delivery toggle leads to a green checkout. This menu → customize → cart → checkout spine is the product.

The color system around the green is warm and edible: bread tan (`#D8B271`), soup orange (`#E07A2F`), berry red (`#B0324B`) and a gold review/loyalty star (`#F2B705`) recur on cards, badges, and the Sip Club mark. Backgrounds are cream and oat, never stark. The rewards system uses green as "earned/available" and gold as the celebratory "star reward" accent.

Typography is a friendly geometric sans for brand/display moments (Panera's marketing uses a rounded humanist face — **Poppins** is the closest free analog) paired with a highly legible neutral sans for body and menu copy (**Inter**). On iOS both fall back to SF Pro. Hierarchy is product-like and appetite-forward: large warm screen titles, bold item names, readable descriptions, and prominent prices. The type system stays approachable — it should read like a neighborhood bakery menu board, not a fintech dashboard.

**Key Characteristics:**
- Warm white canvas (`#FFFFFF`) light / warm near-black (`#14140F`) dark — bakery-warm, never clinical or cold-gray
- Cream-tinted surfaces (`#F4EFE1`) on cards and rows — the "paper menu" feel
- Food photography is the hero — large rounded photo cards do the selling
- A single brand action color: Panera Green `#4C8B2B` (bright `#6BBE45`) for CTAs, active tabs, rewards arc, tags
- MyPanera rewards card — green gradient banner with progress + Unlimited Sip Club status near top of Home
- Menu → item detail (hero photo + stepper + customization) → cart (pickup/delivery) → green checkout spine
- Gold star `#F2B705` as the celebratory "reward / Sip Club / rating" accent
- Quantity stepper and customization rows as the core ordering atoms
- Warm edible palette: bread tan, soup orange, berry red as photographic/badge accents
- Approachable geometric display sans + neutral body sans — neighborhood-bakery, not corporate
- Minimal navigation chrome: 5-tab bottom bar — Home / Order / Rewards / Favorites / Account

## 2. Color Palette & Roles

### Primary (Interactive)
- **Panera Green** (`#4C8B2B`): Primary interactive color — "Add to Order", "Checkout", "Start Order" button background, active selections.
- **Panera Green Bright** (`#6BBE45`): Emphasis / dark-mode action color, rewards progress fill, success accents, link-like text.
- **Panera Green Pressed** (`#3C6E22`): Pressed state for primary buttons.
- **Green Tint Surface** (`#EAF3E3`): 10% green wash behind selected order options and applied-reward rows (light).

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Warm Cream** (`#F4EFE1`): Card backgrounds, grouped rows, the "paper menu" surface.
- **Oat Surface** (`#FAF6EC`): Subtle elevated panels, search field fill.
- **Surface Pressed** (`#EDE6D4`): Pressed/selected rows.
- **Divider** (`#E7E0CF`): Hairline dividers between menu rows and sections.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#14140F`): Primary dark canvas — warm near-black, NOT cold gray.
- **Dark Surface 1** (`#1E1E18`): Cards, grouped rows, elevated surfaces.
- **Dark Surface 2** (`#28281F`): Pressed rows, stepper/chip fills.
- **Dark Divider** (`#33332A`): Hairline dividers.

### Text
- **Text Primary Light** (`#2A2A1F`): Primary body text — warm dark, NOT pure black.
- **Text Secondary Light** (`#6C6655`): Descriptions, metadata, calorie counts.
- **Text Tertiary Light** (`#9C9684`): Disabled, very low-emphasis.
- **Text Primary Dark** (`#F3EFE4`): Primary body on dark — warm cream-white.
- **Text Secondary Dark** (`#B4AE9C`): Secondary on dark.
- **Text Tertiary Dark** (`#76705F`): Disabled on dark.

### Brand / Photographic Accents

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Bread Tan | `#D8B271` | `#D8B271` |
| Soup Orange | `#E07A2F` | `#E88A42` |
| Berry Red | `#B0324B` | `#C44A63` |
| Gold Star (reward / rating / Sip Club) | `#F2B705` | `#F4C42E` |
| Sip Club Coffee | `#5A3A22` | `#7A5436` |

### Semantic
- **Success Green** (`#4C8B2B` light / `#6BBE45` dark): Order placed, reward unlocked.
- **Error Red** (`#D6452F` light / `#E0594B` dark): Card declined, item unavailable.
- **Warning Amber** (`#E07A2F`): Store closing soon, limited availability.
- **Info Green Tint** (`#EAF3E3` light / `#1E2A18` dark): Applied-reward / saved-favorite background.

## 3. Typography Rules

### Font Family
- **Display / Brand**: `Poppins` (by Indian Type Foundry / Jonny Pinhorn, SIL OFL) — geometric humanist sans, the closest free analog to Panera's warm rounded marketing face. Used for screen titles, item names, prices, reward headlines.
- **Body / UI**: `Inter` (by Rasmus Andersson, SIL OFL) — neutral high-legibility UI sans for descriptions, menu copy, metadata, calorie counts.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **iOS**: SF Pro is the system fallback when Poppins/Inter aren't bundled — keep the warm, friendly tone.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Poppins | 32pt | 800 | 1.2 | -0.5pt | "Good morning", screen headers |
| Hero / Reward Headline | Poppins | 26pt | 700 | 1.25 | -0.3pt | "You're 2 visits from a reward" |
| Section Header | Poppins | 22pt | 700 | 1.3 | -0.2pt | "Order Favorites", "Soups" |
| Card / Item Title | Poppins | 18pt | 600 | 1.3 | -0.1pt | Item-detail name, large card name |
| Item Name (list) | Poppins | 15pt | 600 | 1.35 | 0pt | Menu-row item name |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Item descriptions, paragraph copy |
| Meta / Calorie | Inter | 14pt | 400 | 1.4 | 0pt | "720 cal", "Ready in 12–17 min" |
| Price | Poppins | 14pt | 700 | 1.3 | 0pt | "$7.59" item price |
| Reward Tag | Poppins | 12pt | 700 | 1.0 | 0.4pt (uppercase) | "FREE WITH 2,500 PTS" |
| Button | Poppins | 16pt | 700 | 1.0 | 0.2pt | Primary/secondary buttons |
| Pill / Chip | Inter | 13pt | 600 | 1.0 | 0.1pt | Filter chips, pickup/delivery toggle |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Caption | Inter | 12pt | 500 | 1.4 | 0pt | Fine print, disclaimers |

### Principles
- **Appetite-forward hierarchy**: item names and prices are bold and prominent; descriptions stay readable but secondary — the photo + name + price is the unit.
- **Display vs body split**: Poppins carries brand/display warmth (titles, names, prices, reward headlines); Inter carries dense legible copy (descriptions, metadata).
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: regular body, medium captions, semibold names, bold prices and buttons, extrabold screen titles. No thin, no hairline.
- **Warm dark text, never pure black**: body text is `#2A2A1F`, preserving the bakery warmth.
- **Dynamic Type first-class**: titles, item names, body, prices, calories scale; tab labels, reward tags, chips stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button ("Add to Order" / "Checkout" / "Start Order")**
- Shape: Fully rounded pill, 999pt corner radius
- Background: `#4C8B2B` (Panera Green) light / `#6BBE45` dark
- Text: `#FFFFFF` light / `#14140F` dark
- Padding: 14pt vertical, 28pt horizontal (full-width sticky variant: 16pt vertical)
- Font: Poppins 16pt weight 700
- Pressed: background `#3C6E22` + scale 0.98
- Sticky cart variant: full-width, pinned bottom above tab bar, shows "Add to Order · $7.59"

**Secondary / Outline Button ("Customize" / "Add to Favorites")**
- Background: transparent
- Text: `#4C8B2B` light / `#6BBE45` dark
- Border: 1.5pt `#4C8B2B` light / `#6BBE45` dark
- Padding: 12pt vertical, 22pt horizontal
- Corner radius: 999pt (pill)
- Font: Poppins 14pt weight 600
- Pressed: background `#EAF3E3` light / `#1E2A18` dark

**Pill / Toggle Button (Pickup / Delivery, filter chips)**
- Background: `#FAF6EC` light / `#28281F` dark (selected: `#4C8B2B` / `#6BBE45`)
- Text: `#2A2A1F` / `#F3EFE4` (selected: `#FFFFFF` / `#14140F`)
- Border: 1pt `#E7E0CF` / `#33332A`
- Padding: 10pt vertical, 18pt horizontal
- Corner radius: 999pt
- Font: Inter 13pt weight 600

**Text Button ("View full menu" / "See all")**
- Font: Inter 14pt weight 600
- Color: `#4C8B2B` light / `#6BBE45` dark
- No underline; chevron `›` optional trailing
- Pressed: 60% opacity

### Core Atoms

**Menu Item Card (the core atom)**
- Layout: leading square food photo (92pt list / full-width hero on detail), trailing name + description + price + add button
- Photo corner radius: 14pt
- Name: Poppins 15–18pt w600
- Description: Inter 12–14pt w400 `#6C6655`, 2-line clamp
- Price: Poppins 14pt w700
- Add button: 30pt green circle with white `+` glyph
- Reward tag (optional): "★ FREE WITH 2,500 PTS" in Poppins 12pt w700 `#4C8B2B`
- Row divider: 0.5pt `#E7E0CF`

**MyPanera Rewards Card**
- Background: linear gradient `#4C8B2B → #6BBE45` (135°)
- Corner radius: 18pt
- Padding: 16pt all
- Label: "MYPANERA REWARDS" uppercase Poppins 11pt w700, 92% white
- Headline: "You're 2 visits from a reward" Poppins 19pt w700 `#FFFFFF`
- Subtext: Unlimited Sip Club / renewal status, Inter 12pt 92% white
- Progress bar: 6pt track `rgba(255,255,255,0.28)`, fill `#FFFFFF`, 3pt radius
- Trailing chevron `›` to rewards detail

**Quantity Stepper**
- Container: pill, `#FAF6EC` / `#28281F`, 1pt `#E7E0CF` / `#33332A` border
- − / + glyphs: Poppins 16pt `#4C8B2B` / `#6BBE45`, 44pt hit area each
- Count: Poppins 15pt w700 `#2A2A1F` / `#F3EFE4`, centered
- Spacing: 16pt between glyphs and count

**Customization Row**
- Full-width, 52pt tall, leading label (Inter 16pt), trailing radio/checkbox or stepper
- Selected: leading `#EAF3E3` tint / green checkmark `#4C8B2B`
- Add-on price suffix: "+$1.49" Inter 14pt `#6C6655`
- Divider: 0.5pt `#E7E0CF`

**Reward / Loyalty Chip**
- Pill, `#FAF6EC` / `#28281F`, 1pt border
- Leading 22pt green coin (gradient `#6BBE45 → #4C8B2B`)
- Text: "2,500 pts available" Poppins 13pt w700
- Used for available points, applied rewards, Sip Club status

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` / `#14140F` at 94% with blur, 0.5pt top border `#E7E0CF` / `#33332A`
- Tabs (5): Home, Order, Rewards, Favorites, Account
- Icon size: 22pt
- Active color: `#4C8B2B` light / `#6BBE45` dark (icon fills solid)
- Inactive: `#9C9684` light / `#76705F` dark
- Labels: Inter 10pt w600, always shown

**Top Nav (Item detail / Cart)**
- Height: 44pt + safe area, transparent over hero photo (scrolls to opaque)
- Leading: back chevron in a 32pt translucent circle over photo
- Trailing: heart (favorite) + share, same translucent circle treatment
- Title: appears Poppins 17pt w600 when scrolled past hero

**Category Strip (Order tab)**
- Horizontal scroll of category chips/photos: Soups, Sandwiches, Salads, Bowls, Bakery, Drinks
- Active category: green underline 2pt `#4C8B2B` / `#6BBE45`
- Chip text: Inter 13pt w600

### Input Fields

**Search Bar ("Find a menu item")**
- Height: 44pt
- Background: `#FAF6EC` / `#28281F`
- Border: none
- Corner radius: 999pt (pill)
- Leading `magnifyingglass` 16pt `#6C6655` / `#B4AE9C`
- Placeholder: "Search the menu" Inter 14pt `#9C9684`
- Focus: 1.5pt `#4C8B2B` border

**Address / Pickup Selector**
- Full-width row, 56pt, leading location pin (green), title + subtitle stack
- Title: "Pickup at Cherry Creek" Poppins 15pt w600
- Subtitle: "Ready in ~15 min" Inter 13pt `#6C6655`
- Trailing chevron, tap opens store/address sheet

### Distinctive Components

**MyPanera Rewards Card** — green-gradient progress banner; the loyalty engine pinned near top of Home (see Core Atoms).

**Unlimited Sip Club Badge**
- Coffee-cup glyph + "Unlimited Sip Club" label
- Background: Sip Club coffee `#5A3A22` or green; gold star accent for active membership
- Used on Home banner and account screen to surface subscription status/renewal

**Item Detail Hero**
- Full-bleed food photo, 280–340pt tall
- Name, calories, price overlaid below on cream surface
- Customization rows + quantity stepper
- Sticky green "Add to Order · $7.59" bar pinned bottom

**Reward Star Tag**
- Inline "★ FREE WITH 2,500 PTS" tag on redeemable items
- Gold star `#F2B705` + green text `#4C8B2B`
- Signals which menu items the member's points can unlock

**Pickup / Delivery Toggle**
- Segmented pill at top of cart
- Selected segment: green fill `#4C8B2B`, white text
- Unselected: transparent, `#6C6655` text

**Cart Summary Sheet**
- Bottom sheet listing line items with thumbnail, name, qty stepper, price
- Subtotal / rewards-applied / total rows
- Green "Checkout" pill pinned bottom
- Applied reward row tinted `#EAF3E3` with green checkmark

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 18, 20, 24, 28, 32, 40, 56
- Card vertical gap: 0 (rows divided by hairline) or 12pt (separated photo cards)
- Section top margin: 24pt
- Screen content inset: 16pt horizontal

### Grid & Container
- iPhone: content at 16pt horizontal insets; hero photos full-bleed
- iPad: content at max-width 720pt centered, 24pt insets; menu in 2-column grid
- Menu list: single column of rows; featured items in horizontal photo carousel
- Category strip: horizontal scroll, 12pt gap between chips

### Whitespace Philosophy
- **Food breathes**: photos get generous size and rounded corners; descriptions stay tight under names
- **Rewards card is the anchor**: it always sits in a calm zone near the top of Home with margin around it
- **Sticky CTA never crowds**: the green "Add to Order" bar floats with 16pt internal padding above the tab bar
- **Warm dividers, not boxes**: menu rows are separated by 0.5pt hairlines, not heavy card borders

### Border Radius Scale
- Square (0pt): Full-bleed hero photos, dividers
- Subtle (6pt): Inline tags, small badges
- Standard (10pt): Small thumbnails, chips
- Comfortable (14pt): Menu photo cards, list food images
- Card (18pt): Rewards card, sheets, large surfaces
- Large (24pt): Modal top corners
- Pill (999pt): Buttons, toggles, search bar, reward chips, steppers
- Circle (50%): Add buttons, avatars, nav circles, reward coins

## 6. Depth & Elevation

Panera uses gentle, warm elevation — soft shadows that suggest fresh layers, never harsh tech drop-shadows.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Menu rows, page content, dividers |
| Raised (Level 1) | `rgba(60,40,10,0.06) 0 2px 8px` | Photo cards, rewards card |
| Floating (Level 2) | `rgba(60,40,10,0.10) 0 4px 16px` | Sticky CTA bar, store selector |
| Sheet (Level 3) | `rgba(40,30,10,0.16) 0 -4px 24px` | Cart sheet, customization sheet, store picker |
| Modal Overlay | `rgba(20,15,5,0.4)` | Dim behind sheets and modals |

**Shadow Philosophy**: shadows are warm-tinted (brown-amber, not neutral gray) so they read as natural depth against cream surfaces. The rewards card and food cards get a soft raise so they feel tangible; the sticky checkout bar floats just enough to read as "always available". On dark mode, shadows nearly vanish, so a 1pt `#33332A` divider/hairline becomes the elevation cue on sheets and the sticky bar.

### Motion
- **Add to Order**: tapping the green CTA triggers a 200ms scale-bounce + the cart badge increments with a soft haptic; a brief "Added" toast slides up 250ms ease-out
- **Quantity stepper**: count number cross-fades + ticks 120ms; soft haptic on each tap
- **Rewards progress**: progress fill animates from old → new value over 600ms ease-out when a reward is earned; gold star sparkle on unlock
- **Category switch**: underline slides between categories 200ms ease-out; menu list cross-fades
- **Item detail open**: photo card expands into full-bleed hero with a 300ms shared-element-style transition (iOS push)
- **Cart sheet**: slides up 300ms ease-out; backdrop dims simultaneously
- **Pickup/Delivery toggle**: selected fill slides 180ms ease-out
- **Pull to refresh (Home)**: bread-loaf / Panera mark spinner
- **Haptic feedback**: soft impact on Add to Order, stepper tap, reward unlock, toggle switch

## 7. Do's and Don'ts

### Do
- Use warm white (`#FFFFFF`) with cream cards (`#F4EFE1`) in light — Panera is a bakery, not a tech app
- Use warm near-black (`#14140F`) for dark mode — NOT cold gray, NOT pure black
- Use warm dark text (`#2A2A1F`) — NOT pure black
- Keep Panera Green (`#4C8B2B`, bright `#6BBE45`) as the single action color — CTAs, active tabs, rewards arc
- Make food photography the hero — large rounded photo cards do the selling
- Pin the MyPanera rewards card near the top of Home — it's the loyalty engine
- Use the gold star (`#F2B705`) only for reward / Sip Club / rating moments
- Use fully-rounded pills for buttons and toggles — Panera's UI is soft and rounded
- Pin a sticky green "Add to Order · $price" bar on item detail
- Show calorie counts next to prices — it's a Panera signature
- Use the quantity stepper + customization rows as the ordering atoms

### Don't
- Don't use pure white tech surfaces — the cream warmth is the brand
- Don't introduce a second brand action color — green is the only CTA color
- Don't use cold gray dark mode — keep it warm `#14140F`
- Don't use pure black (`#000000`) for body text — `#2A2A1F` warm dark is correct
- Don't make the green dark variant disappear on dark — use `#6BBE45` for legible CTAs
- Don't bury the MyPanera rewards card — it must be prominent on Home
- Don't over-use the gold star — it's the celebratory loyalty accent, not decoration
- Don't use sharp rectangular buttons — Panera buttons are pills
- Don't hide prices or calories — they belong next to the item name
- Don't add heavy card borders around menu rows — use warm 0.5pt hairlines
- Don't animate aggressively — motion is gentle 180–600ms ease-out

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | List photos 80pt; hero photo 240pt; single-column menu |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in transparent top nav |
| iPhone 15/16 Pro Max | 430pt | List photos 100pt; hero photo 360pt |
| iPad (portrait) | 768pt | 2-column menu grid; rewards card spans top; content max 720pt centered |
| iPad (landscape) | 1024pt+ | 3-column menu grid; persistent category sidebar |

### Dynamic Type
- Screen titles, item names, descriptions, prices, calories: full scale
- Tab labels, reward tags, filter chips, stepper glyphs: FIXED (layout-sensitive)
- Photo card minimum sizes preserved; text truncates with ellipsis at extreme scales

### Orientation
- All screens support rotation
- iPad split: category sidebar (landscape) + menu grid + cart panel
- Hero photos rotate freely; sticky CTA bar stays pinned bottom

### Touch Targets
- Add button (circle): 30pt glyph, 44pt hit area
- Stepper − / +: 24pt glyph, 44pt hit area
- Tab bar icons: 22pt glyph, 44pt hit area
- Menu row: full-row tap, ≥ 100pt tall
- Primary buttons: 48pt tall minimum

### Safe Area Handling
- Top: transparent nav respects safe area and Dynamic Island over hero photo
- Bottom: sticky CTA bar and tab bar respect home indicator
- Keyboard: search and address fields scroll above keyboard
- Sides: 16pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Card surface (light): `#F4EFE1`
- Canvas (dark): `#14140F`
- Surface (dark): `#1E1E18`
- Divider: `#E7E0CF` light / `#33332A` dark
- Text primary: `#2A2A1F` light / `#F3EFE4` dark
- Text secondary: `#6C6655` light / `#B4AE9C` dark
- Panera Green (primary): `#4C8B2B`
- Panera Green Bright (dark CTA / accent): `#6BBE45`
- Panera Green Pressed: `#3C6E22`
- Green tint surface: `#EAF3E3` light / `#1E2A18` dark
- Gold star (reward): `#F2B705`
- Bread tan: `#D8B271`
- Soup orange: `#E07A2F`
- Berry red: `#B0324B`
- Error: `#D6452F` light / `#E0594B` dark

### Example Component Prompts
- "Build the Panera MyPanera rewards card in SwiftUI: full-width rounded rectangle, 18pt corner radius, linear gradient `#4C8B2B → #6BBE45` at 135°, 16pt padding. Top row: 'MYPANERA REWARDS' uppercase Poppins 11pt w700 white at 92% opacity, trailing chevron. Headline: 'You're 2 visits from a reward' Poppins 19pt w700 `#FFFFFF`. Subtext: 'Unlimited Sip Club renews May 28' Inter 12pt white 92%. Bottom: 6pt progress track `rgba(255,255,255,0.28)` with white fill at 64%, 3pt radius. Soft warm shadow `rgba(60,40,10,0.06) 0 2px 8px`."

- "Create a Panera menu item row: leading 92pt square food photo with 14pt corner radius, trailing column with name in Poppins 15pt w600 `#2A2A1F`, description in Inter 12pt w400 `#6C6655` clamped to 2 lines, optional reward tag '★ FREE WITH 2,500 PTS' in Poppins 12pt w700 `#4C8B2B`, then a foot row with price 'Poppins 14pt w700' and a 30pt green circle add-button with a white `+`. Row divider 0.5pt `#E7E0CF`."

- "Build the Panera item-detail screen: full-bleed food hero photo 320pt tall, transparent top nav with back/heart/share in 32pt translucent circles. Below on cream `#F4EFE1`: item name Poppins 18pt w600, '720 cal · $7.59' Inter 14pt `#6C6655`. Customization rows (52pt, label + radio/checkbox, '+$1.49' suffix). Quantity stepper pill. Sticky bottom bar: full-width Panera Green `#4C8B2B` pill, Poppins 16pt w700 white, 'Add to Order · $7.59', floating shadow `rgba(60,40,10,0.10) 0 4px 16px`."

- "Render the Panera quantity stepper: pill container `#FAF6EC` (dark `#28281F`) with 1pt `#E7E0CF` border, − and + glyphs in Poppins 16pt `#4C8B2B` (dark `#6BBE45`) with 44pt hit areas, centered count in Poppins 15pt w700 `#2A2A1F`. Soft haptic + 120ms count cross-fade on each tap."

- "Build the Panera primary button: fully-rounded pill (999pt radius), background `#4C8B2B` (dark `#6BBE45`), text `#FFFFFF` (dark `#14140F`) in Poppins 16pt w700, 14pt vertical / 28pt horizontal padding. Pressed: background `#3C6E22` + scale 0.98 + soft haptic."

- "Render the Panera bottom tab bar: 56pt + safe area, background `#FFFFFF` / `#14140F` at 94% with blur, 0.5pt top border. 5 tabs — Home, Order, Rewards, Favorites, Account — 22pt icons, active `#4C8B2B` (dark `#6BBE45`) with solid fill, inactive `#9C9684` (dark `#76705F`), Inter 10pt w600 labels always shown."

### Iteration Guide
1. Canvas is warm white `#FFFFFF` with cream cards `#F4EFE1` in light, warm near-black `#14140F` in dark — never cold gray, never pure black
2. Text primary is warm dark `#2A2A1F` — NOT pure black; body Inter 400, names Poppins 600+
3. Panera Green `#4C8B2B` (bright `#6BBE45` for dark/accent) is the ONLY action color — CTAs, active tabs, rewards arc, tags
4. Food photography is the hero — large rounded photo cards do the selling
5. The MyPanera rewards card (green gradient + progress) is the loyalty engine pinned near top of Home
6. Buttons and toggles are fully-rounded pills — Panera's UI is soft
7. Gold star `#F2B705` is reserved for reward / Sip Club / rating moments only
8. The order spine is menu → item detail (hero photo + stepper + customization) → cart (pickup/delivery) → green checkout
9. Show calories next to prices — it's a Panera signature
10. Motion is gentle 180–600ms ease-out; soft haptics on Add to Order, stepper, reward unlock
