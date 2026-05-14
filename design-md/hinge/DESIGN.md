# Design System Inspiration of Hinge (iOS)

## 1. Visual Theme & Atmosphere

Hinge's iOS app reads like a personal journal printed on warm uncoated paper. The canvas is a soft off-white — almost a cream — that intentionally rejects the cold pure-white of most tech apps, because Hinge's premise is intimate: this is the app "designed to be deleted", and the surface is supposed to feel quiet, considered, careful. Where Tinder's swipe stack is a slot machine of full-bleed faces, Hinge's profile is a vertical scroll — long, novel-like, prompts interleaved with photos — and you respond by tapping a specific paragraph or picture and leaving a comment. The interaction model is the design: Hinge is a conversation, not a verdict.

The accent that does almost all the work is Hinge Black (`#1A1A1A`) — a warm, slightly desaturated near-black used for primary type and the iconic "H" mark. The signature pop is a single gold-orange that Hinge uses for one thing only: the Standouts star, the Roses currency, and certain premium moments. Everything else is greyscale on cream. The restraint is the brand. When the entire app is paper-textured neutrals and a profile suddenly blooms with a single Rose icon, the gesture lands.

Typography is dominated by Sailec — the proprietary face Hinge uses for prompts and personal-attribute text. Headlines and the iconic prompts ("Two truths and a lie", "My simple pleasures") are set in display weights at 28-32pt; prompts answers are oversized at 22-24pt — a tweet-sized intimate statement, sized like a magazine pull-quote, given more weight than the photos themselves. Below the answers, attribute chips (height, job, location, education) use a smaller geometric sans at 13-14pt, sitting on subtle pill backgrounds. The voice the type carries is editorial-personal: a profile reads like an excerpt from someone's diary.

**Key Characteristics:**
- Cream canvas (`#FDF8F2`) and Paper White (`#FAF6F0`) — warm off-whites that never read as cold
- Hinge Black (`#1A1A1A`) for type and the iconic "H" mark — a warm near-black, not pure
- Single gold accent (`#E8A04D`) reserved for Standouts star, Roses currency, premium edges
- Vertical scrolling profiles with prompt cards and photos interleaved — never a card stack
- Comment-on-photo and Like-with-a-comment patterns — tap any prompt or photo to react
- Heart button (filled black `#1A1A1A`) under every prompt and photo — the most pressed button in the app
- Rose icon (filled gold `#E8A04D`) — premium send, reserved for "this is the one"
- Bottom tab: Discover / Likes You / Standouts / Matches / Profile — 5 tabs, labeled, black-active
- Sailec for display + prompts; Inter or system fallback for chrome and attributes
- Subtle, paper-soft shadows (`rgba(0,0,0,0.06)`) and 12-16pt corner radii on all cards

## 2. Color Palette & Roles

### Primary
- **Hinge Black** (`#1A1A1A`): The brand color — type, "H" mark, primary CTAs, filled heart, active tab, vote check.
- **Black Pressed** (`#0A0A0A`): Pressed-state of Hinge Black on CTAs.
- **Hinge Cream** (`#FDF8F2`): The signature canvas — every screen background by default.

### Brand Accents
- **Rose Gold** (`#E8A04D`): The single accent — Standouts star, Roses currency icon, premium "send a Rose" CTA, gold ring on hero standout cards.
- **Rose Gold Deep** (`#C57E2E`): Pressed state on Rose CTAs, gold sparkle on the Roses sheet.
- **Rose Gold Light** (`#F5D9A8`): Soft fill behind the Standouts module, gold halo on the Rose icon at large sizes.

### Surfaces & Dividers
- **Paper White** (`#FAF6F0`): Card fill — prompt cards, photo frames, sheet backgrounds.
- **Surface Sand** (`#F2EBE0`): Attribute chip fills, input field backgrounds, sticky note feel.
- **Surface Sand 2** (`#E8DFD0`): Pressed states on chips, hairline-thicker dividers.
- **Divider Bone** (`#E0D6C5`): 0.5pt row dividers on settings and forms.
- **Hinge Shadow** (`rgba(28, 20, 10, 0.08)`): Card shadow — warm-tinted, never blue-grey.

