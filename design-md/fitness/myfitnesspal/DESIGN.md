# Design System Inspiration of MyFitnessPal (iOS)

## 1. Visual Theme & Atmosphere

MyFitnessPal's iOS app is a calorie ledger trying its hardest not to feel like one. Where Strava is theatrical and Whoop is clinical, MyFitnessPal is a *spreadsheet with a kind face* — a polished food diary that has logged a billion meals and learned to make data entry feel like progress. The canvas is pure white, the chrome is light blue, and the unmistakable hero of the home screen is the giant ring of calories remaining: a thick circular gauge that flips from light gray (untouched) to MFP Blue (filled) as the day's eating accumulates. Every screen is in service of that ring — log a food, scan a barcode, add an exercise, and the ring redraws.

The accent is **MFP Blue** (`#005DAA`) — a confident, slightly desaturated navy-blue that has been the brand color since the Under Armour acquisition in 2015, with a brighter modernized **Lake Blue** (`#0072CE`) introduced in the 2022 refresh that's now used on most CTAs and the calorie ring fill. The palette is otherwise clinical: white canvas, gentle `#F5F7FA` surface for cards and food-log rows, charcoal `#1F2937` text, and a small set of semantic accents — green for "under your goal", orange for "approaching limit", red for "over". The macro donut (Carbs / Fat / Protein) uses a fixed trio — `#FF9F1C` orange, `#A463F2` purple, `#19C37D` green — that's been stable for years and is instantly recognizable to any returning user.

Typography is **SF Pro** on iOS, with the brand sometimes substituting **Proxima Nova** for marketing — both are no-nonsense geometric sans faces that prioritize legibility of numerals. The numerals are the whole point: MyFitnessPal is a numbers app, and big tabular-figure totals (`1,847`, `−312`, `64g`) carry the screen. Type uses tight tabular numerals on the calorie ring, the macro percentages, the goal weight, and every gram count in the diary; everything else is the system default proportional.

**Key Characteristics:**
- White canvas (`#FFFFFF`) with Surface Gray (`#F5F7FA`) for diary rows, food-database results, and section fills
- MFP Blue (`#005DAA`) as the persistent brand anchor; Lake Blue (`#0072CE`) as the modernized action color for CTAs, ring fills, and active tabs
- The Calorie Remaining donut — 220pt diameter on home, a giant tabular-figure number in the middle, color flips green → blue → orange → red as you eat through the day
- Macro donut trio — orange `#FF9F1C` Carbs, purple `#A463F2` Fat, green `#19C37D` Protein — never reorder these
- Diary screen organized as meal sections (Breakfast / Lunch / Dinner / Snacks) with a leading "+" Add Food row in each
- Barcode scanner is a hero feature — a dedicated centered FAB on the Diary tab pops a full-screen camera with a horizontal scan line
- Tab bar (5 tabs): Dashboard, Diary, Plans, Community, More — bottom-anchored with iconic SF Symbols
- SF Pro at every size with **tabular numerals** on every number that summarizes nutrition
- Subtle elevation only — MyFitnessPal is a content-first app, drop shadows are 4% opacity and short

## 2. Color Palette & Roles

### Primary
- **MFP Blue** (`#005DAA`): Heritage primary — logomark "M" plate, Premium upsell banner, large nav title accents.
- **Lake Blue** (`#0072CE`): Current primary action color — Add Food CTA, ring fill at "on track", active tab tint, link color in food labels.
- **Lake Blue Pressed** (`#005FA8`): Pressed state on all Lake Blue CTAs.
- **Sky Blue** (`#E7F0FF`): Background tint for selected meal sections, info pills, "Quick Tools" cards.

### Macro Trio (do not reorder, do not recolor)
- **Carbs Orange** (`#FF9F1C`): Always the first slice of the macro donut.
- **Fat Purple** (`#A463F2`): Always the second slice.
- **Protein Green** (`#19C37D`): Always the third slice.

### Canvas, Surfaces & Dividers
- **Canvas White** (`#FFFFFF`): Primary canvas everywhere.
- **Surface Gray** (`#F5F7FA`): Diary section backgrounds, food-row backgrounds on tap-down, dashboard card fills.
- **Surface Gray 2** (`#E5E9F0`): Chip fills, input fields, progress-bar tracks.
- **Divider** (`#E5E7EB`): Hairline dividers between diary rows, between meals, under the nav bar.
- **Hairline** (`#EEF1F5`): Lighter divider used inside dashboard cards.

