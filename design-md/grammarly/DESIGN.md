# Design System Inspiration of Grammarly (iOS)

## 1. Visual Theme & Atmosphere

Grammarly's iOS app is a *calm writing surface wrapped around an opinionated assistant*. The canvas is pure white (`#FFFFFF`) in light mode and a warm charcoal (`#121212`) in dark — never stark, because the product's job is to make your own prose feel comfortable to read while a quiet green assistant works in the margins. Open the editor and you see a near-frameless document: a serene title in Inter 22-26pt bold, body copy at 16pt with generous 1.65 line-height, and — the signature moment — words and phrases marked with **colored underlines**. Each underline is a category: a red squiggle for Correctness, a blue highlight for Clarity, a green dotted line for Engagement, a purple line for Delivery. Tapping any marked span slides up a **suggestion card** from the bottom: a small colored category dot, a strikethrough-old → green-new word swap, a one-sentence plain-language explanation, and a single green "Accept" pill with a circular dismiss. This card is the heartbeat of the entire app.

The other two signature affordances are the **document score ring** — a thin circular gauge in the top-right showing an overall 0–100 number that fills Grammarly Green as the writing improves — and the **assistant bar** pinned to the bottom: a small green Grammarly orb (the rotated-G logomark), a running suggestion count ("14 suggestions · 3 Correctness · 6 Clarity · 5 Engagement"), and a "Review" CTA that walks the user card-by-card. Everything in the app routes through these three: the colored underline (where), the suggestion card (what), the assistant bar (how many left).

The color system is monobrand and disciplined. There is exactly **one** brand accent — Grammarly Green (`#15C39A`, deepening to `#11A683`) — and it carries every primary action, the score ring, and the orb. There is no secondary brand color. Functional color lives entirely in the four **suggestion categories**: Correctness Red (`#E5484D`), Clarity Blue (`#3B82F6`), Engagement Green (`#16A34A`), Delivery Purple (`#8B5CF6`). These four hues never act as CTAs — they only color underlines, category dots, and inline highlights. Premium upsell uses a warm gold (`#E0A82E`). The restraint is the point: the assistant is helpful, not loud.

Typography is a clean grotesque. Grammarly's product uses a humanist sans (Apercu in marketing, SF Pro / system on iOS); **Inter** is the closest free face and is used here. Hierarchy is editorial: screen title 32pt/800, document heading 22-26pt/700, card title 18pt/700, editor body 16pt/400 (the calm reading size), suggestion title 15pt/600, explanation 14pt/400, category tag 12pt/700 uppercase. Weight concentrates at 400/600/700/800 — regular for prose, semibold for suggestion titles, bold for headings, extrabold for screen titles. The type system is built to recede so the user's own words read cleanly.

**Key Characteristics:**
- Calm writing canvas — pure white (`#FFFFFF`) light / warm charcoal (`#121212`) dark, near-frameless editor
- Single brand accent — Grammarly Green (`#15C39A` / `#11A683`); no secondary brand color
- Color-coded suggestion underlines — red Correctness, blue Clarity, green Engagement, purple Delivery
- Suggestion card — bottom card with category dot, strikethrough → green swap, explanation, green Accept pill
- Document score ring — thin circular 0–100 gauge, top-right, fills Grammarly Green as writing improves
- Assistant bar — pinned bottom strip: green orb + running suggestion count + Review CTA
- Tone detector — emoji + tone pills ("Confident", "Formal") summarizing how the writing reads
- Pill-shaped buttons everywhere — fully rounded (999pt) primary, secondary, premium
- Inter / SF Pro type — editorial hierarchy, body at 16pt for calm reading
- Premium gold (`#E0A82E`) for upsell — the only non-green, non-functional accent
- Minimal chrome — back chevron, doc title, score ring up top; assistant bar at bottom; nothing else

## 2. Color Palette & Roles

### Primary (Interactive)
- **Grammarly Green** (`#15C39A`): The single brand accent — Accept buttons, score ring, assistant orb, active states, links.
- **Green Deep** (`#11A683`): Deeper green for emphasis, gradient end, large fills.
- **Green Pressed** (`#0E8A6D`): Pressed state of any green button.
- **Green Tint** (`#15C39A1F`): 12% green wash behind selected suggestions and the accepted-state flash.