### Text
- **Hinge Black** (`#1A1A1A`): Primary text — prompt questions, prompt answers, names.
- **Hinge Graphite** (`#4A4239`): Secondary text — attribute labels, dates, "x liked your prompt" metadata. Warm-tinted, not neutral grey.
- **Hinge Stone** (`#7A7268`): Tertiary text — placeholder, helper, "Send a Like" hint.
- **Hinge Bone** (`#B0A89C`): Disabled labels, very-secondary metadata.

### Semantic
- **Match Green** (`#2D7A4B`): "It's a match" confirmation banner, profile "verified" checkmark.
- **Warning Amber** (`#D88B2E`): Profile incomplete warnings, photo review pending.
- **Error Red** (`#B33A2F`): Form errors, decline confirmation on Likes You.
- **Info Slate** (`#5A6273`): Rarely used — Hinge Labs info banners, external links.

### Dark Mode
Hinge added dark mode in 2022. It is warm-dark — preserving the paper feel, never going pure black.
- **Dark Canvas** (`#16130E`): Warm near-black canvas — the cream-paper-at-night feel.
- **Dark Surface 1** (`#1E1A14`): Cards and prompt boxes.
- **Dark Surface 2** (`#2A2520`): Chip fills and pressed states.
- **Dark Divider** (`#2F2A22`): Hairlines.
- **Dark Text Primary** (`#EFE8DA`): Inverted Hinge Black, slightly warm.
- **Dark Text Secondary** (`#A89E8E`): Inverted Hinge Graphite.
- **Rose Gold (dark)** (`#F0B05C`): Brightened gold for OLED — the Standouts star and Rose icon get +8% luminance on dark canvas.

## 3. Typography Rules

### Font Family
- **Primary (display, prompts)**: `Sailec` — geometric humanist sans by Type Dynamic, used across Hinge marketing and in-app
- **Weights available**: Light (300), Regular (400), Medium (500), Bold (700), Black (900)
- **Secondary (UI chrome, body, chips)**: `Inter` or system — paired for compact UI sizes 12-15pt
- **Fallback stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Helvetica Neue', sans-serif` — SF Pro is the warmer system fallback
- **Web/marketing Google Fonts substitute**: `Manrope` or `DM Sans` — both capture Sailec's mix of warmth and geometric clarity

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display Hero | Sailec | 36pt | 700 (Bold) | 1.1 | -0.6pt | "It's a match!" celebration, name on profile detail |
| Prompt Question | Sailec | 22pt | 500 (Medium) | 1.25 | -0.2pt | "Two truths and a lie", italicized in some templates |
| Prompt Answer | Sailec | 24pt | 700 (Bold) | 1.25 | -0.3pt | The user's response — the most prominent text on profile |
| Name + Age | Sailec | 28pt | 700 | 1.15 | -0.4pt | "Sigrún, 28" at top of profile |
| Section Header | Sailec | 18pt | 700 | 1.2 | -0.1pt | "Vitals", "My Vices", on profile detail |
| Card Body | Inter | 16pt | 400 (Regular) | 1.4 | 0pt | Prompt context, settings descriptions |
| Card Body Bold | Inter | 16pt | 600 (Semi) | 1.4 | 0pt | Emphasis within paragraphs |
| Attribute Chip | Inter | 14pt | 500 (Medium) | 1.3 | 0pt | "5'9", "Engineer", "San Francisco" |
| Metadata | Inter | 13pt | 400 | 1.3 | 0pt | "Liked your prompt 2h ago", date stamps |
| Button (Primary) | Sailec | 16pt | 700 | 1.0 | 0pt | "Send Like", "Send Rose", "Match" |
| Tab Label | Sailec | 10pt | 500 | 1.0 | 0.2pt | Bottom tab bar — all caps not used |
| Comment Input | Inter | 16pt | 400 | 1.35 | 0pt | "Add a comment…" placeholder |
| Helper / Caption | Inter | 12pt | 400 | 1.3 | 0pt | "Visible to people who liked you", fine print |
| Match Banner | Sailec | 14pt | 700 | 1.0 | 0.2pt | "You matched with Sigrún" in success green |