### Text
- **Ink** (`#1F2937`): Primary text — food names, meal section titles, numeric totals.
- **Slate** (`#4B5563`): Secondary text — serving size text, gram counts in metadata position, body copy.
- **Mute** (`#9CA3AF`): Tertiary — placeholder, disabled, "0 of 1 servings" empty hints.
- **Hero Number** (`#111827`): The big calorie-remaining number — even darker than Ink for emphasis.

### Semantic
- **Under Goal Green** (`#19C37D`): "−312 cal remaining" when under the goal, weight-loss confirmation.
- **Approaching Amber** (`#F59E0B`): When you're within 200 cal of your limit.
- **Over Limit Red** (`#EF4444`): When you've exceeded the daily goal, weight-gain bar.
- **Streak Orange** (`#FB923C`): The fire glyph for daily-streak badges.
- **Premium Gold** (`#D4A437`): Premium subscription badges, gated-feature locks.

### Dark Mode
MyFitnessPal added dark mode in 2020. It's a deep, slightly-blue dark — not pure black.
- **Dark Canvas** (`#0F1419`): Primary dark background.
- **Dark Surface 1** (`#1A1F26`): Cards, diary rows, sheets.
- **Dark Surface 2** (`#252B33`): Pressed states, chip fills.
- **Dark Divider** (`#2A2F37`): Hairlines.
- **Dark Text Primary** (`#F3F4F6`): Inverted Ink.
- **Dark Text Secondary** (`#9CA3AF`): Inverted Slate.
- **Lake Blue (dark)** (`#3B8FDF`): Brighter blue for OLED legibility; CTAs lift slightly.

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro` (Display / Text — automatic switching at 20pt)
- **Marketing / Web**: `Proxima Nova` (proprietary licensed face used on marketing)
- **Numbers**: Always set with `font-variant-numeric: tabular-nums` so 1's and 7's align in the diary stack
- **Fallback stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Inter', 'Helvetica Neue', Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Calorie Hero | SF Pro Display | 56pt | 700 (Bold) | 1.0 | -1.0pt | The giant "1,247" inside the calorie ring |
| Calorie Hero Label | SF Pro Text | 13pt | 600 | 1.0 | 0.6pt | "REMAINING" in uppercase under the number |
| Large Nav Title | SF Pro Display | 28pt | 700 | 1.15 | -0.4pt | "Diary", "Dashboard", "Plans" |
| Section Header | SF Pro Display | 22pt | 700 | 1.2 | -0.3pt | "Today's Meals", "Recent Foods" |
| Meal Title | SF Pro Text | 18pt | 600 | 1.2 | -0.2pt | "Breakfast", "Lunch", "Dinner", "Snacks" |
| Food Name | SF Pro Text | 16pt | 500 (Medium) | 1.3 | -0.1pt | "Greek Yogurt, Plain, Nonfat" |
| Food Subtitle | SF Pro Text | 13pt | 400 | 1.3 | 0pt | "1 cup, 8 oz · Chobani" |
| Diary Calorie | SF Pro Text | 16pt | 600 tnum | 1.0 | 0pt | "120" right-aligned on each row |
| Body | SF Pro Text | 15pt | 400 | 1.4 | 0pt | Article copy, plans descriptions |
| Body Small | SF Pro Text | 13pt | 400 | 1.4 | 0pt | Help text, footers |
| Macro Number | SF Pro Display | 22pt | 700 tnum | 1.0 | -0.3pt | "187g" Carbs total |
| Macro Label | SF Pro Text | 11pt | 600 | 1.0 | 0.8pt uppercase | "CARBS / FAT / PROTEIN" |
| Button (Primary) | SF Pro Text | 16pt | 600 | 1.0 | 0pt | "Add Food", "Log Exercise" |
| Tab Label | SF Pro Text | 10pt | 600 | 1.0 | 0.2pt | Bottom tab labels |
| Streak Badge | SF Pro Text | 12pt | 700 tnum | 1.0 | 0pt | "42 day streak" |

### Principles
- **Tabular numerals on every number** in a diary, ring, or chart context — 1's and 0's align vertically so totals "stack visually"
- **Weight ladder concentrated at 400 / 500 / 600 / 700**: SF Pro's regular for body, medium for food names, semibold for totals and meal titles, bold for nav and hero numbers
- **Tight tracking at display sizes** (-1.0pt on the 56pt calorie number), neutral at 16pt body
- **Uppercase labels with tracking** for short status words: "REMAINING", "CARBS / FAT / PROTEIN", "TODAY"
- **Ink over pure black**: body sits at `#1F2937`, not `#000000` — the app feels approachable, not surgical
- **Dynamic Type respected** on body and meal titles; fixed sizes on the calorie hero number, macro numbers, and tab labels

