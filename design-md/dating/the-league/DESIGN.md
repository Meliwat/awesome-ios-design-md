# Design System Inspiration of The League (iOS)

## 1. Visual Theme & Atmosphere

The League's iOS app is a black-tie members' club rendered as software. Open it and the entire screen is a near-true-black canvas (`#0A0A0A`) — the color of a private club after dark, a velvet rope, a leather-bound member directory. There is almost no UI chrome in the conventional sense: no shadows, no bright fills, no playful color. Depth is created entirely by **1px gold-tinted hairline borders** and an embossed calling-card treatment. The signature object is the **prospect card** — a single curated member presented like a heavy paper calling card: a photo in the top two-thirds, then a Cormorant-style display-serif name with the age in a lighter weight, a short gold hairline rule, and a stack of credential bullets (title · company, school, neighborhood) each prefixed with a small gold mark. It does not read like a swipe-app card; it reads like an embossed introduction handed to you by a maître d'.

The League's brand promise is exclusivity and curation — you don't browse infinitely; a **concierge** hand-selects a small daily "batch" of vetted members. The interface enforces that scarcity: a "Today's Batch — 7 Prospects" banner sits above the card, and the concierge appears as a recurring character via the **Concierge Note** — an italic serif message, gold-accented and set behind a left gold rule, that reads like a personal introduction ("I think you and Eleanor would hit it off — you both ran the New York Marathon and studied at HBS"). This human, hand-curated voice is the emotional core of the product, and the design language — serif, gold, hairline, restraint — exists to make the experience feel earned and refined rather than gamified.

The color system is monastically disciplined. One gold (`#C8A35A`, with a brighter `#DBBA71` and deeper `#A8863F` for states) does *all* the accent work: the crest/wordmark, every hairline rule, the single primary CTA, the credential bullets, the verification seal, and the active tab. There is no second accent. Champagne (`#E8DCC0`) appears rarely on premium/celebration surfaces; a muted sage (`#6FAE8A`) and dusty rose (`#C77A7A`) cover success/error only. Photography is the only full-color element and it sits inside a hairline-bordered frame so it reads as a mounted portrait, not a feed image. The League is, deliberately, almost monochrome — black, gold, and a warm off-white for text.

Typography is the brand's voice. A **Cormorant Garamond–class display serif** carries every name, the wordmark, and screen titles — high-contrast, literary, the typography of an engraved invitation. A clean geometric sans (**Jost**-class) handles credentials, body, and UI, frequently in a quiet letter-spaced uppercase for eyebrows and tab labels. The serif/sans split is not decorative — it encodes the hierarchy: serif = the person and the institution (gravitas), sans = the machine and the metadata (utility). Names are serif 26pt bold; ages drop to a lighter serif weight beside them; eyebrows are 11pt uppercase gold tracked +2px; tab labels are tiny 9pt uppercase.

**Key Characteristics:**
- Near-true-black canvas (`#0A0A0A`) — a private club after dark; no light mode in spirit
- One rationed gold (`#C8A35A`) does all accent work — crest, hairlines, one CTA, credential bullets
- **No drop shadows** — depth comes entirely from 1px gold-tinted hairline borders + embossed treatment
- The **prospect card** reads like an embossed calling card, not a swipe-app photo card
- Display **serif** (Cormorant Garamond–class) for names / wordmark / titles; clean **sans** (Jost-class) for credentials / UI
- Serif/sans split encodes hierarchy: serif = person & institution, sans = metadata & machine
- **Concierge** as a recurring character — italic serif notes, gold left-rule, personal introductions
- Scarcity made visual — "Today's Batch · 7 Prospects" banner; curated, finite, never infinite scroll
- Credential bullets (title · company, school, neighborhood) with small gold marks — the LinkedIn-meets-Vogue dossier
- Sharp, minimal corner radii (2–6pt) — engraved-stationery geometry, never rounded/friendly
- Quiet letter-spaced uppercase for eyebrows and tabs — black-tie restraint
- Champagne (`#E8DCC0`) only on premium / celebration; sage success, dusty-rose error — nothing louder

