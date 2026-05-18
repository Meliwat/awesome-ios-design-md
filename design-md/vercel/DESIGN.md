# Design System Inspiration of Vercel (iOS)

## 1. Visual Theme & Atmosphere

Vercel's iOS app is the most uncompromisingly minimal developer tool on the App Store. It opens on **pure black** (`#000000`) — not the charcoal `#191919` that most "dark mode" apps settle on, but absolute, OLED-true black — and almost everything on screen is either white text, a hairline grey border, or one of three status colors. There is no brand-color CTA, no gradient hero, no decorative chrome. The aesthetic is a direct translation of the Vercel marketing site and dashboard: a geometric black triangle logomark, the Geist typeface, and an obsessive commitment to "the content is the product, the chrome is invisible."

The app's spine is the **deployments list**. Every project's screen is dominated by a vertical stack of deployment cards, each showing a colored status dot (Ready green, Building amber, Error red), the deployment state label, an environment tag (`Production` / `Preview`), the deployment URL in monospace, and a git metadata row (branch name, commit, relative time). This list IS Vercel — it is the screen a developer opens fifty times a day to answer one question: "did my push go live?" The signature interaction is pulling to refresh and watching a "Building" card flip to "Ready" with its dot animating from amber to green.

The second pillar is the **project overview** — a header with the production domain, the latest production deployment summary, and quick links to Domains, Analytics, Logs, and Settings — and the third is **Analytics**, a sparse set of bar/line charts (Web Vitals, visitors, top paths) rendered in `#0070F3` blue on the black canvas with no gridlines or ornament. Everything is information-dense and ornament-free; the design philosophy is that a developer's attention is expensive, so the UI spends none of it on decoration.

The color system is radically restrained. The canvas is `#000000`; the only elevation is a single step to `#0A0A0A`/`#111111` for cards, with separation carried by `#2E2E2E` hairline borders rather than shadows. There is no primary brand accent — action is signaled by a high-contrast white "fill" button (`#EDEDED` on black). The accent palette exists almost entirely to communicate **deployment state**: Ready `#0CCE6B`, Building/Queued `#F5A623`, Error/Canceled `#E5484D`, plus `#0070F3` "Vercel Blue" for links, focused inputs, and analytics. A purple→cyan gradient (`#7928CA` → `#50E3C2`) appears only in avatar placeholders and the occasional marketing-style highlight.

Typography is **Geist** — Vercel's own typeface family (Geist Sans for UI, Geist Mono for everything machine-generated). On iOS it falls back to SF Pro / a system mono when Geist isn't bundled. The hierarchy is tight and functional: a bold 32pt display, descending through 26/22/18pt headings, 16pt body, and — critically — **Geist Mono 13pt** for every URL, branch name, commit hash, environment variable, and log line. The split between proportional Sans (human prose) and Mono (machine data) is one of the most legible parts of the system.

**Key Characteristics:**
- Pure black canvas (`#000000`) — true OLED black, NOT charcoal; this is the single most identifying trait
- Deployments list as the home screen — colored status dot + state + env tag + mono URL + git metadata
- Status traffic-light — Ready `#0CCE6B` / Building `#F5A623` / Error `#E5484D` is the only systemic color
- No brand-accent CTA — primary action is a white fill button (`#EDEDED` on black)
- Hairline borders, not shadows — `#2E2E2E` 1pt separators do all the elevation work
- Geist Sans + Geist Mono — proportional for prose, monospace for every URL / branch / hash / log
- Geometric triangle logomark — equilateral triangle, fill-only, never restyled
- Analytics in `#0070F3` on black — Web Vitals + visitors charts with zero gridlines/ornament
- Environment tags — `Production` / `Preview` pills in mono, low-chroma surface fill
- Light mode is a pure inversion — white `#FFFFFF` canvas, `#000000` text, same triangle

## 2. Color Palette & Roles

### Primary (Interactive)
- **Vercel White** (`#EDEDED`): Primary interactive fill — the "Deploy" / primary button background on dark; foreground text on dark.
- **Pure White** (`#FFFFFF`): Highest-emphasis text, light-mode canvas.
- **White Pressed** (`#CFCFCF`): Pressed state of the white fill button.
- **Vercel Blue** (`#0070F3`): Links, focused input border, analytics chart fill, secondary "Visit" action.
- **Vercel Blue Hover** (`#3291FF`): Hover/pressed link & blue button.