### Principles
- **Prompts get magazine-pull-quote weight**: Answers at 24pt Bold are intentionally larger than supporting photos in visual weight — Hinge wants you to read the words, not skim the face.
- **Sailec carries voice, Inter carries chrome**: Display, prompts, and CTAs in Sailec; everything tabular or compact in Inter.
- **Warm Hinge Black, never pure**: Body text sits at `#1A1A1A`, not `#000000` — alongside the cream canvas, this is what makes the surface feel like printed paper.
- **Italic prompts on certain templates**: Prompt questions like "I geek out on _" sometimes use Sailec Italic for the answer-blank line — a typographic wink that makes them feel handwritten.
- **Dynamic Type respected on prompt content, name, body**: Attribute chips and tab labels are fixed (layout-sensitive); profile content scales generously.
- **Tracking is editorial — tight at display, neutral at body**: -0.6pt at 36pt falling to 0pt at body, no positive tracking anywhere except caps-set tab labels at +0.2pt.

## 4. Component Stylings

### Buttons

**Primary CTA (Send Like / Send Rose / Match / Continue)**
- Background: `#1A1A1A`
- Text: `#FAF6F0` (Paper White, not pure white — warm contrast)
- Padding: 16pt vertical, 32pt horizontal
- Corner radius: 28pt (capsule — fully rounded, height ≈ 56pt)
- Height: 56pt fixed
- Pressed: background `#0A0A0A`, scale 0.97
- Haptic: `.impactOccurred(.medium)` on Send Like; `.notificationOccurred(.success)` on match

**Rose CTA (Send a Rose — premium)**
- Background: `#1A1A1A` with a 1pt gold inner stroke (`#E8A04D`)
- Text: `#FAF6F0`, with a 16pt filled Rose icon in `#E8A04D` leading the label
- Padding: 14pt vertical, 28pt horizontal
- Corner radius: 28pt
- Selected/Rose-spent state: background turns `#E8A04D`, text turns `#1A1A1A`
- The Rose CTA always carries a `.heavy` haptic — it costs currency and the haptic communicates weight

**Heart Tap (under every prompt and photo — the most-pressed button)**
- Container: 44pt circle, `#FAF6F0` fill on canvas with 1pt `#1A1A1A` stroke
- Icon: filled heart, `#1A1A1A`, 18pt
- Pressed state: scale 0.88, fill flips to `#1A1A1A` solid with white heart
- Tapped state (the "leave a comment" affordance): expands into a sheet with a text field and the chosen prompt/photo pinned at top
- Haptic: `.impactOccurred(.light)` on initial tap; `.impactOccurred(.medium)` on send

**Secondary Text Button ("Decline" on Likes You, "Skip" on prompts)**
- Background: transparent
- Text: `#4A4239`, Sailec 14pt weight 500
- No border, 44pt hit target
- Underline only on text-link variants ("Read more", "Show all")

**X Decline (Likes You stack)**
- 48pt circle, `#FAF6F0` fill, 1.5pt `#1A1A1A` stroke, 22pt black X glyph
- Lives bottom-left of the Likes You card; the heart-with-comment lives bottom-right
- Pressed state: scale 0.92

### Cards & Containers

**Prompt Card (The Hero Component)**
- Width: full content width (viewport - 32pt horizontal margins)
- Background: `#FAF6F0` (Paper White, NOT cream — slightly whiter than canvas for the lift)
- Border: 0.5pt `#E0D6C5` (Divider Bone)
- Corner radius: 16pt
- Padding: 24pt all around
- Structure: question at top (Sailec 22pt Medium `#4A4239`, italicized on certain templates), 12pt gap, answer (Sailec 24pt Bold `#1A1A1A`), then a 44pt heart-tap button bottom-right with 12pt inset
- Shadow: `rgba(28, 20, 10, 0.06) 0 2px 8px` — barely-there paper lift
- The card is the canonical reactive surface — tap anywhere on it (not just the heart) and you go straight into "leave a comment"