## 4. Component Stylings

### Buttons

**Primary CTA ("Add Food", "Log Exercise", "Complete Diary")**
- Background: `#0072CE` (Lake Blue)
- Text: `#FFFFFF`, SF Pro 16pt weight 600
- Padding: 14pt vertical, 28pt horizontal
- Corner radius: 12pt (rounded rectangle — never a full pill on the primary CTA)
- Height: 48pt minimum
- Pressed: background `#005FA8`, scale 0.98
- Haptic: `.impactOccurred(.light)` on tap; `.notificationOccurred(.success)` on "Complete Diary"

**Secondary Tinted Button ("Quick Add", "Copy Yesterday")**
- Background: `#E7F0FF` (Sky Blue tint)
- Text: `#0072CE`, SF Pro 16pt weight 600
- Corner radius: 12pt
- Same height/padding as primary

**Outline Button ("Cancel", filter applies)**
- Background: transparent
- Border: 1pt `#E5E7EB`
- Text: `#1F2937`, SF Pro 16pt weight 500
- Corner radius: 12pt

**FAB / Scan Barcode Button (Diary tab)**
- 56pt circle, centered in the Diary screen above the tab bar
- Background: `#0072CE` Lake Blue with a `rgba(0,93,170,0.35) 0 6px 16px` blue-tinted shadow
- Icon: `barcode.viewfinder` SF Symbol, 24pt, white
- Haptic: `.impactOccurred(.medium)` on tap → opens the full-screen scanner with a horizontal red laser line that pulses

**Plus Add-Food Row ("+ Add Food")**
- Inline row at the top of each meal section
- Text: Lake Blue `#0072CE`, SF Pro 15pt weight 600
- Leading 24pt SF Symbol `plus.circle.fill` in Lake Blue
- 56pt row height, full-width tap target, no background
- Pressed: background fades to `#F5F7FA` for 200ms

### Cards & Containers

**Calorie Ring (the hero of Dashboard / Home)**
- Diameter: 220pt on iPhone Pro/Max; 180pt on iPhone SE
- Stroke width: 18pt
- Background ring: `#E5E9F0` (Surface Gray 2)
- Filled ring: gradient depending on remaining state
  - Under goal: `#19C37D` (Under Goal Green)
  - On track (consumed 60–95% of goal): `#0072CE` (Lake Blue) — the most common state
  - Approaching (95–100%): `#F59E0B` (Approaching Amber)
  - Over: `#EF4444` (Over Limit Red)
- Center stack (top to bottom): "REMAINING" label 13pt 600 uppercase tracking 0.6pt → 56pt 700 tabular number → "calories" 13pt 500 in Slate
- Goal/Food/Exercise mini-rows below the ring: each shows a number + uppercase label (e.g., `2,000` over `GOAL`, `753` over `FOOD`, `0` over `EXERCISE`)
- Update animation: ring fills via stroke-dasharray transition over 600ms ease-out; the center number ticks via a quick number-flip when you add a food

**Macro Donut (Carbs / Fat / Protein breakdown)**
- Diameter: 96pt
- Stroke width: 10pt
- Slices: orange `#FF9F1C` (Carbs) → purple `#A463F2` (Fat) → green `#19C37D` (Protein), in that order, clockwise from 12 o'clock
- Center number: total carbs+fat+protein percentage left of goal in 22pt 700 tabular
- Legend rows below the donut: 8pt color square + label (`CARBS`) + value (`187g · 65%`) — three rows
- Hit target: tapping a slice highlights its legend row with a 1pt outline