### Canvas & Surfaces (Dark — default)
- **Pure Black** (`#000000`): Primary canvas. True black, NOT `#191919`.
- **Surface 1** (`#0A0A0A`): Cards, deployment rows, the one elevation step above canvas.
- **Surface 2** (`#111111`): Inset panels, chart backgrounds, code blocks.
- **Surface 3** (`#1A1A1A`): Pressed rows, environment-tag fill.
- **Divider** (`#2E2E2E`): Hairline borders between rows and around cards (does the work shadows would).
- **Border Strong** (`#333333`): Input borders, focused-element rings (un-focused).

### Canvas & Surfaces (Light)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray** (`#FAFAFA`): Cards, elevated rows.
- **Surface Gray 2** (`#F2F2F2`): Inset panels, pressed rows.
- **Divider Light** (`#EAEAEA`): Hairline borders.
- **Border Light** (`#E1E1E1`): Input borders.

### Text
- **Text Primary Dark** (`#EDEDED`): Primary body & headings on black.
- **Text Secondary Dark** (`#A1A1A1`): Metadata, secondary labels, captions.
- **Text Tertiary Dark** (`#707070`): Disabled, timestamps, lowest emphasis.
- **Text Primary Light** (`#000000`): Primary text on white.
- **Text Secondary Light** (`#666666`): Metadata on white.

### Status (Deployment State — the systemic accent)
| Role | Color | Dim Surface (10–12%) | Use |
|------|-------|----------------------|-----|
| Ready / Success | `#0CCE6B` | `#0F3D2E` | Deployment Ready, status dot, success toast |
| Building / Queued | `#F5A623` | `#3A2A0F` | Building/Queued state, spinner accent |
| Error / Canceled | `#E5484D` | `#3A1416` | Error/Canceled deployment, destructive action |
| Info / Link | `#0070F3` | `#0A2540` | Links, focused input, analytics |

### Highlight (decorative only)
- **Highlight Purple** (`#7928CA`): Avatar gradient start, marketing-style accents.
- **Highlight Cyan** (`#50E3C2`): Avatar gradient end.
- **Highlight Pink** (`#FF0080`): Rare promotional gradient stop.

### Semantic
- **Error** (`#E5484D`): Destructive buttons, failed builds, inline error text.
- **Success** (`#0CCE6B`): Ready state, save-success.
- **Warning** (`#F5A623`): Building/queued, warning callouts.
- **Focus Ring** (`#0070F3` @ 40%): 2pt focus outline on inputs and focusable rows.

## 3. Typography Rules

### Font Family
- **Sans (UI)**: `Geist Sans` (by Vercel + basement.studio, 2023, SIL OFL) — a neutral, low-contrast grotesque designed for developer interfaces and high legibility at small sizes.
- **Mono (machine data)**: `Geist Mono` (Vercel, SIL OFL) — monospace companion for URLs, branches, commit hashes, environment variables, logs, and tabular figures.
- **Fallback Sans Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Fallback Mono Stack**: `'SF Mono', ui-monospace, 'Menlo', 'Consolas', monospace`
- **Not user-switchable**: Geist Sans + Geist Mono are fixed; the split between the two is by content type (prose vs machine).

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Geist Sans | 32pt | 800 | 1.1 | -0.6pt | Top-level screen title ("Overview") |
| Title | Geist Sans | 26pt | 700 | 1.15 | -0.4pt | "Production Deployment" header |
| Section | Geist Sans | 22pt | 700 | 1.2 | -0.3pt | "Recent Deployments" |
| Subsection | Geist Sans | 18pt | 600 | 1.3 | -0.2pt | "Build Logs", card group titles |
| Body | Geist Sans | 16pt | 400 | 1.5 | 0pt | Descriptive prose |
| Label | Geist Sans | 15pt | 500 | 1.3 | -0.1pt | Form labels, list item titles |
| Body Strong | Geist Sans | 14pt | 600 | 1.4 | -0.1pt | Deployment state ("Ready") |
| Meta | Geist Sans | 14pt | 400 | 1.4 | 0pt | "Deployed by alex · 2m ago" |
| URL / Hash | Geist Mono | 13pt | 400 | 1.4 | 0pt | Deployment URLs, branches, commits |
| Log Line | Geist Mono | 12pt | 400 | 1.5 | 0pt | Build-log output, env var rows |
| Caption | Geist Sans | 12pt | 500 | 1.3 | 0.2pt | "PRODUCTION" tag, eyebrows (uppercase) |
| Tab Label | Geist Sans | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Button | Geist Sans | 14pt | 600 | 1.0 | -0.1pt | All buttons |