**Photo Card**
- Width: full content width
- Aspect: 4:5 (portrait) — taller than Tinder's photos, by design
- Corner radius: 16pt
- Heart-tap: 44pt circle in bottom-right with 16pt inset
- No caption overlays — Hinge keeps photos clean; the captions live in prompt cards

**Attribute Chip Row (Vitals)**
- Each chip: pill, `#F2EBE0` (Surface Sand) background, 8pt vertical / 14pt horizontal padding, Inter 14pt Medium `#1A1A1A`
- 13pt SF Symbol or custom glyph leading the label (e.g., `ruler`, `briefcase.fill`, `mappin`, `graduationcap.fill`)
- 8pt gap between chips, wrapping into 2-3 rows under the name header
- Verified-attribute chips get a tiny `checkmark.seal.fill` in `#2D7A4B` (Match Green) trailing the label

**Standouts Card (Premium hero)**
- Width: full minus 32pt margins
- Aspect: 1:1 (square) for the photo at top
- Corner radius: 20pt
- Background: `#FAF6F0` with a 1pt `#E8A04D` (Rose Gold) gradient border — this is the only gold-bordered surface in the app
- Header strip above the photo: 32pt tall, `#F5D9A8` (Rose Gold Light) fill, 14pt `star.fill` in `#E8A04D` + "Standout" label in Sailec 12pt Bold `#1A1A1A`
- Below the photo: a single prompt answer in Sailec 24pt Bold, then the Rose CTA pinned at the bottom
- Tap haptic: a softer `.impactOccurred(.soft)` because the Standouts list is delicate — these are people specifically curated for you

**Likes You Tile (grid view)**
- 2-col grid, 8pt gap, full-bleed photos with the answered prompt below
- Each tile: 16pt radius, photo 4:5 aspect, prompt text below in Sailec 16pt Medium `#1A1A1A` truncated to 2 lines
- Blur overlay on non-subscribed accounts — full reveal requires Hinge+
- Tap opens the full vertical-scroll profile

### Navigation

**Top Header Bar (Discover / Likes You / Standouts / Matches / Profile)**
- Height: 56pt (no large-title pattern like Airbnb — Hinge uses a slim header)
- Background: `#FDF8F2` (canvas), no blur — content scrolls under cleanly
- Title: Sailec 24pt Bold `#1A1A1A`, left-aligned, 20pt leading margin
- Trailing icons: 24pt filter icon on Discover, 24pt sort-by icon on Likes You

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FDF8F2` with 0.5pt `#E0D6C5` top divider; semi-transparent `.regularMaterial` blur when content scrolls beneath
- Tabs: Discover (compass), Likes You (heart-stack), Standouts (star), Matches (chat-bubble), Profile (person)
- Icon size: 24pt, outlined default / filled when active
- Active color: `#1A1A1A` (Hinge Black) — no special accent; the active tab uses filled icons + a 2pt black indicator dot 4pt below the icon
- Inactive: `#7A7268`
- Labels: Sailec 10pt weight 500, visible always
- Tap haptic: `.selectionChanged()` on each switch

**The "H" Mark**
- The proprietary "H" logomark — a tall, thin slab-serif uppercase "H" usually inside a circle
- Render: `#1A1A1A` on cream canvas; reversed (Paper White on Hinge Black circle) for splash and onboarding hero
- Used on splash, the Match celebration screen behind the names, and as a watermark on shared profile links
- Never rendered in any color other than `#1A1A1A` or `#FAF6F0` (and the rare gold variant on the "Standouts" launch screen)

### Input Fields

**Comment Input (the most-used input in the app)**
- Background: `#F2EBE0` (Surface Sand)
- Border: none
- Corner radius: 24pt (capsule)
- Padding: 14pt vertical, 20pt horizontal
- Placeholder: "Add a comment…" in Inter 16pt Regular `#7A7268`
- Send icon: 28pt circle in `#1A1A1A` trailing the input, white arrow glyph
- Disabled state (no text): send circle drops to `#B0A89C`
- The comment input is paired with the prompt or photo it's replying to — that source is pinned at the top of the sheet, sized to half the available height