## 2. Color Palette & Roles

### Primary (Interactive)
- **League Gold** (`#C8A35A`): The single accent — crest/wordmark, hairline rules, primary CTA, credential marks, verification seal, active tab.
- **Gold Bright** (`#DBBA71`): Hover/focus on gold elements, the brighter glint on the crest.
- **Gold Deep** (`#A8863F`): Pressed state of the primary CTA, gold dividers behind glass.
- **League Black** (`#000000`): The absolute base — splash, modal scrims, behind the canvas.

### Canvas & Surfaces (Dark — the only theme in spirit)
- **True Black Canvas** (`#0A0A0A`): Primary canvas — near-true black, the club after dark.
- **Surface 1** (`#141414`): Cards, banners, list rows, sheets — the first lift off the canvas.
- **Surface 2** (`#1C1A17`): Prospect-row tiles, concierge notes, inset fields — a hair warmer.
- **Gold Hairline** (`#2A2620`): The 1px gold-tinted border that does ALL the depth work.

### Text
- **Text Primary** (`#EDE9E2`): Names, headings, body — a warm off-white, never pure white.
- **Text Secondary** (`#9E988C`): Credentials, metadata, captions — a muted warm gray.
- **Text Tertiary** (`#6B665C`): Disabled, inactive tabs, very low emphasis.
- **Champagne** (`#E8DCC0`): Premium/celebration headings only — a step above primary, rarely used.
- **On-Gold Ink** (`#1A1408`): Text on a gold-filled surface (the primary CTA label) — near-black warm brown.

### Accents (Rationed)
| Role | Color | Use |
|------|-------|-----|
| Accent / CTA / hairline | `#C8A35A` | Everything gold — the only accent in the system |
| Accent bright | `#DBBA71` | Gold hover/focus, crest glint |
| Accent deep | `#A8863F` | Pressed CTA, gold dividers under glass |
| Premium / celebration | `#E8DCC0` | "It's a Match" / membership-tier surfaces only |
| Success | `#6FAE8A` | "Introduction sent", profile-approved — muted sage |
| Error / Destructive | `#C77A7A` | Decline, leave-the-League, form errors — dusty rose |

### Semantic
- **Success** (`#6FAE8A`): Introduction sent, application accepted.
- **Error / Destructive** (`#C77A7A`): Pass-with-confirmation, unmatch, declined application.
- **Waitlist / Pending** (`#9E988C`): "On the waitlist", "Under review" — neutral secondary, never alarming.
- **Seal Glow** (`#C8A35A` at 20%): Soft gold halo behind the verification seal on a confirmed prospect.

## 3. Typography Rules

### Font Family
- **Display Serif**: A high-contrast transitional serif in the **Cormorant Garamond** family (Google Fonts, SIL OFL) — the engraved-invitation voice. Carries names, the wordmark, screen titles, and concierge notes (italic).
- **UI Sans**: A clean geometric grotesque in the **Jost / Futura** family (Jost — Google Fonts, SIL OFL) — credentials, body, buttons, eyebrows, tabs. Often letter-spaced uppercase.
- **Fallback Stack (serif)**: `'Cormorant Garamond', 'Playfair Display', Georgia, 'Times New Roman', serif`
- **Fallback Stack (sans)**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Futura', 'Helvetica Neue', sans-serif`
- **Numerals**: Old-style figures in serif names/ages where available; lining tabular in metadata and timestamps.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Wordmark | Serif | 34pt | 700 | 1.0 | 0.2pt | "The League", gold, sometimes uppercase tracked |
| Prospect Name | Serif | 26pt | 700 | 1.1 | 0.2pt | Age beside in 400 weight, secondary color |
| Screen Title | Serif | 21pt | 600 | 1.2 | 0.2pt | "Today's Batch", "Concierge" |
| Concierge Note | Serif (italic) | 16pt | 500 | 1.6 | 0pt | Italic, primary color, behind a gold left rule |
| Subtitle | Sans | 16pt | 500 | 1.4 | 0.3pt | Section intro, often title-case |
| Body | Sans | 15pt | 400 | 1.6 | 0pt | Paragraph, descriptions |
| Credential | Sans | 13pt | 400 | 1.7 | 0pt | "VP Strategy · Goldman Sachs" — secondary color |
| Eyebrow / Label | Sans | 11pt | 600 | 1.0 | 2.0pt | UPPERCASE gold — "Members Only", "Today's Batch" |
| Button | Sans | 13pt | 600 | 1.0 | 1.6pt | UPPERCASE — "Send Heart", "View Profile" |
| Caption | Sans | 12pt | 400 | 1.4 | 0pt | Timestamps, helper text |
| Tab Label | Sans | 9pt | 500 | 1.0 | 1.0pt | UPPERCASE — "Prospects", "Concierge" |
| Stat Number | Serif | 21pt | 600 | 1.0 | 0pt | "7 Prospects" — the batch count |