### Principles
- **Sans for humans, Mono for machines**: Any string a machine produced — URL, branch, commit SHA, env var, log line, build duration — is Geist Mono. Everything a human wrote — labels, prose, headings — is Geist Sans. This split is non-negotiable and is the most legible part of the system.
- **Tight tracking on headings**: Display/Title use negative letter-spacing (-0.6 to -0.3pt) for the dense, engineered Vercel feel.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: No thin, no light. Body 400, labels 500, emphasis/buttons 600, headings 700, display 800.
- **Uppercase only for tiny eyebrows**: `PRODUCTION`, `PREVIEW`, section eyebrows at 12pt with +0.2pt tracking. Never uppercase body.
- **Tabular figures in Mono**: Use Geist Mono's tabular numerals so metric columns and durations align vertically.
- **Dynamic Type**: scale display/headings/body/meta; keep tab labels, env tags, and tiny mono captions fixed.

## 4. Component Stylings

### Buttons

**Primary Button (Deploy / Save / Confirm)**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#EDEDED` (dark) / `#000000` (light — inverted)
- Text: `#000000` dark / `#FFFFFF` light, Geist Sans 14pt weight 600
- Padding: 11pt vertical, 20pt horizontal
- Pressed: background `#CFCFCF` (dark) / `#333333` (light) + scale 0.98
- Disabled: background `#1A1A1A`, text `#707070`

**Secondary / Ghost Button (Redeploy / Cancel)**
- Background: transparent
- Text: `#EDEDED` dark / `#000000` light, Geist Sans 14pt weight 500
- Border: 1pt `#333333` dark / `#E1E1E1` light
- Padding: 10pt vertical, 18pt horizontal
- Corner radius: 8pt
- Pressed: background `#1A1A1A`

**Blue Button (Visit / Open Link)**
- Background: `#0070F3`
- Text: `#FFFFFF`, Geist Sans 14pt weight 600
- Corner radius: 8pt
- Pressed: background `#005FCC`

**Destructive Button (Delete Project / Remove Domain)**
- Background: transparent
- Text & border: `#E5484D` 1pt
- Pressed: background `rgba(229,72,77,0.10)`
- Confirmation: requires typing the project name to enable a solid `#E5484D` fill button

**Icon Button**
- Size: 20pt glyph in 44pt hit area
- Color: `#A1A1A1` default → `#EDEDED` active
- No background; pressed shows `#1A1A1A` 8pt-radius square

### Core Atoms

**Deployment Card**
- Background: `#0A0A0A`, 1pt `#2E2E2E` border, 12pt corner radius, 14pt padding
- Row 1: status dot (9pt, `box-shadow` halo of status color @ 15%) + state label (14pt 600, colored if Building/Error) + environment tag (right-aligned)
- Row 2: deployment URL — Geist Mono 13pt `#EDEDED`
- Row 3: git icon + branch (Mono 13pt `#A1A1A1`) + relative time (right-aligned `#707070`)
- Pressed: background `#111111`

**Status Dot**
- 9pt circle, fill = status color, with a 3pt `box-shadow` halo at 15% of the same color
- Building state: subtle 1.2s pulse on the halo

**Environment Tag**
- Mono 10pt 500, `#A1A1A1`, background `#1A1A1A`, 1pt `#333333` border, 3×7pt padding, 5pt radius
- `Production` may render with a faint `#0CCE6B` left tick on the project header

**Status Badge (pill)**
- Inline-flex, 7pt dot + label, Geist Mono 12pt 500
- Ready: text `#0CCE6B`, border `rgba(12,206,107,0.4)`, bg `rgba(12,206,107,0.08)`
- Building: text `#F5A623` (analogous), Error: text `#E5484D`
- Corner radius: 999pt (pill)

**Card / Panel**
- Background `#0A0A0A`, 1pt `#2E2E2E`, 12–16pt radius, 16–28pt padding — no shadow

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(0,0,0,0.92)` with `backdrop-filter: blur(20px)`, 0.5pt top border `#2E2E2E`
- Tabs (4): Overview, Deployments, Analytics, Settings
- Icon size: 21pt, stroke style 2pt
- Active: `#FFFFFF` (no tint pill — just white) / Inactive: `#707070`
- Labels: Geist Sans 10pt 500, always shown

**Top Bar (Project)**
- Height: 44pt + safe area
- Leading: triangle logomark (22pt) + project name (Geist Sans 17pt 600)
- Trailing: avatar (28pt circle, purple→cyan gradient placeholder)
- No large-title collapse — Vercel keeps a compact, fixed top bar