**Food Row (in a meal section, Diary tab)**
- Height: 64pt minimum
- Background: `#FFFFFF` default, `#F5F7FA` on press
- Layout (leading to trailing): 8pt indent → food name (16pt 500 `#1F2937`, 1-line truncate) → 2pt gap → subtitle "1 cup · 8 oz · Chobani" (13pt 400 `#4B5563`, 1-line truncate) → flex spacer → calorie value (16pt 600 tabular `#1F2937`) → 16pt trailing inset → 12pt chevron `#9CA3AF`
- Divider: 0.5pt `#E5E7EB` between rows, inset 16pt from leading
- Swipe-left: reveals Delete (red `#EF4444` fill) and More (Slate `#4B5563`)

**Meal Section Header (Breakfast / Lunch / Dinner / Snacks)**
- Height: 48pt
- Background: `#FFFFFF`
- Layout: meal name 18pt 600 `#1F2937` on the left + total calories tabular 16pt 600 `#1F2937` on the right (e.g., "Lunch ... 487")
- 0.5pt `#E5E7EB` divider below
- Tap: expands/collapses the meal section with a 250ms ease-out height transition

**Dashboard Card (Steps, Weight, Water, Plans modules)**
- Background: `#FFFFFF`
- Border: 1pt `#E5E7EB`
- Corner radius: 16pt
- Padding: 16pt all sides
- Shadow: none on outer card, `rgba(0,0,0,0.04) 0 1px 3px` on hover/press for subtle lift
- Title row: 11pt 600 uppercase Slate label + trailing icon (e.g., `figure.walk` for Steps) sized 14pt
- Big number: SF Pro Display 28pt 700 tabular `#111827`
- Subtitle: 13pt 400 Slate (e.g., "8,432 of 10,000 steps")
- Progress bar at the bottom: 6pt tall, `#E5E9F0` track, Lake Blue `#0072CE` fill, 3pt corner radius

**Search Bar (Food database search)**
- Background: `#F5F7FA`
- Height: 44pt
- Corner radius: 12pt
- Leading `magnifyingglass` 16pt `#4B5563`
- Placeholder: "Search foods or scan a barcode" SF Pro 15pt 400 `#9CA3AF`
- Trailing: `barcode.viewfinder` 18pt Lake Blue tap target

**Barcode Scanner Camera Overlay**
- Full-screen black background with the camera feed centered
- Horizontal scan-line guide: 1pt red `#EF4444` line spanning the full width, pulsing opacity 0.4 → 1.0 every 1200ms
- Corner brackets: 4 white 3pt-thick L-brackets framing a 280pt × 180pt scan window
- Top: "Scan a barcode" 17pt 600 white centered with a back chevron leading
- Bottom: "Can't scan? Search manually →" Lake Blue text-button white background
- Haptic on capture: `.notificationOccurred(.success)`

### Navigation

**Large-Title Nav Bar**
- Height: variable — 96pt with large title, collapses to 44pt inline
- Background: `#FFFFFF`, becomes `.regularMaterial` blur when content scrolls under
- Title: SF Pro Display 28pt 700 `#1F2937`, left-aligned, 16pt leading inset
- Trailing: 24pt SF Symbol (often `bell` for notifications or `person.crop.circle`)
- Date row (Diary only): "Today, May 14" in 14pt 500 Slate with `chevron.down` for date picker

**Bottom Tab Bar**
- Height: 50pt + safe area
- Background: `#FFFFFF` with 0.5pt `#E5E7EB` top divider; `.regularMaterial` blur when content scrolls beneath
- Tabs (5): Dashboard, Diary, Plans, Community, More
- Icon size: 22pt, SF Symbols (`square.grid.2x2`, `book.closed`, `list.clipboard`, `person.3`, `ellipsis`)
- Active tint: `#0072CE` Lake Blue (icon + label)
- Inactive: `#9CA3AF` Mute
- Labels: SF Pro 10pt 600, always visible
- Tap haptic: `.selectionChanged()`

**Tab Bar FAB (the Diary Tab is special)**
- A 56pt circular Lake Blue button sits *centered above* the Diary tab icon
- Icon: `plus` in 22pt white
- Tap action: opens Add Food drawer (sheet from bottom with options Scan Barcode / Search Food / Quick Add / Recent / My Foods)

### Input Fields

**Quick Add Number Pad**
- Modal sheet from the bottom, 320pt height
- Big number display: 40pt 700 tabular `#111827` centered at top
- Number pad: 3×4 grid of 64pt buttons; tap target 80pt; background `#F5F7FA`, pressed `#E5E9F0`
- Bottom: cancel (text-link Slate) + Add (Lake Blue pill)