### Principles
- **Serif is the person; sans is the machine**: Names and institutions get the display serif (gravitas); credentials, UI, and metadata get the quiet sans. This split *is* the hierarchy — never set a name in sans or a tab label in serif.
- **Restraint over expression**: Type is set with generous tracking on labels and tight, confident leading on serif headings — the rhythm of engraved stationery, not a consumer app.
- **Uppercase carries the chrome**: Eyebrows, buttons, and tabs are letter-spaced uppercase sans — black-tie discipline. Body and names are mixed-case.
- **Gold in type is rationed**: The serif wordmark and eyebrows are gold; everything else is the warm off-white. Gold never bleeds into body or credentials.
- **Italic = the concierge voice**: Italic serif is reserved for concierge messages — it personifies the human curator.
- **Dynamic Type**: Names, titles, body, credentials, concierge notes scale; eyebrows, tab labels, the batch count, and button labels stay fixed (layout-sensitive uppercase).

## 4. Component Stylings

### Buttons

**Primary Button (Send Heart / Request Introduction)**
- Shape: Sharp rectangle, 3pt corner radius (engraved-plate geometry, never pill)
- Background: `#C8A35A` (League Gold)
- Text: `#1A1408` (on-gold ink), Jost 13pt weight 600, UPPERCASE, +1.6px tracking
- Padding: 15pt vertical, 30pt horizontal
- Pressed: background `#A8863F` + scale 0.98
- One per screen — the single moment of gold fill

**Secondary / Outline Button (View Profile)**
- Background: transparent
- Text: `#C8A35A`, Jost 13pt weight 600, UPPERCASE, +1.6px tracking
- Border: 1px `#C8A35A`
- Padding: 14pt vertical, 26pt horizontal
- Corner radius: 3pt
- Pressed: background `#C8A35A` at 10% opacity

**Ghost Button (Pass / Not Now)**
- Background: transparent
- Text: `#9E988C`, Jost 13pt weight 600, UPPERCASE, +1.4px tracking
- Border: 1px `#2A2620` (gold hairline)
- Corner radius: 3pt
- Pressed: border brightens to `#3A352B`

**Text Button (Upgrade Membership)**
- Font: Jost 12pt weight 600, UPPERCASE, +1.4px tracking
- Color: `#C8A35A`
- No background, no underline
- Pressed: `#A8863F`

### Prospect Card (the core atom)

The defining component — a curated member as an embossed calling card.
- Container: full-width minus 22pt insets, 6pt corner radius, 1px `#2A2620` border, background `#100F0C`, **no shadow**
- Photo: top ~64% of the card, full-color, square-bottom (the only color element; framed by the hairline)
- Verification seal: 30pt circle, 1px gold border, gold star glyph, top-right over the photo, on a `rgba(10,10,10,0.6)` plate
- Body zone (lower 36%): 14pt/18pt padding
  - Name: serif 24–26pt w700 `#EDE9E2`; age inline in serif 400 `#9E988C`
  - Gold rule: 28pt × 1px `#C8A35A` divider beneath the name
  - Credentials: 3 lines, sans 13pt `#9E988C`, each prefixed with a small gold `◦` mark — title · company / school / neighborhood