**Project Switcher (sheet)**
- Slides up as a bottom sheet, `#0A0A0A`, 12pt top radius
- Each row: project name (15pt 500) + production domain (Mono 12pt `#A1A1A1`) + chevron
- Search field pinned at top

### Input Fields

**Text Field**
- Height: 40pt
- Background: `#0A0A0A` dark / `#FAFAFA` light
- Border: 1pt `#333333`, corner radius 8pt
- Text: Geist Sans 15pt `#EDEDED`; mono variant for domains/env values
- Placeholder: `#707070`
- Focus: border `#0070F3` + 2pt `rgba(0,112,243,0.4)` ring
- Error: border `#E5484D`, helper text `#E5484D` 12pt

**Environment Variable Row**
- Key field (Mono 13pt) + value field (Mono 13pt, masked dots until "Reveal")
- Scope chips: `Production` / `Preview` / `Development` — selectable, `#1A1A1A` fill, active = `#0070F3` 1pt border

**Search Field**
- Height: 38pt, leading magnifier 16pt `#707070`
- Background `#0A0A0A`, 1pt `#2E2E2E`, 8pt radius
- Placeholder "Search projects" `#707070`

### Distinctive Components

**Deployment Status Dot + State**
- The atomic Vercel signal. 9pt colored dot + state word. Green=Ready, Amber=Building/Queued, Red=Error/Canceled. The dot's halo pulses while Building.

**Deployments List**
- The home screen. Vertically stacked deployment cards, newest first, grouped implicitly by recency. Pull-to-refresh re-polls; a Building card animates dot amber→green on completion.

**Build Log Viewer**
- Full-bleed `#000000`, Geist Mono 12pt, line numbers in `#707070`, ANSI colors mapped (red errors `#E5484D`, green `#0CCE6B`, dim `#707070`). Auto-scrolls; "Following" pin at bottom.

**Analytics Chart**
- Bar/line charts in `#0070F3` on `#111111`, NO gridlines, NO axis ornament. Web Vitals (LCP/FID/CLS), visitors over time, top paths. Sparse axis labels in Mono 11pt `#707070`.

**Environment Tag**
- `Production` / `Preview` mono pill; the single most repeated chip in the app — see Core Atoms.

**Triangle Logomark**
- An equilateral triangle, single fill (`#EDEDED` dark / `#000000` light). Never gradient-filled, never outlined, never rotated. SVG path: `M37.527 0L75.054 65H0z`.

**Domain Row**
- Domain in Geist Mono 14pt, trailing a status badge: green dot "Valid Configuration" or amber "Pending" / red "Invalid". Inline "Refresh" ghost button.

**Avatar Placeholder**
- 28pt circle, `linear-gradient(135deg, #7928CA, #0070F3)` when no image — the only place the purple gradient appears in the core app.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 10, 12, 14, 16, 20, 24, 28, 32, 40, 48
- Card internal padding: 14pt; section gap: 24pt
- Row vertical rhythm in deployment card: 8pt between the three meta rows
- Screen horizontal inset: 16–18pt

### Grid & Container
- iPhone: single column, 16pt insets, full-width deployment cards
- iPad: max content width ~720pt centered; deployment list may go 2-column at ≥1024pt
- No decorative gutters — content runs edge-to-edge minus the 16pt inset

### Whitespace Philosophy
- **Density with air**: lists are information-dense (3 data rows per card) but cards get 10pt vertical gaps so the eye can parse the stack
- **Borders replace shadows**: separation is a 1pt `#2E2E2E` line, never a drop shadow — keeps the pure-black canvas flat and honest
- **No ornament budget**: zero illustrations, zero decorative gradients in the core flow (gradient lives only in avatar placeholders)
- **Charts breathe by subtraction**: analytics has no gridlines; whitespace itself is the grid

### Border Radius Scale
- Square (0pt): full-bleed log viewer, dividers
- Subtle (4pt): tiny tags, inline code chips
- Tag (6pt): environment tags
- Standard (8pt): buttons, inputs, search field
- Card (12pt): deployment cards, panels
- Comfortable (16pt): sheets, large containers, modals
- Pill (500pt): status badges
- Circle (50%): avatars, status dots

## 6. Depth & Elevation