**Serving Size Stepper**
- "Number of Servings" row with a `−` button (32pt circle, `#F5F7FA` background, `#1F2937` glyph), tabular number `2.0` in 18pt 600, and a `+` button matching
- Tap and hold accelerates the increment (multiplies after 1s)

### Distinctive Components

**Weekly Weight Trend Card**
- 160pt tall card with a line chart of the last 7 weight entries
- X-axis: 7 mini dots, one per day; current day filled Lake Blue, others outlined
- Line: 2pt Lake Blue stroke with a subtle linear gradient fill underneath (Lake Blue → transparent, 12% opacity)
- Y-axis labels: top and bottom only, in 11pt 500 Mute, e.g., "162.4" and "159.8"
- Tap a dot: a tooltip pops up with date + weight in 13pt 600 over a 4pt-radius dark callout

**Streak Flame Badge**
- 32pt orange flame glyph (`flame.fill` SF Symbol filled `#FB923C`) + "42 day streak" 12pt 700 tabular
- Appears in the top-right of the Dashboard large nav; also at the top of Diary on a 7-day streak milestone
- On 7/30/100/365 day milestones, a small confetti burst plays once

**Plus Premium Upsell Banner**
- Full-width banner, 80pt tall, background gradient `#005DAA` → `#0072CE`
- Left: white "Premium" badge + "Ad-free diary, custom macros, deeper insights" in 14pt 500 white
- Right: "Try Free →" white pill, MFP Blue text
- Persists on Dashboard for non-Premium users; dismissable

**Logo Block (M square)**
- The MFP heritage mark is a rounded square `#005DAA` background with a white serif "M" in the center
- 32pt corner radius on the square at large sizes (96pt+); 8pt radius at app-icon scale (40pt)
- Used in About, splash, and the avatar fallback when no profile photo is set

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64, 80
- Standard horizontal margin: 16pt on Diary and Dashboard; 24pt on detail screens (Food Detail, Settings)
- Section vertical gap: 32pt between major sections on Dashboard; 24pt between meal sections on Diary

### Grid & Container
- Content width: full device width minus 16pt horizontal margins on Diary
- Dashboard cards: 2-col grid for Steps/Water/Weight modules (8pt gap); full-width cards for Plans/Insights
- Calorie ring: centered in a full-width container, vertical padding 32pt top and 24pt bottom

### Whitespace Philosophy
- **Numbers breathe**: the big calorie number gets 64pt of vertical space around it on Dashboard — the ring is the screen
- **Diary rows compress**: 64pt tall rows let you scan many foods quickly; this is intentional density
- **Sticky tab bar + FAB compress the viewport** — the bottom 110pt is always chrome on Diary; plan list heights accordingly

### Border Radius Scale
- Square (0pt): horizontal-divider hairlines only
- Small (4pt): badge pills, tooltip callouts
- Medium (8pt): chip backgrounds, alert banners
- Standard (12pt): primary CTAs, secondary tinted buttons, search bar, input fields
- Comfortable (16pt): dashboard cards, sheet containers
- Soft (24pt): bottom-sheet top corners
- Full Pill (500pt): streak badge, Premium "Try Free" pill
- Circle (50%): FAB, avatars, stepper buttons, ring stroke caps

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, diary rows, section backgrounds, canvas |
| Card (Level 1) | `rgba(0,0,0,0.04) 0 1px 3px` | Dashboard cards on press, food row press states |
| Subtle Lift (Level 2) | `rgba(0,0,0,0.06) 0 2px 8px` | Sticky bottom sheets, expanded macro donut |
| FAB (Level 3) | `rgba(0,93,170,0.35) 0 6px 16px` | Diary FAB (Lake Blue–tinted shadow, not neutral) |
| Sheet (Level 4) | `rgba(0,0,0,0.16) 0 -8px 24px` | Add-Food modal, Quick Add sheet, food detail |
| Blur Material | `.regularMaterial` over `#FFFFFF` at 92% | Nav bar on scroll, tab bar over content |

**Shadow Philosophy**: MyFitnessPal is a clinical, white app — shadows are almost imperceptible except on the Diary FAB, where the shadow is intentionally *tinted with the button's blue* to make the action feel like it's emanating light, not casting darkness. Everything else relies on hairline borders and divider strokes for separation.