- Card actions: a row at the card bottom — Pass (48pt hairline circle, gray X), Heart (56pt circle, 1px gold border, gold heart — the focal action), Message (48pt hairline circle, gold glyph)
- The Heart uses a gold *outline* circle, not a gold fill — restraint; the fill is reserved for the primary CTA

### Concierge Note

The human-curation voice, recurring throughout the app.
- Container: `#1C1A17`, 4pt corner radius, 1px `#2A2620` border, **2px `#C8A35A` left rule**
- Header: small gold star glyph + "YOUR CONCIERGE" eyebrow (11pt uppercase gold +1.6px)
- Body: serif *italic* 16pt `#EDE9E2`, line-height 1.6 — reads as a personal note
- Optional inline CTA: a text button "Make the introduction →" in gold

### Batch Banner

Scarcity made visible.
- Full-width minus 22pt insets, 4pt radius, 1px `#2A2620` border, background `#141414`
- Left: "TODAY'S BATCH" eyebrow (11pt uppercase secondary +1.5px)
- Right: serif count "7 Prospects" (serif 17pt w600 gold)
- Resets daily; an empty state reads "Your next batch arrives at 5pm"

### Navigation

**Bottom Tab Bar**
- Height: 64pt + safe area
- Background: `rgba(10,10,10,0.96)` with a 1px `#2A2620` top border (no blur — opaque black, club-like)
- Tabs (4): Prospects, Matches, Concierge, Profile
- Icon size: 21pt, 1.7px stroke (fine-line, never filled)
- Active color: `#C8A35A`; inactive `#6B665C`
- Labels: Jost 9pt w500, UPPERCASE, +1px tracking, always shown
- No active pill/indicator — the gold tint alone signals selection (restraint)

**Top Bar**
- Centered serif wordmark "The League" (gold, 21pt) with a tiny uppercase "MEMBERS ONLY" eyebrow beneath
- Leading: search (fine-line gold, 22pt); Trailing: settings gear (fine-line gold, 22pt)
- 1px `#2A2620` bottom hairline instead of a shadow

### Input Fields

**Search / Profile Fields**
- Height: 46pt
- Background: transparent; **1px `#2A2620` bottom rule only** (no box) — engraved-form aesthetic
- Focus: the bottom rule brightens to 1px `#C8A35A`
- Placeholder: Jost 14pt `#6B665C`
- Label: 11pt uppercase secondary +1.5px above the field

**Message Composer (Matches thread)**
- A single hairline-bordered field, 3pt radius, transparent fill
- Send affordance: a gold text "SEND" (no filled button) — restraint in conversation too

### Distinctive Components

**Prospect Card** — see above. The embossed calling-card hero.

**Concierge Note** — the italic-serif, gold-left-rule personal introduction; the human voice of the product.

**Batch Banner** — the daily scarcity counter ("7 Prospects"); the anti-infinite-scroll statement.

**Verification Seal** — a gold-bordered star medallion on confirmed members; the club's stamp of vetting.

**Credential Stack** — the title/company/school/neighborhood dossier with gold `◦` marks; the LinkedIn-meets-Vogue résumé that replaces a bio.

**Gold Hairline System** — the 1px `#2A2620`/`#C8A35A` border that replaces every shadow; depth is line, not light.