Vercel is aggressively flat. There is essentially **one elevation step** and shadows are nearly absent.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No border, no shadow | Pure-black canvas, log viewer |
| Bordered (Level 1) | 1pt `#2E2E2E` border, bg `#0A0A0A`, no shadow | Deployment cards, panels, inputs |
| Floating (Level 2) | `rgba(0,0,0,0.6) 0 8px 24px` + 1pt `#333333` | Project-switcher sheet, dropdowns, popovers |
| Modal Overlay | `rgba(0,0,0,0.7)` scrim | Behind bottom sheets and destructive-confirm dialogs |

**Shadow Philosophy**: On a true-black canvas a drop shadow is invisible — so Vercel doesn't fake one. Elevation is communicated by a **lighter surface + a hairline border**, never by blur. Floating layers (sheets, popovers) get the only real shadow plus a slightly stronger `#333333` border so they read as "above" the page. This is the inverse of Material: Vercel removes depth rather than adding it.

### Motion
- **Status transition**: dot color cross-fades amber→green over 240ms ease-out when a Building deployment becomes Ready; soft success haptic
- **Building pulse**: status-dot halo scales 1.0→1.15→1.0 on a 1.2s ease-in-out loop while Building
- **Pull-to-refresh**: native rubber-band; a thin `#0070F3` progress line draws across the top while polling
- **Sheet present**: bottom sheet slides up 300ms ease-out; scrim fades to 70% over 200ms
- **Tab switch**: instant content swap, no slide (Vercel favors immediacy over transition)
- **Button press**: scale 0.98 + background shift, 120ms ease-out
- **Log auto-scroll**: continuous, follows tail; "Jump to bottom" pill fades in if user scrolls up
- **Haptics**: soft success on Ready transition; light selection on tab change; medium on destructive confirm

## 7. Do's and Don'ts

### Do
- Use **true black** `#000000` for the dark canvas — this is Vercel's single most identifying trait
- Lift cards exactly one step (`#0A0A0A`) and separate with a 1pt `#2E2E2E` border — never a shadow
- Make the deployments list the home screen — status dot + state + env tag + mono URL + git meta
- Use the status traffic-light (`#0CCE6B` / `#F5A623` / `#E5484D`) as the ONLY systemic color
- Set every URL, branch, commit hash, env var, and log line in Geist Mono
- Keep the primary button a high-contrast white fill (`#EDEDED` on black) — no brand-accent CTA
- Keep the triangle logomark a single-fill equilateral triangle — never gradient/outline/rotate
- Render analytics in `#0070F3` on `#111111` with NO gridlines
- Use `Production` / `Preview` mono pills consistently
- Invert cleanly to pure white `#FFFFFF` in light mode

### Don't
- Don't use charcoal `#191919` for dark — Vercel is true `#000000`
- Don't add drop shadows to cards — they're invisible on black and off-brand; use borders
- Don't introduce a brand-accent CTA color — there is none; the white fill IS the primary
- Don't set URLs or hashes in the proportional Sans — machine strings are always Mono
- Don't gradient-fill or outline the triangle logomark
- Don't add gridlines, axis ticks, or chart ornament to Analytics
- Don't use the purple→cyan gradient anywhere except avatar placeholders / rare marketing accents
- Don't animate tab switches with slides — Vercel favors instant content swap
- Don't bold body prose by default — body is 400, only state labels/buttons are 600
- Don't use saturated fills for surfaces — surfaces are `#0A0A0A`/`#111111`, color is reserved for status

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Deployment card meta wraps tighter; URL truncates with middle ellipsis |
| iPhone 13/14/15 | 390pt | Standard single-column layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; compact top bar respects it |
| iPhone 15/16 Pro Max | 430pt | Slightly larger card padding (16pt); more meta visible inline |
| iPad (portrait) | 768pt | Content max-width ~720pt centered; analytics charts wider |
| iPad (landscape) | 1024pt+ | Deployments list may go 2-column; sidebar project switcher persistent |

### Dynamic Type
- Display, Title, Section, Body, Meta: full scale
- Tab labels, environment tags, mono captions (≤12pt), status badges: FIXED (layout-sensitive)
- Build-log Mono: scales up but stays monospace; line numbers stay aligned

### Orientation
- All screens support rotation
- iPad landscape adds a persistent left project list (280pt) + detail pane
- Charts re-flow to available width; no fixed aspect lock

### Touch Targets
- Status badge / env tag: display-only (not tappable)
- Deployment card: full-card tap → deployment detail, ≥64pt tall
- Tab bar icons: 21pt glyph, 44pt hit
- Icon buttons: 20pt glyph, 44pt hit
- Primary buttons: ≥40pt tall