**Text Field (Profile editing, settings)**
- Background: `#FAF6F0` (Paper White)
- Border: 1pt `#E0D6C5` default, 1.5pt `#1A1A1A` focused
- Corner radius: 12pt
- Height: 52pt
- Label: floating — sits as placeholder at 16pt Regular `#7A7268` when empty; animates to 12pt Medium `#4A4239` at the top-left corner when focused or filled
- Text: Inter 16pt Regular `#1A1A1A`

**Prompt Picker (full-screen modal)**
- Scrollable list of prompt templates grouped by mood ("About me", "Self-care", "Story time")
- Each prompt row: 64pt tall, `#FAF6F0` background, 12pt radius, Sailec 16pt Medium label, 1pt `#E0D6C5` divider beneath
- Tapping a prompt expands inline into a text editor with character counter (max 150 chars) in Inter 12pt at top-right

### Distinctive Components

**Match Celebration ("You matched with…")**
- Full-screen takeover, `#FDF8F2` canvas
- Two photos side-by-side at top (each 120pt circle), 24pt gap, soft confetti animation in Rose Gold particles
- "It's a match!" in Sailec 36pt Bold `#1A1A1A` centered
- Subline: "You and Sigrún liked each other" in Inter 16pt Regular `#4A4239`
- Primary CTA: "Send a message" pill, full width, Hinge Black
- Secondary CTA: "Keep browsing" text button below
- Haptic on entry: `.notificationOccurred(.success)` + a slight `.impactOccurred(.soft)` 200ms later as the photos settle

**Rose Send Sheet**
- A bottom sheet, top corners 24pt radius
- 80pt rose icon at top in `#E8A04D` (Rose Gold), with a subtle 1500ms pulse halo
- "Send Sigrún a Rose?" in Sailec 22pt Bold
- Sub: "You have 3 Roses · Roses stand out 3× more than likes"
- Primary CTA: "Send Rose" with the rose icon leading
- Tertiary action: "Maybe later"
- Background: `#FAF6F0` with a 1pt Rose Gold border on the sheet to signal the premium moment

**Profile Vertical Scroll (the architecture)**
- A long single-column scroll: name+age header → vitals chip row → photo 1 → prompt 1 → photo 2 → prompt 2 → photo 3 → prompt 3 → photo 4 → vitals 2 (Religion/Politics/Family Plans) → photo 5 → prompt 4 → photo 6 → bottom action bar
- Each photo and each prompt is a separate reactive card with its own heart-tap
- 16pt vertical gap between cards
- Bottom action bar (sticky on Likes You; bottom-of-scroll on Discover): X Decline / Heart Tap area
- The architecture is the brand — there is no swipe gesture; advancement is read-and-tap, not flick

**Roses Currency Sheet**
- "You have 3 Roses" display, Sailec 28pt Bold centered, with three small `rose` icons floating above
- A 3-row purchase ladder ("3 Roses for $3.99", "12 Roses for $14.99", "36 Roses for $39.99") in Sailec 18pt Medium, each row a tappable pill
- Each row has a tiny rose-count icon and the price right-aligned in Sailec 16pt Bold

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Standard horizontal margin: 16pt on lists; 24pt inside prompt cards
- Section vertical gap: 16pt between prompt and photo cards (this gap defines the profile rhythm)
- Generous vertical padding inside prompt cards (24pt) — the words need to breathe

### Grid & Container
- Content width: full device width minus 16pt horizontal margins
- Profile: single column, full-width cards stacked vertically — no two-up layout, ever
- Likes You: 2-col grid with 8pt gap on the grid view; single-column when viewing a specific profile
- Standouts: vertical carousel, one card at a time, full-width
- Attribute chips: wrap horizontally, never scroll horizontally

### Whitespace Philosophy
- **Cream gives the spacing visual weight**: the off-white canvas reads as breathing room even at zero margin — Hinge can use tighter spacing than Airbnb because the cream itself is a soft pillow
- **Prompts get more vertical air than photos**: 24pt internal padding on prompt cards versus 16pt around photos — the words are the focal points
- **Sticky chrome is minimal**: just the top header (56pt) and tab bar (56pt + safe area) — the rest of the screen is profile