### Suggestion Categories (Functional — underlines & dots only, NEVER CTAs)
- **Correctness Red** (`#E5484D`): Grammar, spelling, punctuation — red squiggle underline + red dot.
- **Clarity Blue** (`#3B82F6`): Wordiness, hard-to-read sentences — blue highlight + blue dot.
- **Engagement Green** (`#16A34A`): Bland, repetitive language — green dotted underline + green dot.
- **Delivery Purple** (`#8B5CF6`): Tone, formality, confidence — purple underline + purple dot.

### Premium / Upsell
- **Premium Gold** (`#E0A82E`): "Try Premium", premium-only suggestion lock, upgrade banners.
- **Premium Gold Pressed** (`#C8941F`): Pressed state.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary editor canvas.
- **Surface 1** (`#F7F8F8`): Suggestion cards, settings rows, elevated panels.
- **Surface 2** (`#EEF0F0`): Pressed rows, tone pills, secondary chips.
- **Divider** (`#E4E6E6`): Hairline separators.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#121212`): Primary dark editor — warm charcoal, NOT pure black.
- **Dark Surface 1** (`#1C1C1E`): Suggestion cards, settings rows.
- **Dark Surface 2** (`#262629`): Pressed rows, tone pills.
- **Dark Divider** (`#2C2C2E`): Hairline separators.

### Text
- **Text Primary Light** (`#1A1A1A`): Editor body, headings — near-black, slightly warm.
- **Text Secondary Light** (`#6B6B70`): Explanations, metadata, tone labels.
- **Text Tertiary Light** (`#9A9A9F`): Placeholders, disabled.
- **Text Primary Dark** (`#E4E4E4`): Editor body on dark.
- **Text Secondary Dark** (`#9A9A9F`): Secondary text on dark (shared with light tertiary).
- **Text Tertiary Dark** (`#6A6A6E`): Disabled on dark.

### Underline / Highlight Roles

| Role | Light Mode | Dark Mode | Treatment |
|------|-----------|-----------|-----------|
| Correctness | `#E5484D` | `#F05A5F` | 2pt solid squiggle underline |
| Clarity | `#3B82F6` | `#5A95F8` | 18% fill highlight + 2pt underline |
| Engagement | `#16A34A` | `#22B85A` | 2pt dotted underline |
| Delivery | `#8B5CF6` | `#A07CF8` | 2pt solid underline |
| Accepted flash | `#15C39A1F` | `#15C39A24` | 12% green wash, 600ms fade |

### Semantic
- **Error Red** (`#E5484D`): Sync failure toast, destructive confirm (shares Correctness hue).
- **Success Green** (`#15C39A`): "All clear" state, save-success toast (uses brand green).
- **Warning Gold** (`#E0A82E`): Premium-locked notice, plagiarism flag.
- **Info Blue** (`#3B82F6`): Informational tip banners (shares Clarity hue).

## 3. Typography Rules