### Safe Area Handling
- Top: compact top bar respects safe area + Dynamic Island
- Bottom: tab bar + home indicator respected; blurred bar floats over content
- Keyboard: env-var and search fields scroll above keyboard
- Sides: 16pt content inset on iPhone, centered container on iPad

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#000000` (TRUE black)
- Canvas (light): `#FFFFFF`
- Surface 1 (card): `#0A0A0A`
- Surface 2 (panel): `#111111`
- Divider/border: `#2E2E2E`
- Text primary: `#EDEDED` dark / `#000000` light
- Text secondary: `#A1A1A1`
- Text tertiary: `#707070`
- Primary button: `#EDEDED` fill, `#000000` text (dark)
- Link / blue button: `#0070F3`
- Ready (success): `#0CCE6B`
- Building (warning): `#F5A623`
- Error: `#E5484D`
- Avatar gradient: `#7928CA` → `#0070F3`

### Example Component Prompts
- "Build the Vercel deployments list in SwiftUI: a `ScrollView` of deployment cards on a pure-black `#000000` background. Each card: `#0A0A0A` fill, 1pt `#2E2E2E` border, 12pt radius, 14pt padding. Row 1: a 9pt status dot (green `#0CCE6B` Ready / amber `#F5A623` Building / red `#E5484D` Error) with a 3pt halo of the same color at 15% opacity, then the state label in Geist Sans 14pt w600 (colored if not Ready), then a right-aligned environment tag. Row 2: the deployment URL in Geist Mono 13pt `#EDEDED`. Row 3: a git-branch icon, the branch in Geist Mono 13pt `#A1A1A1`, and a right-aligned relative timestamp in `#707070`. 10pt gap between cards. NO shadows."

- "Create the Vercel environment tag: Geist Mono 10pt w500 `#A1A1A1`, background `#1A1A1A`, 1pt `#333333` border, 3pt vertical / 7pt horizontal padding, 5pt corner radius. Variants: `Production`, `Preview`, `Development`. Display-only, not tappable."

- "Render the Vercel primary button: background `#EDEDED`, text `#000000` Geist Sans 14pt w600, 8pt corner radius, 11pt vertical / 20pt horizontal padding. Pressed: background `#CFCFCF` + scale 0.98. There is no brand-accent color — this white fill IS the primary."

- "Build a Vercel status badge pill: inline-flex, a 7pt dot + label in Geist Mono 12pt w500, 999pt corner radius. Ready: text/dot `#0CCE6B`, border `rgba(12,206,107,0.4)`, bg `rgba(12,206,107,0.08)`. Building: `#F5A623`. Error: `#E5484D`."

- "Render the Vercel Analytics bar chart: bars filled with `#0070F3` (fading to 25% alpha at the base) on a `#111111` panel with 12pt radius, NO gridlines, NO axis ticks. Sparse axis labels in Geist Mono 11pt `#707070`. This sparseness is the Vercel signature — do not add chart ornament."

- "Build the Vercel build-log viewer: full-bleed `#000000`, Geist Mono 12pt, line numbers in `#707070` at the left gutter, ANSI colors mapped (errors `#E5484D`, success `#0CCE6B`, dim `#707070`). Auto-scroll to tail; a 'Jump to bottom' pill fades in when the user scrolls up."

- "Create the Vercel triangle logomark: a single-fill equilateral triangle, SVG path `M37.527 0L75.054 65H0z`, fill `#EDEDED` on dark / `#000000` on light. Never gradient, never outlined, never rotated."

### Iteration Guide
1. Canvas is **true black `#000000`** in dark — NOT charcoal `#191919`; light is pure `#FFFFFF`
2. One elevation step only: cards are `#0A0A0A` with a 1pt `#2E2E2E` border — never a shadow
3. The deployments list is the home screen — status dot + state + env tag + mono URL + git meta
4. The status traffic-light (`#0CCE6B` / `#F5A623` / `#E5484D`) is the ONLY systemic color
5. Every machine string (URL, branch, commit, env var, log) is Geist Mono; prose is Geist Sans
6. The primary button is a white fill (`#EDEDED` on black) — there is no brand-accent CTA
7. The triangle logomark is single-fill, never restyled
8. Analytics is `#0070F3` on `#111111` with zero gridlines — sparseness is the signature
9. `Production` / `Preview` mono pills are the most repeated chip — keep them consistent
10. Light mode is a clean inversion: `#FFFFFF` canvas, `#000000` text, same triangle