### Border Radius Scale
- Square (0pt): rarely used — only on certain attribute glyphs
- Small (4pt): toast notifications, tiny pills
- Medium (12pt): text fields, settings rows
- Standard (16pt): prompt cards, photo cards — the signature Hinge radius
- Large (20pt): Standouts cards, modal sheets
- Capsule (28pt): primary CTAs, comment inputs — the rounded paper-feel
- Full Pill (500pt): attribute chips, secondary chips
- Circle (50%): tab indicator dot, heart-tap container, avatar in Match celebration

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, section dividers |
| Card (Level 1) | `rgba(28, 20, 10, 0.06) 0 2px 8px` | Prompt cards, photo cards — paper-soft lift |
| Floating (Level 2) | `rgba(28, 20, 10, 0.10) 0 4px 12px` | Standouts cards, hero modules |
| Overlay (Level 3) | `rgba(28, 20, 10, 0.14) 0 8px 24px` | Comment sheet, prompt picker modal |
| Sheet (Level 4) | `rgba(28, 20, 10, 0.18) 0 -12px 32px` | Bottom sheets (Rose send, settings actions) |
| Blur Material | `.regularMaterial` over `#FDF8F2` at 92% | Sticky bottom action bar on Likes You, tab bar |

**Shadow Philosophy**: Hinge's shadows are warm-tinted — they use a base color of `rgba(28, 20, 10, x)` rather than `rgba(0, 0, 0, x)`, which keeps the lift consistent with the cream canvas. The result is paper-on-paper depth: a prompt card lifts off the cream like a torn-out journal page placed on a leather notebook. Shadows never exceed 0.18 opacity; nothing in Hinge "pops".

### Motion
- **Heart tap (the most repeated motion in the app)**: scale 1.0 → 0.88 → 1.0 over 250ms spring (damping 0.6), paired with `.impactOccurred(.light)` haptic; fill morph from white→black happens on the down-press
- **Card tap into comment sheet**: shared-element 400ms spring — the tapped card scales up and slides to the top of the sheet, content cross-fades; the rest of the profile blurs slightly behind
- **Match celebration confetti**: 60fps Rose Gold particle fall for 1800ms, paired with a `notificationOccurred(.success)` and a soft photo-pulse on the two avatars
- **Rose Send pulse halo**: the rose icon pulses scale 1.0 → 1.05 → 1.0 over 1500ms while the sheet is open — a gentle "this is precious" cue
- **Standouts card swipe**: vertical carousel with subtle parallax — the gold border on the active card brightens from `rgba(232, 160, 77, 0.6)` to `rgba(232, 160, 77, 1.0)` during transition
- **Tab switch**: 200ms ease — icons transition outlined→filled and the indicator dot fades in 4pt below; `.selectionChanged()` haptic on each tab

## 7. Do's and Don'ts

### Do
- Use the cream canvas (`#FDF8F2`) everywhere — it's the brand's most recognizable surface treatment
- Reserve Rose Gold (`#E8A04D`) for Standouts, Roses, and premium edges — nothing else
- Render the "H" mark in Hinge Black (`#1A1A1A`) on cream, or Paper White on Hinge Black — never gold, never any other color except the launch-screen variant
- Use Sailec for display, prompts, names, and CTAs; Inter or system for attribute chips and chrome
- Make every prompt card and photo independently tappable — the unit of interaction is "a moment on the profile", not "a profile"
- Put the heart-tap button on every reactive surface with consistent placement (bottom-right, 12pt inset)
- Use warm Hinge Black (`#1A1A1A`) for primary text, not pure black — the warmth is the brand
- Use warm-tinted shadows (`rgba(28, 20, 10, x)`) — never neutral blue-greys
- Add the `.impactOccurred(.light)` haptic on heart-tap and the `.impactOccurred(.heavy)` on Rose-send — currency gets weight
- Render attribute chips with a 13pt SF Symbol leading the label, on `#F2EBE0` (Surface Sand) pills
- Treat the comment input as a first-class element — capsule-rounded, generous padding, source-pinned