### Font Family
- **Product**: `Apercu` (Colophon Foundry — Grammarly's brand grotesque) in marketing; `SF Pro Text` / `SF Pro Display` on iOS via the system stack.
- **Closest Free Face**: `Inter` (by Rasmus Andersson, 2020, SIL OFL) — humanist grotesque with near-identical proportions, excellent UI hinting.
- **Mono (rare)**: `SF Mono` for in-app code samples and the plagiarism source viewer.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Inter | 32pt | 800 | 1.15 | -0.6pt | "Assistant", "Insights" top-level |
| Document Heading | Inter | 26pt | 700 | 1.2 | -0.4pt | Title inside the editor (H1) |
| Document Subhead | Inter | 22pt | 700 | 1.25 | -0.3pt | H2 inside editor / section title |
| Card Title | Inter | 18pt | 700 | 1.3 | -0.1pt | Suggestion category headline |
| Editor Body | Inter | 16pt | 400 | 1.65 | 0pt | The prose being edited — calm reading size |
| Suggestion Title | Inter | 15pt | 600 | 1.35 | 0pt | "Consider a more concise phrase" |
| Suggestion Swap | Inter | 16pt | 600 | 1.4 | 0pt | Old (strikethrough) → New (green) word |
| Explanation | Inter | 14pt | 400 | 1.5 | 0pt | Plain-language rule rationale |
| Card Meta / Action | Inter | 13pt | 600 | 1.0 | 0.1pt | "Accept", "Dismiss", count labels |
| Category Tag | Inter | 12pt | 700 | 1.0 | 0.4pt | "CORRECTNESS" uppercase |
| Tone Pill | Inter | 12pt | 600 | 1.0 | 0.2pt | "Confident", "Formal" |
| Score Number | Inter | 17pt | 700 | 1.0 | 0pt | Number inside the score ring |
| Tab / Bar Label | Inter | 11pt | 600 | 1.0 | 0.1pt | Assistant-bar subtitle |
| Button | Inter | 16pt | 700 | 1.0 | 0pt | Primary pill label |

### Principles
- **Editorial first**: Sizes follow document logic (title → heading → body), so the editor reads like a clean word processor, not a dashboard.
- **Calm body**: Editor body is 16pt/400 with a roomy 1.65 line-height — the prose must feel pleasant to read, not cramped.
- **Weight discipline**: 400 (prose), 600 (suggestion titles/actions), 700 (headings/categories), 800 (screen titles). No thin, no black, no light.
- **Categories are uppercase 12pt/700**: the four category tags are the only consistently uppercased text — a fixed, scannable label.
- **Dynamic Type first-class**: Document heading, body, suggestion title, explanation all scale; category tags, tone pills, score number, bar labels stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (Accept / Continue / Save)**
- Shape: Fully rounded pill, 999pt corner radius
- Background: `#15C39A` (Grammarly Green) / same on dark
- Text: `#06281F` (deep green-black for contrast on green) — Inter 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal (Accept inside card stretches full width, 10pt vertical)
- Pressed: background `#0E8A6D` + scale 0.98
- Disabled: background `#15C39A` at 40% opacity

**Secondary / Ghost Button (Dismiss / Skip)**
- Background: transparent
- Text: `#15C39A` light / `#15C39A` dark
- Border: 1.5pt `#15C39A`
- Corner radius: 999pt (pill)
- Padding: 12pt vertical, 22pt horizontal
- Font: Inter 15pt weight 600
- Pressed: background `#15C39A14` (8% green)

**Premium Button (Try Premium / Upgrade)**
- Background: `#E0A82E` (Premium Gold)
- Text: `#2A1E04` Inter 14pt weight 700
- Corner radius: 999pt
- Padding: 11pt vertical, 20pt horizontal
- Pressed: background `#C8941F` + scale 0.98

**Dismiss Icon Button (× on suggestion card)**
- Size: 44pt × 40pt pill, 1pt `#E4E6E6` / `#2C2C2E` border
- Glyph: 16pt × in `#6B6B70` / `#9A9A9F`
- Pressed: background `#EEF0F0` / `#262629`

**Text Button (See all suggestions / Learn more)**
- Font: Inter 14pt weight 600
- Color: `#15C39A`
- No underline; 12pt vertical hit padding
- Pressed: 60% opacity

### Suggestion Card (the core atom)

The single most important component. Slides up from the bottom when a marked span is tapped.

- Container: `#F7F8F8` light / `#1C1C1E` dark, 16pt corner radius, 1pt `#E4E6E6` / `#2C2C2E` border, shadow `0 8px 24px rgba(0,0,0,0.12)` light / `rgba(0,0,0,0.5)` dark
- Padding: 16pt all
- **Header row**: 10pt category dot (category color) + category tag (Inter 12pt 700 uppercase, `#6B6B70`)
- **Swap line**: old word in category color with line-through, "→" in `#9A9A9F`, new word in `#15C39A` weight 600 (Inter 16pt)
- **Explanation**: Inter 14pt 400 `#6B6B70`, 1.5 line-height, max 2 lines
- **Actions row**: full-width green "Accept" pill (Inter 14pt 700, `#06281F` on `#15C39A`) + 44pt dismiss icon button at 10pt gap
- Premium-locked variant: Accept replaced by gold "Unlock with Premium" + lock glyph

### Document Score Ring

- Diameter: 38pt (top bar) / 60pt (insights screen)
- Track: 3pt `#E4E6E6` / `#2C2C2E`
- Progress arc: 3pt `#15C39A`, sweeps from 12 o'clock clockwise, length = score/100
- Center: score number Inter 13-17pt 700, color = `#15C39A` (≥90), `#E0A82E` (60–89), `#E5484D` (<60)
- Animation: arc fills 0 → target over 600ms ease-out when document opens or a suggestion is accepted

### Colored Underlines (inline marking)

- **Correctness**: 2pt solid wavy underline `#E5484D` under the flagged span
- **Clarity**: 18% `#3B82F6` background fill + 2pt solid `#3B82F6` underline, 2pt corner radius
- **Engagement**: 2pt dotted underline `#16A34A`
- **Delivery**: 2pt solid underline `#8B5CF6`
- Tapping a span: span scrolls to vertical center, suggestion card animates up, span gets a faint category-tint background while card is open

### Tone Detector

- A horizontal row above/below the editor: leading emoji (😊 / 🙂 / 😐 / 😟 reflecting detected sentiment) 18pt, "Tone sounds" label Inter 13pt `#6B6B70`, then 1–3 tone pills
- Tone pill: `#EEF0F0` / `#262629` background, 1pt divider border, Inter 12pt 600 `#1A1A1A` / `#E4E4E4`, 999pt radius, 5pt × 12pt padding

### Assistant Bar (pinned bottom)

- Height: 64pt + safe area, background `rgba(255,255,255,0.96)` / `rgba(18,18,18,0.96)` with saturate-blur, 0.5pt top border
- Leading: 36pt green orb (circle `#15C39A`) containing the white rotated-G logomark 20pt
- Center: count title Inter 15pt 700 ("14 suggestions"), subtitle Inter 11pt 600 `#6B6B70` ("3 Correctness · 6 Clarity · 5 Engagement")
- Trailing: "Review" CTA — small green pill, Inter 13pt 700 `#06281F`
- When zero suggestions: orb stays green, title becomes "All clear" with a check, no Review CTA

### Navigation

**Top Bar (Editor)**
- Height: 44pt + safe area
- Leading: back chevron `chevron.left` 22pt `#1A1A1A` / `#E4E4E4`
- Center: document title Inter 15pt 600, truncating
- Trailing: 38pt score ring

**Bottom Tab Bar (App shell — Documents / Assistant / Account)**
- Height: 56pt + safe area, `#FFFFFF` / `#121212`, 0.5pt top border
- 3 tabs: Documents (`doc.text`), Assistant (the green orb, center, slightly enlarged), Account (`person.circle`)
- Active color: `#15C39A`; inactive `#9A9A9F`
- Labels: Inter 11pt 600, always shown; no tint pill — icon + label color change only

### Input Fields

**New Document / Title Field**
- Height: 48pt, background `#F7F8F8` / `#1C1C1E`, no border, 12pt corner radius
- Placeholder: "Untitled document" Inter 16pt `#9A9A9F`
- Focus: 1.5pt `#15C39A` border

**Search (Documents list)**
- Height: 40pt, `#EEF0F0` / `#262629`, 999pt radius
- Leading `magnifyingglass` 16pt `#9A9A9F`, placeholder "Search documents" Inter 14pt `#9A9A9F`

### Distinctive Components

**Suggestion Card** — see core atom above; the defining component.

**Document Score Ring** — thin animated 0–100 gauge; the at-a-glance health metric.

**Colored Category Underlines** — the inline marking language; four hues, four treatments.

**Tone Detector Row** — emoji + tone pills; Grammarly's signature "how does this sound" read.

**Assistant Orb** — the rotated-G logomark in a green circle; appears in the bar, as the center tab, and as the floating keyboard-extension button.

**Weekly Insights Card** — a stat card on the Account tab: large score ring + "You were more productive than 88% of Grammarly users", productivity / mastery / vocabulary mini-rings.

**Custom Keyboard Strip** — in the Grammarly Keyboard extension: a thin strip above the keys showing the green orb + the next suggestion inline with a tap-to-accept chip.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Editor horizontal inset: 20pt
- Card internal padding: 16pt
- Block / paragraph gap in editor: 16pt
- Suggestion card → assistant bar gap: 0 (card sits directly above bar)

### Grid & Container
- iPhone: editor at 20pt horizontal insets, full-width suggestion card minus 16pt insets
- iPad: editor max-width 680pt centered, 24pt insets; suggestion card can dock to a right rail (360pt) instead of sliding from bottom
- Documents list: single-column cards, 16pt insets, 12pt gap

### Whitespace Philosophy
- **The prose breathes**: 16pt body line-height becomes 1.65 — reading comfort outranks density
- **One thing at a time**: only one suggestion card is visible at once; the assistant walks you through serially
- **Chrome recedes**: top bar and assistant bar are the only persistent UI; everything else is the user's document
- **Cards float, prose is flat**: suggestion cards get a soft shadow; the editor surface itself has no borders or cards

### Border Radius Scale
- Square (0pt): underlines, full-bleed dividers
- Subtle (4pt): inline Clarity highlight, small chips
- Standard (8pt): list rows, banners
- Comfortable (12pt): input fields, settings cards
- Card (16pt): suggestion card, insights card
- Large (20pt): bottom sheets, modals
- Pill (999pt): all buttons, tone pills, score-count chip
- Circle (50%): assistant orb, score ring, avatars

## 6. Depth & Elevation

Grammarly is mostly flat — the editor has zero elevation so the prose reads like paper. Shadow is reserved for *the assistant speaking*.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Editor surface, document text, list rows |
| Card (Level 1) | `rgba(0,0,0,0.12) 0 8px 24px` light / `rgba(0,0,0,0.5) 0 8px 24px` dark | Suggestion card, insights card |
| Bar (Level 2) | `rgba(0,0,0,0.10) 0 -2px 12px` + saturate-blur | Assistant bar, top bar on scroll |
| Sheet (Level 3) | `rgba(0,0,0,0.16) 0 -8px 32px` | Bottom sheets (share, settings, premium paywall) |
| Modal Scrim | `rgba(0,0,0,0.4)` | Dim behind paywall and confirm modals |

**Shadow Philosophy**: The suggestion card's shadow says "this is the assistant talking — it floats above your document and you can act on it or dismiss it." The editor itself never lifts. On dark mode, shadows are deepened to `0.5` opacity and floating surfaces add a 1pt `#2C2C2E` border as a secondary elevation cue, since shadow is hard to read on charcoal.

### Motion
- **Suggestion card present**: slides up from bottom edge + fades in over 280ms ease-out (spring response 0.4, damping 0.85); the tapped span scrolls to vertical center in parallel
- **Accept**: card fades + slides down 220ms ease-in; the flagged span animates old→new text with a 200ms crossfade; a 12% green wash flashes over the corrected span and fades over 600ms; soft success haptic
- **Dismiss**: card slides down 200ms ease-in; underline removed instantly
- **Score ring fill**: arc sweeps 0 → target over 600ms ease-out on document open; on accept, arc animates from old → new value over 400ms with a soft tick haptic at completion
- **Tone pills**: fade + 4pt rise in 200ms when tone is recomputed (debounced 800ms after typing stops)
- **Assistant bar count**: number rolls (odometer) over 250ms when suggestion count changes
- **Tab switch**: cross-dissolve 200ms; the center assistant orb has a gentle 1.0→1.06→1.0 pulse when new suggestions arrive
- **Haptic feedback**: soft success on Accept, light tick on score-ring completion, light impact on card present

## 7. Do's and Don'ts

### Do
- Use pure white (`#FFFFFF`) light / warm charcoal (`#121212`) dark as the editor canvas — NOT off-white, NOT pure black
- Use Grammarly Green (`#15C39A` → `#11A683`) as the ONE brand accent — CTAs, score ring, orb
- Color suggestion underlines by category — red Correctness, blue Clarity, green Engagement, purple Delivery
- Slide the suggestion card up from the bottom with a soft shadow — it is the assistant speaking
- Show one suggestion card at a time and walk the user serially via the assistant bar
- Keep editor body at 16pt/400 with 1.65 line-height — reading comfort first
- Make all buttons fully rounded pills (999pt)
- Animate the score ring arc filling on open and on accept (600ms / 400ms ease-out)
- Use the tone detector (emoji + tone pills) to summarize how the writing reads
- Flash a faint green wash over a span after Accept, then fade it (600ms)
- Use Premium Gold (`#E0A82E`) only for upsell — never as a functional or primary color

### Don't
- Don't use the four category colors (red/blue/green/purple) as buttons or CTAs — they only mark text
- Don't introduce a secondary brand color — Grammarly is monobrand green
- Don't put a card or border around the editor prose — the document is flat, only suggestion cards float
- Don't show multiple suggestion cards stacked — one at a time, serially
- Don't use pure black (`#000000`) for body text — warm near-black `#1A1A1A` light / `#E4E4E4` dark
- Don't cram the editor with a tight line-height — 1.65 is the calm reading rhythm
- Don't use rectangular (sharp-corner) buttons — every button is a pill
- Don't make the score ring jump instantly — it sweeps over 600ms
- Don't tint the active tab with a pill background — Notion-style color-only change (icon + label go green)
- Don't animate aggressively — motion is 200–600ms ease-out, calm and reassuring
- Don't surface the full card on a plain tap of unmarked text — cards appear only when a marked span is tapped or via the Review walkthrough

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Editor 16pt insets; suggestion card full-width; tone row wraps to 2 pills max |
| iPhone 13/14/15 | 390pt | Standard layout, 20pt editor insets |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top bar |
| iPhone 15/16 Pro Max | 430pt | Larger editor measure; tone row shows up to 3 pills |
| iPad (portrait) | 768pt | Editor max 680pt centered; suggestion card docks to bottom OR right rail (user setting) |
| iPad (landscape) | 1024pt+ | 2-pane: documents list (320pt) + editor; suggestion card as 360pt right rail |

### Dynamic Type
- Document heading, editor body, suggestion title, explanation: full scale
- Category tags, tone pills, score number, assistant-bar labels, tab labels: FIXED (layout-sensitive)
- Code samples: scale to XL but stay SF Mono

### Orientation
- All screens rotate; iPad landscape promotes the documents list into a persistent sidebar
- Suggestion card relocates from bottom sheet (portrait) to right rail (landscape / iPad) so it never covers the prose being corrected

### Touch Targets
- Accept pill: full card width, 40pt tall
- Dismiss icon button: 44pt × 40pt
- Score ring (tap to open insights): 38pt glyph, 44pt hit
- Tab bar icons: 24pt glyph, 56pt hit
- Marked span tap: span height + 8pt vertical hit padding

### Safe Area Handling
- Top: top bar respects safe area and Dynamic Island
- Bottom: assistant bar + home indicator respected; suggestion card sits directly above the assistant bar, both above the home indicator
- Keyboard: editor scrolls above the keyboard; the suggestion card and assistant bar lift to sit above the keyboard when it is open
- Sides: 20pt editor inset (16pt on SE)

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#121212`
- Surface 1: `#F7F8F8` light / `#1C1C1E` dark
- Divider: `#E4E6E6` light / `#2C2C2E` dark
- Text primary: `#1A1A1A` light / `#E4E4E4` dark
- Text secondary: `#6B6B70` light / `#9A9A9F` dark
- Brand green (single accent): `#15C39A` → deep `#11A683` → pressed `#0E8A6D`
- Correctness underline: `#E5484D`
- Clarity underline/highlight: `#3B82F6`
- Engagement underline: `#16A34A`
- Delivery underline: `#8B5CF6`
- Premium gold: `#E0A82E`
- Accept-flash wash: `#15C39A1F`

### Example Component Prompts
- "Build the Grammarly suggestion card in SwiftUI: a bottom-anchored card, `#F7F8F8` light / `#1C1C1E` dark, 16pt corner radius, 1pt divider border, shadow `rgba(0,0,0,0.12) 0 8px 24px`. Header row: 10pt category dot (Correctness `#E5484D`) + 'CORRECTNESS' in Inter 12pt w700 uppercase `#6B6B70`. Swap line: old word `#E5484D` with strikethrough, '→' `#9A9A9F`, new word `#15C39A` w600, Inter 16pt. Explanation: Inter 14pt w400 `#6B6B70`, max 2 lines. Actions: full-width green Accept pill (Inter 14pt w700, `#06281F` on `#15C39A`, 999pt radius) + 44×40 dismiss icon button. Card slides up + fades over 280ms spring, soft haptic."

- "Create the Grammarly document score ring: 38pt circle, 3pt track `#E4E6E6`, 3pt progress arc `#15C39A` sweeping clockwise from 12 o'clock, arc length = score/100. Center number Inter 13pt w700, color `#15C39A` if ≥90 else `#E0A82E` if ≥60 else `#E5484D`. On appear, animate arc 0 → score over 600ms ease-out; on accept, animate old → new value over 400ms with a light tick haptic."

- "Render Grammarly colored underlines on a text run: Correctness = 2pt solid wavy `#E5484D` underline; Clarity = 18% `#3B82F6` background fill + 2pt solid `#3B82F6` underline, 2pt corner radius; Engagement = 2pt dotted `#16A34A`; Delivery = 2pt solid `#8B5CF6`. On tap, scroll the span to vertical center, give it a faint category-tint background, and present the suggestion card from the bottom."

- "Build the Grammarly assistant bar: 64pt + safe area, `rgba(18,18,18,0.96)` dark with saturate blur, 0.5pt top border. Leading 36pt green circle `#15C39A` with white rotated-G logomark 20pt. Center: 'X suggestions' Inter 15pt w700 + subtitle '3 Correctness · 6 Clarity · 5 Engagement' Inter 11pt w600 `#9A9A9F`. Trailing small green 'Review' pill Inter 13pt w700 `#06281F`. When count is 0: title 'All clear' + check, no Review CTA. Count rolls odometer-style over 250ms on change."

- "Create the Grammarly tone detector row: leading emoji 18pt (😊/🙂/😐/😟 by sentiment), 'Tone sounds' Inter 13pt `#6B6B70`, then 1–3 tone pills. Tone pill: `#262629` bg, 1pt `#2C2C2E` border, Inter 12pt w600 `#E4E4E4`, 999pt radius, 5×12 padding. Recompute debounced 800ms after typing stops; pills fade + 4pt rise over 200ms."

- "Build the Grammarly accept interaction: on tap Accept, fade+slide the card down 220ms ease-in, crossfade the flagged span's old→new text over 200ms, flash a 12% `#15C39A` wash over the corrected span fading over 600ms, decrement the assistant-bar count with an odometer roll, advance the score ring to its new value over 400ms, fire a soft success haptic."

### Iteration Guide
1. Canvas is pure white light / warm charcoal `#121212` dark — NOT pure black, NOT off-white
2. There is exactly ONE brand accent: Grammarly Green `#15C39A` (deep `#11A683`) — every CTA, the ring, the orb
3. The four category colors (red/blue/green/purple) mark text ONLY — never buttons, never CTAs
4. The suggestion card slides up from the bottom with a soft shadow — it is the assistant speaking
5. Show ONE suggestion card at a time; the assistant bar walks the user through serially
6. Editor body is 16pt/400 with 1.65 line-height — reading comfort beats density
7. Every button is a fully rounded pill (999pt)
8. The score ring is a thin animated 0–100 arc that fills green on open and on accept
9. Premium Gold `#E0A82E` is upsell-only — never functional, never primary
10. Motion is calm: 200–600ms ease-out, soft haptics, an odometer count, a green accept-flash that fades