### Motion
- **Calorie ring fill**: when you add a food, the ring's stroke-dashoffset transitions over 600ms ease-out and the center number ticks via a 300ms number-flip
- **Food row press**: background fades `#FFFFFF` → `#F5F7FA` in 100ms, no scale
- **FAB tap**: scale 1.0 → 0.92 → 1.0 over 250ms spring, paired with `.impactOccurred(.medium)`
- **Add Food sheet**: slides up from the bottom with a 350ms cubic-bezier curve, backdrop fades to `rgba(0,0,0,0.4)`
- **Barcode laser**: 1pt red line pulses opacity 0.4 → 1.0 → 0.4 every 1200ms; on capture, a green tick replaces the laser for 800ms
- **Streak confetti**: triggered only at 7/30/100/365 day milestones — paper confetti burst at the top of Dashboard, 1.4s total
- **Tab switch**: `.selectionChanged()` haptic, no animation

## 7. Do's and Don'ts

### Do
- Use Lake Blue `#0072CE` for the primary CTA, active tab tint, calorie ring fill at "on track", and the diary FAB
- Use MFP Blue `#005DAA` for heritage moments — the logomark, Premium upsell gradient end, large nav title accents
- Keep the macro donut trio in the exact order Carbs Orange → Fat Purple → Protein Green; never reorder, never recolor
- Set tabular numerals on every number that summarizes nutrition — calories, grams, percentages, weight, water ounces
- Use the ring color-flip semantically: green when under, blue when on-track, amber when approaching, red when over
- Use the FAB pattern on the Diary tab — a 56pt blue circle floating above the tab icon with a `plus` glyph
- Use `#1F2937` Ink for body text — pure black is too clinical for a wellness app
- Use SF Pro on iOS; substitute Proxima Nova only on marketing/web
- Keep dashboard cards on a 16pt corner radius with a 1pt `#E5E7EB` border and no shadow
- Place the streak flame badge in the Dashboard nav-bar trailing position — it's a small but motivating moment

### Don't
- Don't use the macro trio colors anywhere outside the macro context — orange should not appear on a button, purple should not appear on a tab
- Don't render the calorie ring without tabular numerals — non-aligned digits make the giant number look broken
- Don't use the Premium gold `#D4A437` as a primary CTA — it's a *gate*, not an action
- Don't add aggressive shadows — MFP is clinical and flat; soft 4% shadows only
- Don't break the meal section order (Breakfast, Lunch, Dinner, Snacks) — this is the cultural pattern users expect
- Don't use red `#EF4444` for primary actions; it's strictly "over your goal" semantic
- Don't skip the "+ Add Food" row inside each meal — it's the fastest entry path and a critical pattern
- Don't replace the M-square logomark color with Lake Blue — it stays heritage MFP Blue `#005DAA`
- Don't use a brighter blue than `#0072CE` on light mode — it loses contrast against white
- Don't compress the calorie ring smaller than 180pt — below that, the giant number stops being legible

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Calorie ring shrinks to 180pt diameter; dashboard cards 2-col with 8pt gap |
| iPhone 13/14/15 | 390pt | Standard 220pt ring; 16pt horizontal margins |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected; ring nudges down 6pt |
| iPhone 15/16 Pro Max | 430pt | 240pt ring possible on Pro Max; dashboard cards remain 2-col |
| iPad | 768pt+ | Dashboard becomes a 3-col layout; Diary keeps single column with wider rows |

### Dynamic Type
- Body, food names, meal titles: scale up to XXL
- **Fixed**: calorie hero number (56pt), macro donut numbers, tab labels, streak badge — layout would break if these scaled

### Orientation
- Portrait-locked on iPhone for Dashboard, Diary, Food Detail
- Charts and weight trend support landscape on iPhone Pro Max
- iPad supports both orientations natively

### Touch Targets
- FAB: 56pt circle, 44pt minimum tap target met easily
- Add-Food row: 56pt tall full-width hit area
- Food row chevron: 44pt hit slop around the 12pt glyph
- Tab bar items: 56pt row height, 44pt minimum width

### Safe Area Handling
- Top: large nav title starts at safe-area top + 16pt; Dynamic Island avoided by adding 12pt extra padding
- Bottom: tab bar honors home indicator; FAB sits 88pt from the bottom safe area on Diary

## 9. Quick Reference Cheat Sheet