### Don't
- Don't use a Tinder-style swipe stack — Hinge profiles are vertical scrolls, not card decks. The interaction model IS the design
- Don't render the canvas in pure white (`#FFFFFF`) — cream (`#FDF8F2`) is the signature
- Don't use gold (`#E8A04D`) anywhere except Standouts, Roses, and the Rose CTA — it loses its meaning if it appears in chrome
- Don't use pure black (`#000000`) for type — `#1A1A1A` is warm and intentional
- Don't make the prompt question larger than the answer — the user's words should always be the loudest element
- Don't use a 2-col grid for the main profile flow — vertical single-column is the Hinge pattern; 2-col is reserved for the Likes You preview grid
- Don't skip the heart-tap on any reactive card — the consistency is the muscle memory of the app
- Don't add a "super like" rocket or any Tinder-style escalation glyph — Hinge has Roses, full stop
- Don't put attribute chips in a horizontal scroll — they wrap, never scroll
- Don't use cool greys anywhere — Hinge's neutrals are all warm-tinted (sand, bone, graphite, stone)
- Don't blur Likes You photos for paying subscribers — the blur is the paywall, and it must lift cleanly when subscribed

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Prompt card padding drops to 20pt; photo aspect shifts 4:5 → 1:1.2 for vertical density |
| iPhone 13/14/15 | 390pt | Standard 4:5 photos, 24pt prompt card padding, 16pt horizontal margins |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected; the top header drops slightly to make room |
| iPhone 15/16 Pro Max | 430pt | Photos render slightly larger; prompt answer can reach 26pt for visual parity |
| iPad | 768pt+ | 2-col profile rendering (photos right, prompts left), or full-bleed single column with side margins to keep prompt cards readable |

### Dynamic Type
- Prompt questions, prompt answers, name, body, metadata: full scale
- Attribute chips, tab labels, the "H" mark sizing: fixed
- Comment input: full scale; the heart-tap glyph stays fixed but the input row grows

### Orientation
- All swipe surfaces: portrait-locked
- Photo lightbox (tap-to-zoom on a profile photo): full-bleed, rotates to landscape
- The Match celebration: portrait only — confetti choreography is portrait-tuned

### Touch Targets
- Heart-tap: 44pt circle, 56pt full hit area (8pt inset hit-slop)
- Attribute chip: 36pt tall, 80pt minimum width
- Tab bar icons: 24pt icon, full 56pt row
- CTAs: 56pt height, full width minus 24pt margins

### Safe Area Handling
- Top: safe area honored — the 56pt header sits below the dynamic island/status bar
- Bottom: the sticky bottom action bar on Likes You respects home indicator; tab bar pins above home indicator on all other screens
- Sides: 16pt content insets on lists; 24pt inside prompt cards

## 9. Quick Reference Cheat Sheet

### Quick Color Reference
- Canvas: `#FDF8F2` (Hinge Cream)
- Paper White (cards): `#FAF6F0`
- Surface Sand (chips, inputs): `#F2EBE0`
- Divider Bone: `#E0D6C5`
- Hinge Black (primary): `#1A1A1A`
- Hinge Graphite (secondary): `#4A4239`
- Hinge Stone (tertiary): `#7A7268`
- Rose Gold (Standouts/Roses): `#E8A04D`
- Rose Gold Light (Standouts halo): `#F5D9A8`
- Match Green: `#2D7A4B`
- Error Red: `#B33A2F`
- Dark canvas: `#16130E`