**Prospect Row (calling-card mini)** — the list form: a gold-ringed circular avatar + serif name + gold rule + one-line credential.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 18, 22, 28, 36, 48, 64
- Screen inset: 22pt horizontal (generous club margins, wider than a typical app's 16pt)
- Card body padding: 14pt vertical, 18pt horizontal
- Gold rule under names: 28pt long, 8pt above/below
- Section gap: 24–28pt between major blocks (the app breathes; scarcity = whitespace)

### Grid & Container
- Prospects: one prospect card at a time (curated, not a deck/feed) — full-width minus 22pt
- Matches / Concierge: single-column list of calling-card rows
- iPad: prospect card centered at max-width 460pt with a black surround (a framed portrait on a wall)
- Profile: single-column, hairline-divided credential sections

### Whitespace Philosophy
- **Scarcity is spatial**: few items, lots of black around them — emptiness signals exclusivity, not lack
- **Hairlines instead of fills**: structure is drawn with 1px gold-tinted rules, never colored panels or shadows
- **The card is a mounted object**: a hairline-bordered portrait floating in black, like art in a gallery
- **Generous margins**: 22pt insets and 24pt+ section gaps — the layout equivalent of a quiet, well-spaced room

### Border Radius Scale
- Square (0pt): hairline dividers, photo bottom edges, the verification plate
- Whisper (2pt): inline tags, the credential-mark container
- Plate (3pt): buttons, message field — engraved-stationery geometry
- Card-inner (4pt): banners, concierge notes
- Card (6pt): the prospect card, prospect rows
- Comfortable (8pt): bottom sheets, modals
- Pill (500pt): used ONLY for the membership-tier toggle (rare)
- Circle (50%): avatars, the verification seal, card-action buttons

## 6. Depth & Elevation

The League rejects shadow entirely. Elevation is drawn, not lit — a 1px gold-tinted hairline is the only depth cue.

| Level | Treatment | Use |
|-------|-----------|-----|
| Base (Level 0) | `#0A0A0A`, no border | The canvas |
| Surface (Level 1) | `#141414` + 1px `#2A2620` border | Cards, banners, list rows |
| Featured (Level 2) | `#1C1A17` + 1px `#2A2620` border + 2px `#C8A35A` left rule | Concierge notes, highlighted prospect |
| Sheet (Level 3) | `#141414` + 1px `#2A2620` top border + `rgba(0,0,0,0.7)` scrim behind | Bottom sheets, filters |
| Modal (Level 4) | `#141414` + full `rgba(0,0,0,0.85)` dim | Membership upsell, match celebration |

**Shadow Philosophy**: There are no `box-shadow`s anywhere in The League. A drop shadow would read as soft/consumer; the brand is hard-edged, engraved, black-tie. Separation between a card and the canvas comes from the 1px `#2A2620` border (a gold-tinted graphite line) and the slight tonal lift of the surface (`#141414` vs `#0A0A0A`). The "elevated" feeling of a featured item is signalled by a 2px gold left rule, not by lifting it with light. This is intentional and load-bearing — never add a shadow.

### Motion
- **Card advance (next prospect)**: the current card fades + drifts up 12pt over 280ms ease-out; the next card cross-fades in beneath — restrained, not a thrown swipe
- **Heart send**: the gold outline heart fills gold over 240ms, scales 1.0 → 1.12 → 1.0, then the card recedes; a single soft haptic
- **Concierge note arrival**: the gold left rule draws top-to-bottom (200ms) then the italic text fades in (180ms) — as if being written
- **Batch refresh**: the count rolls (odometer, 320ms) when a new batch lands; a brief gold underline sweep
- **Match / introduction celebration**: a full-screen `#0A0A0A` with a champagne-foil seal that draws on (stroke animation, 600ms) — no confetti; refined, like a wax seal pressed
- **Tab switch**: instant; the active label/icon tint cross-fades to gold over 150ms (no pill slide)
- **Field focus**: the bottom hairline brightens transparent → `#C8A35A` over 160ms (left-to-right wipe)
- **Page push**: iOS-native horizontal push, 300ms; backgrounds stay true-black so transitions feel seamless
- **Haptics**: one soft impact on heart-send and on introduction-accepted (success); a light tick on tab change; nothing buzzy — restraint extends to touch

## 7. Do's and Don'ts

### Do
- Use a near-true-black canvas (`#0A0A0A`) — the club after dark; treat it as the only theme
- Ration gold (`#C8A35A`) to the crest, hairlines, one CTA, and credential marks — nothing else
- Replace every shadow with a 1px gold-tinted hairline (`#2A2620`) — depth is line, not light
- Set names, the wordmark, titles, and concierge notes in the display serif (Cormorant-class)
- Set credentials, body, eyebrows, buttons, and tabs in the geometric sans (Jost-class), often uppercase
- Make the prospect card read like an embossed calling card — photo, serif name, gold rule, credential stack
- Personify the concierge with italic serif notes behind a 2px gold left rule
- Show scarcity explicitly — a daily batch count, finite prospects, never infinite scroll
- Use sharp corner radii (2–6pt) — engraved-stationery geometry
- Letter-space uppercase eyebrows (+2px) and buttons (+1.6px) for black-tie restraint
- Use a gold *outline* heart and reserve the gold *fill* for the single primary CTA
- Keep motion restrained — fades and draws, never a thrown swipe or confetti

### Don't
- Don't introduce a second accent color — gold is the only one; the rest is black and warm off-white
- Don't use drop shadows anywhere — they read soft/consumer; the brand is engraved and hard-edged
- Don't set a prospect name in sans or a tab label in serif — the serif/sans split IS the hierarchy
- Don't round corners generously — no pills (except the rare tier toggle), no friendly 16pt+ radii
- Don't fill buttons or cards with gold by default — gold fill is rationed to one CTA per screen
- Don't make it bright or playful — no saturated colors, no gradients beyond subtle black-to-black
- Don't present an infinite feed — the curated, finite daily batch is the entire product premise
- Don't write in sans where the concierge speaks — the italic serif voice personifies the curator
- Don't use pure white (`#FFFFFF`) for text — warm off-white `#EDE9E2`; pure white is too clinical
- Don't add a Material/iOS active-tab pill — the gold tint alone signals selection
- Don't animate aggressively — celebrations are a drawn wax seal, not a confetti burst
- Don't crowd the layout — scarcity is spatial; generous black margins are the point

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Card insets tighten to 18pt; credential stack stays 3 lines; serif name 22pt |
| iPhone 13/14/15 | 390pt | Standard layout, 22pt insets |
| iPhone 15/16 Pro | 393pt | Wordmark/eyebrow clears the Dynamic Island; hairline top bar respects safe area |
| iPhone 15/16 Pro Max | 430pt | Larger serif name (28pt); card body gains a 4th credential line |
| iPad (portrait) | 768pt | Prospect card centered max-width 460pt in a black surround (a framed portrait) |
| iPad (landscape) | 1024pt+ | Two-pane: prospect card left, profile/concierge thread right; both on true-black |

### Dynamic Type
- Serif name, screen titles, body, credentials, concierge notes: full scale
- Eyebrows, tab labels, the batch count, button labels: FIXED (letter-spaced uppercase, layout-sensitive)
- The name/age serif pair scales together, preserving the weight contrast (700 / 400)

### Orientation
- iPhone: portrait-locked on Prospects (the calling card is portrait by nature)
- Matches, Concierge, Profile: rotate freely
- iPad: full rotation with the two-pane split

### Touch Targets
- Heart action: 56pt circle (the focal action — largest)
- Pass / Message actions: 48pt circles
- Tab bar icons: 21pt glyph, 44pt hit
- Concierge inline CTA: 44pt minimum row height
- Credential bullets: display-only (not interactive)

### Safe Area Handling
- Top: the wordmark + "MEMBERS ONLY" eyebrow respect the safe area / Dynamic Island; the 1px hairline sits below
- Bottom: the opaque-black tab bar respects the home indicator; no floating elements over it
- The prospect card stays fully inside the safe area (it is a mounted object, not full-bleed)
- Keyboard: the hairline message field rises above the keyboard; the thread scrolls on true-black

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#0A0A0A`
- Surface 1: `#141414`
- Surface 2: `#1C1A17`
- Gold hairline (the only "border"/depth): `#2A2620`
- Text primary: `#EDE9E2`
- Text secondary: `#9E988C`
- Text tertiary / inactive: `#6B665C`
- League Gold (the only accent): `#C8A35A`
- Gold bright (hover): `#DBBA71`
- Gold deep (pressed): `#A8863F`
- On-gold ink (CTA label): `#1A1408`
- Champagne (premium only): `#E8DCC0`
- Success (sage): `#6FAE8A`
- Error (dusty rose): `#C77A7A`

### Example Component Prompts
- "Build The League prospect card in SwiftUI: full-width minus 22pt insets, 6pt corner radius, 1px `#2A2620` border, background `#100F0C`, NO shadow. Top 64% is a full-color photo with a square bottom edge; a 30pt circular verification seal (1px `#C8A35A` border, gold star glyph) top-right on a `rgba(10,10,10,0.6)` plate. Lower 36%: name in Cormorant Garamond 26pt w700 `#EDE9E2` with the age inline in Cormorant 400 `#9E988C`; a 28pt × 1px `#C8A35A` rule beneath; then three credential lines in Jost 13pt `#9E988C`, each prefixed with a small gold `◦` — title · company / school / neighborhood. Card-bottom action row: Pass (48pt circle, 1px `#2A2620` border, gray X), Heart (56pt circle, 1px `#C8A35A` border, gold heart — focal), Message (48pt circle, 1px `#2A2620` border, gold glyph)."

- "Create The League concierge note: container `#1C1A17`, 4pt radius, 1px `#2A2620` border, a 2px `#C8A35A` left rule. Header: a small gold star + 'YOUR CONCIERGE' in Jost 11pt w600 UPPERCASE `#C8A35A` +2px tracking. Body: Cormorant Garamond *italic* 16pt `#EDE9E2`, line-height 1.6 — a personal introduction. Optional gold text CTA 'Make the introduction →'. On arrival: draw the gold left rule top-to-bottom over 200ms, then fade the italic text in over 180ms."

- "Build The League batch banner: full-width minus 22pt insets, 4pt radius, 1px `#2A2620` border, background `#141414`. Left: 'TODAY'S BATCH' in Jost 11pt w600 UPPERCASE `#9E988C` +1.5px. Right: '7 Prospects' in Cormorant Garamond 17pt w600 `#C8A35A`. Empty state: 'Your next batch arrives at 5pm'. On refresh: roll the count like an odometer over 320ms with a brief gold underline sweep."

- "Render The League primary button: sharp 3pt-radius rectangle, background `#C8A35A`, label in Jost 13pt w600 UPPERCASE `#1A1408` +1.6px tracking, 15pt/30pt padding. Pressed: background `#A8863F` + scale 0.98. One per screen. Secondary variant: transparent fill, 1px `#C8A35A` border, `#C8A35A` label."

- "Build The League bottom tab bar: 64pt + safe area, opaque background `rgba(10,10,10,0.96)` with a 1px `#2A2620` top border (NO blur, NO shadow). 4 tabs — Prospects, Matches, Concierge, Profile — 21pt fine-line icons (1.7px stroke, never filled), labels Jost 9pt w500 UPPERCASE +1px, always shown. Active tint `#C8A35A`, inactive `#6B665C`. No active pill — the gold tint alone signals selection."

- "Create The League match celebration: full-screen `#0A0A0A`, centered. A champagne `#E8DCC0` wax-seal medallion draws on via a 600ms stroke animation (no confetti). Serif headline 'An Introduction' in Cormorant Garamond 28pt w700 `#E8DCC0`. Subtitle in Jost 13pt `#9E988C`. One gold primary CTA 'Send a Message'. A single soft success haptic on appear — refined, like a wax seal pressed."

### Iteration Guide
1. Canvas is near-true-black `#0A0A0A` — the club after dark; treat it as the only theme
2. Gold `#C8A35A` is the SINGLE accent — crest, hairlines, one CTA, credential marks; nothing else gets color
3. NO shadows anywhere — depth is the 1px `#2A2620` gold-tinted hairline, never light
4. Serif (Cormorant Garamond–class) = names / wordmark / titles / concierge; sans (Jost-class) = credentials / UI / labels
5. The prospect card is an embossed calling card — photo, serif name, gold rule, credential stack — not a swipe-app photo
6. The concierge speaks in italic serif behind a 2px gold left rule — personify the human curator
7. Scarcity is the product — a finite daily batch with a visible count, never infinite scroll
8. Corner radii are sharp (2–6pt) — engraved-stationery geometry; no pills, no friendly rounding
9. Gold fill is rationed to one CTA per screen; the Heart action is a gold *outline*, not a fill
10. Restraint everywhere — uppercase tracked labels, fades not throws, a drawn wax seal not confetti, warm off-white text never pure white