### Color Quick Reference
- Canvas: `#FFFFFF`
- Surface Gray: `#F5F7FA`
- Divider: `#E5E7EB`
- Ink (primary text): `#1F2937`
- Slate (secondary text): `#4B5563`
- MFP Blue (heritage): `#005DAA`
- Lake Blue (primary action): `#0072CE`
- Lake Blue Pressed: `#005FA8`
- Sky Blue (tinted bg): `#E7F0FF`
- Carbs Orange: `#FF9F1C`
- Fat Purple: `#A463F2`
- Protein Green: `#19C37D`
- Under Goal Green: `#19C37D`
- Approaching Amber: `#F59E0B`
- Over Limit Red: `#EF4444`
- Streak Orange: `#FB923C`
- Premium Gold: `#D4A437`

### Example Component Prompts
- "Create a SwiftUI MyFitnessPal calorie ring: 220pt diameter, 18pt stroke, light gray `#E5E9F0` background ring, fill color Lake Blue `#0072CE`, stroke-dashoffset transitions over 600ms ease-out. Center stack: 'REMAINING' uppercase 13pt 600 with 0.6pt tracking, then '1,247' SF Pro Display 56pt 700 tabular `#111827`, then 'calories' 13pt 500 Slate. Below the ring, three columns showing Goal / Food / Exercise with a number above an uppercase label."
- "Build the macro donut for MyFitnessPal: 96pt diameter, 10pt stroke. Three slices in this exact clockwise order from 12 o'clock: Carbs `#FF9F1C` orange, Fat `#A463F2` purple, Protein `#19C37D` green. Center number is the percent of goal consumed in SF Pro Display 22pt 700 tabular. Below the donut, three legend rows: 8pt colored square + uppercase 11pt 600 label + value '187g · 65%' in 13pt 500."
- "Design the Diary food row: 64pt tall, white background, leading 16pt food name in SF Pro Text 16pt weight 500 `#1F2937` (1-line truncate), 13pt 400 `#4B5563` subtitle '1 cup · 8 oz · Chobani' below it. Trailing: tabular 16pt 600 calorie value `#1F2937` + 12pt chevron `#9CA3AF`. Press state: background `#F5F7FA`. Swipe-left reveals Delete `#EF4444` and More `#4B5563`."
- "Build the Diary FAB: 56pt circle, Lake Blue `#0072CE` fill, white `plus` SF Symbol at 22pt, blue-tinted shadow `rgba(0,93,170,0.35) 0 6px 16px`. Position: centered above the Diary tab icon, 88pt above the bottom safe area. Tap haptic: `.impactOccurred(.medium)`. On tap, slides up the Add Food sheet (350ms cubic-bezier) with options: Scan Barcode, Search Food, Quick Add, Recent, My Foods."
- "Create the barcode scanner overlay: full-screen black background with a centered camera feed. Four white 3pt L-brackets frame a 280pt × 180pt scan window. A 1pt red `#EF4444` horizontal scan line pulses opacity 0.4 → 1.0 → 0.4 every 1200ms. Top: white 'Scan a barcode' 17pt 600 + leading back chevron. Bottom: 'Can't scan? Search manually →' Lake Blue text-button. On capture: `.notificationOccurred(.success)` and a green tick replaces the laser for 800ms."

### Iteration Guide
1. Canvas is `#FFFFFF`; use Surface Gray `#F5F7FA` for diary rows on press, dashboard card fills, search bar background
2. Primary action is Lake Blue `#0072CE`; heritage MFP Blue `#005DAA` lives on the logomark and Premium gradient end
3. The calorie ring is the hero — 220pt diameter, 18pt stroke, color flips green → blue → amber → red semantically
4. Macro trio is locked: Carbs orange `#FF9F1C`, Fat purple `#A463F2`, Protein green `#19C37D` — clockwise from 12 o'clock, in that order, always
5. Tabular numerals on every number — calories, grams, percentages, weight
6. SF Pro at 400/500/600/700; Display variant kicks in at 20pt+
7. Dashboard cards use 1pt `#E5E7EB` border with no shadow; the FAB is the only meaningfully elevated element
8. Tab bar is 5 tabs (Dashboard, Diary, Plans, Community, More); active tint is Lake Blue
9. Dark mode is `#0F1419` canvas with `#F3F4F6` primary text; Lake Blue lifts to `#3B8FDF`
10. Don't repurpose the macro trio colors elsewhere — they belong only to the macro context