### Example Component Prompts
- "Create a SwiftUI Hinge prompt card: full-width card with `#FAF6F0` (Paper White) fill, 0.5pt `#E0D6C5` border, 16pt corner radius, 24pt internal padding, and a `rgba(28, 20, 10, 0.06) 0 2px 8px` shadow. The prompt question 'Two truths and a lie' is at the top in Sailec 22pt Medium `#4A4239`. Below it, the user's answer in Sailec 24pt Bold `#1A1A1A`, wrapping to 3-4 lines. A 44pt circle heart-tap button sits in the bottom-right with 12pt inset — `#FAF6F0` fill, 1pt `#1A1A1A` stroke, filled-heart glyph in `#1A1A1A` at 18pt. The whole card is tappable and bounces to scale 0.98 on press."
- "Build the Hinge bottom tab bar: 56pt tall plus safe area, `#FDF8F2` (cream) background with 0.5pt `#E0D6C5` top divider and a `.regularMaterial` blur layer for when content scrolls beneath. Five tabs: Discover (compass), Likes You (heart-stack), Standouts (star), Matches (chat-bubble), Profile (person). All icons 24pt, outlined when inactive (`#7A7268`), filled when active (`#1A1A1A`). Labels in Sailec 10pt Medium below each icon. A 4pt circle indicator dot in `#1A1A1A` appears 4pt below the active label. Selection haptic on tap."
- "Design the Standouts hero card: 1:1 photo at top with 20pt corner radius on the outer card. A 32pt header strip above the photo in `#F5D9A8` (Rose Gold Light) with a 14pt `star.fill` glyph in `#E8A04D` followed by 'Standout' in Sailec 12pt Bold `#1A1A1A`. Below the photo, a single prompt answer in Sailec 24pt Bold over 16pt padding. Pinned to the bottom: a Rose CTA — `#1A1A1A` background with a 1pt `#E8A04D` inner stroke, 'Send a Rose' label in Sailec 16pt Bold `#FAF6F0`, leading 16pt rose glyph in `#E8A04D`. The whole card has a 1pt Rose Gold gradient border around the outer frame to signal premium."
- "Create the Hinge match celebration screen: full-screen `#FDF8F2` canvas. Two 120pt circular avatars side-by-side at the vertical-center-third, 24pt apart. Rose Gold confetti particles fall from above for 1800ms on entry. Below the avatars, 'It's a match!' in Sailec 36pt Bold `#1A1A1A` centered, with -0.6pt letter spacing. Subline 24pt below: 'You and Sigrún liked each other' in Inter 16pt Regular `#4A4239`. Primary CTA 'Send a message' at the bottom — full-width capsule, 56pt tall, `#1A1A1A` background, `#FAF6F0` text in Sailec 16pt Bold. Below the CTA, a text-only 'Keep browsing' button in Inter 14pt Medium `#4A4239`. Entry haptic: `.notificationOccurred(.success)`."
- "Build the Hinge comment sheet: bottom sheet with 24pt top corner radius, `#FAF6F0` background. The tapped prompt/photo source pins at the top, scaled to occupy the upper half of the sheet — for prompts, render the question + answer in a card with `#FDF8F2` fill and a thin `#E0D6C5` border. Below the source, a capsule comment input — `#F2EBE0` background, 24pt radius, 'Add a comment about her response' placeholder in Inter 16pt Regular `#7A7268`, 14pt vertical and 20pt horizontal padding. A 28pt circle send button trails the input — `#1A1A1A` fill, white arrow glyph; reduces to `#B0A89C` when input is empty. Send haptic: `.impactOccurred(.medium)`. On send, the sheet animates down with a soft Match Green toast at the bottom."

### Iteration Guide
1. Canvas is `#FDF8F2` (cream); use `#FAF6F0` (Paper White) only on cards, sheets, and inputs
2. Primary action is `#1A1A1A` (Hinge Black) — Send Like, Match, Send Rose, active tab
3. Rose Gold `#E8A04D` is sacred — only Standouts, Roses, and premium edges
4. Body text is `#1A1A1A` warm-black, NOT `#000000` — this is what makes the surface feel like paper
5. Prompt cards are 16pt radius with 24pt internal padding — answers in Sailec 24pt Bold
6. The heart-tap button sits on every reactive card at bottom-right with 12pt inset, 44pt circle
7. Sailec for display + prompts; Inter (or system) for attribute chips and chrome
8. Shadows are warm-tinted (`rgba(28, 20, 10, x)`), low opacity (0.06-0.18), short blur (2-12pt)
9. The architecture is vertical scroll — never a swipe stack; advancement is read-and-tap
10. Dark mode is warm dark `#16130E` canvas with `#EFE8DA` primary text; Rose Gold brightens to `#F0B05C` for OLED
