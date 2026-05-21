# Design System Inspiration of Postman (iOS)

## 1. Visual Theme & Atmosphere

Postman's iOS app is a developer instrument, not a consumer app — and it looks like one. It opens on a neutral dark grey canvas (`#1A1A1A`) and the entire screen is organized around a single task: build an HTTP request, fire it, read the response. The visual atmosphere is "API console in your pocket": dense, monospaced, color-coded, with one warm orange brand mark that appears only on the verbs that matter (Send, Save, primary CTAs). Nothing is decorative; every pixel either holds request data or signals state.

The spine of the app is the **request builder**: a horizontal URL bar with a colored HTTP-method pill on the left, the request URL in the middle (monospace), and a bright orange **Send** button on the right. Below it sits a tab strip — `Params`, `Headers`, `Body`, `Auth`, `Pre-request Script`, `Tests` — each opening a key-value editor where keys and values are typed in monospace. Fire the request and the bottom half of the screen becomes the **response viewer**: a status line (`200 OK` in green, `404` in yellow, `500` in red), timing and size metadata, and a syntax-highlighted JSON body. This request-then-response rhythm is the entire product; Collections, History, and Environments are just ways to organize and replay it.

The signature of the design is its **HTTP-method color system**. Every method has a fixed hue that is consistent everywhere the method appears — in the URL-bar pill, in collection request rows, in history entries: GET is green, POST is yellow, PUT is blue, PATCH is purple, DELETE is red, OPTIONS/HEAD are grey. This is not decoration — it is the primary information channel. A developer scanning a collection reads color before text. On dark mode these hues are softened (pastel-ish) versus the saturated light-mode versions, but the hue identity never changes.

The color logic is therefore split in two: a **single brand orange** (`#FF6C37`, "Postman Orange," from the company's logo and brand) reserved strictly for action affordances (Send button, active-tab underline, primary buttons, links) — and the **method/status palette** that carries semantic meaning. The neutral greys do everything else: canvas `#1A1A1A`, surfaces `#212121`/`#2A2A2A`, dividers `#3A3A3A`, text `#E8E8E8`/`#A6A6A6`. There is deliberately no second brand accent — orange means "do the thing," method colors mean "what kind of thing."

Typography is a clean UI sans (Postman ships a system-leaning stack; **Inter** is the closest free analog) for all chrome — labels, headings, navigation — paired with a strong monospace (**JetBrains Mono** as the free analog) for every piece of machine data: URLs, headers, JSON keys/values, query params, status codes, timing. The sans/mono split mirrors Postman's product reality: humans label things, machines speak JSON.

**Key Characteristics:**
- Neutral dark grey canvas (`#1A1A1A`) — a developer console, not a branded surface
- Request builder as the home screen — method pill + monospace URL + orange Send button
- HTTP-method color system — GET green / POST yellow / PUT blue / PATCH purple / DELETE red, consistent everywhere
- Single brand orange (`#FF6C37`) — reserved for Send / active tab / primary CTAs, never decorative
- Response viewer — status code (color-coded) + timing/size meta + syntax-highlighted JSON
- Sans for chrome, Mono for data — Inter labels, JetBrains Mono URLs/headers/JSON
- Tab strip request editor — Params / Headers / Body / Auth / Scripts / Tests with key-value rows
- Status-code chips — 2xx green, 3xx blue, 4xx yellow, 5xx red — pills in monospace
- Collections / History / Environments — three ways to organize and replay the same request
- JSON syntax coloring — keys blue, strings green, numbers/booleans purple, punctuation dim

## 2. Color Palette & Roles

### Primary (Interactive — the single brand accent)
- **Postman Orange** (`#FF6C37`): The one brand color — Send button, active-tab underline, primary buttons, links, FAB. Never decorative.
- **Postman Orange Pressed** (`#E55A2B`): Pressed state of orange affordances.
- **Postman Orange Dim** (`#3D2417`): 12% orange wash behind selected orange contexts (rare).

### Canvas & Surfaces (Dark — default)
- **Dark Canvas** (`#1A1A1A`): Primary canvas — neutral grey, not pure black, not charcoal-blue.
- **Surface 1** (`#212121`): Cards, URL bar, key-value cells, the first elevation step.
- **Surface 2** (`#2A2A2A`): Hovered/pressed rows, secondary panels.
- **Surface 3** (`#333333`): Inline code chips, tag fills.
- **Code Surface** (`#161616`): The response/JSON body background — slightly darker than canvas for contrast.
- **Divider** (`#3A3A3A`): Hairline separators between rows, under tab strips.
- **Border** (`#444444`): URL-bar border, input borders.

### Canvas & Surfaces (Light)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray** (`#F4F5F5`): Cards, key-value cells.
- **Surface Gray 2** (`#EBECEC`): Pressed rows.
- **Code Surface Light** (`#FAFAFA`): JSON body background.
- **Divider Light** (`#E1E3E3`): Hairline separators.
- **Border Light** (`#D6D8D8`): Input borders.

### Text
- **Text Primary Dark** (`#E8E8E8`): Primary text & headings on dark.
- **Text Secondary Dark** (`#A6A6A6`): Metadata, secondary labels.
- **Text Tertiary Dark** (`#6E6E6E`): Disabled, placeholders, JSON punctuation.
- **Text Primary Light** (`#212121`): Primary text on white.
- **Text Secondary Light** (`#6B6B6B`): Metadata on white.

### HTTP Method Colors (the semantic core — Dark Mode)
These are consistent everywhere a method appears. Light-mode versions are more saturated; hue identity is preserved.

| Method | Dark Mode | Light Mode | Chip Fill (dark, ~12%) |
|--------|-----------|-----------|------------------------|
| GET | `#6BDD9A` | `#0CBB52` | `rgba(107,221,154,0.12)` |
| POST | `#FFE47A` | `#C9A227` (text on chip) | `rgba(255,228,122,0.12)` |
| PUT | `#74AEF6` | `#1A73E8` | `rgba(116,174,246,0.12)` |
| PATCH | `#C0A8E1` | `#7D4FC4` | `rgba(192,168,225,0.12)` |
| DELETE | `#F79090` | `#E5484D` | `rgba(247,144,144,0.12)` |
| OPTIONS / HEAD | `#A6A6A6` | `#6B6B6B` | `rgba(166,166,166,0.12)` |

### Status Code Colors
| Class | Color (dark) | Use |
|-------|--------------|-----|
| 1xx / 2xx Success | `#6BDD9A` | `200 OK`, `201 Created` |
| 3xx Redirect | `#74AEF6` | `301 Moved`, `304 Not Modified` |
| 4xx Client Error | `#FFE47A` | `400`, `401`, `404` |
| 5xx Server Error | `#F79090` | `500`, `502`, `503` |

### JSON Syntax Colors
- **Key** (`#74AEF6`): Object keys.
- **String** (`#6BDD9A`): String values.
- **Number / Boolean / null** (`#C0A8E1`): Numeric, boolean, null literals.
- **Punctuation** (`#6E6E6E`): Braces, brackets, colons, commas.

### Semantic
- **Success** (`#6BDD9A`): 2xx responses, save success, passed tests.
- **Error** (`#F79090`): 5xx responses, failed tests, destructive actions.
- **Warning** (`#FFE47A`): 4xx responses, unsaved-change indicator.
- **Info** (`#74AEF6`): 3xx, informational toasts.

## 3. Typography Rules

### Font Family
- **Sans (UI / chrome)**: `Inter` (by Rasmus Andersson, SIL OFL) — the closest free analog to Postman's system-leaning UI face; neutral, legible at small sizes.
- **Mono (request data)**: `JetBrains Mono` (by JetBrains, SIL OFL) — strong developer monospace for URLs, headers, JSON, params, status codes, timing.
- **Fallback Sans Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Fallback Mono Stack**: `'SF Mono', ui-monospace, 'Menlo', 'Consolas', monospace`
- **Not user-switchable**: The sans/mono split is by content type (chrome vs machine data), not a setting.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Inter | 32pt | 800 | 1.1 | -0.5pt | Top-level screen title ("Collections") |
| Title | Inter | 26pt | 700 | 1.2 | -0.3pt | API / collection name |
| Section | Inter | 22pt | 700 | 1.2 | -0.2pt | "Request Builder" |
| Subsection | Inter | 18pt | 600 | 1.3 | -0.1pt | "Query Params", tab-content headings |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Descriptive prose, empty-state text |
| Label | Inter | 15pt | 500 | 1.3 | 0pt | Form labels, list-item titles |
| Method Pill | JetBrains Mono | 13pt | 700 | 1.0 | 0.3pt | `GET` / `POST` in URL bar & rows |
| URL / JSON | JetBrains Mono | 13pt | 400/500 | 1.4 | 0pt | Request URL, headers, key-value data |
| JSON Body | JetBrains Mono | 12pt | 400 | 1.6 | 0pt | Response body, pretty-printed |
| Status Code | JetBrains Mono | 12pt | 700 | 1.0 | 0.2pt | `200 OK`, status chips |
| Meta | Inter | 14pt | 400 | 1.4 | 0pt | "182 ms · 1.2 KB · 2m ago" |
| Caption | Inter | 12pt | 500 | 1.3 | 0.5pt | "QUERY PARAMS" eyebrow (uppercase) |
| Tab Label | Inter | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Button | Inter | 14pt | 700 | 1.0 | 0pt | All buttons (Send is 700) |

### Principles
- **Sans for chrome, Mono for machine data**: Any string that is part of an HTTP transaction — URL, header name/value, query param, JSON, status code, timing — is JetBrains Mono. Everything a human wrote — labels, headings, prose — is Inter. This split is the most legible part of the system.
- **Method pills are always Mono + bold**: `GET`/`POST`/`PUT` render in JetBrains Mono 13pt weight 700 with +0.3pt tracking and the method's fixed hue.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: Body 400, labels 500, subsections 600, headings/buttons 700, display 800. No thin.
- **JSON is colored, not bolded**: Syntax meaning is carried by hue (key blue / string green / number purple / punctuation dim), never by weight.
- **Uppercase only for tiny eyebrows**: Section eyebrows ("QUERY PARAMS") at 12pt +0.5pt tracking. Never uppercase body or data.
- **Tabular Mono figures**: timing/size columns and JSON numbers use JetBrains Mono's even-width digits so columns align.

## 4. Component Stylings

### Buttons

**Primary Button (Send / Save / Confirm)**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#FF6C37` (Postman Orange) — identical light & dark
- Text: `#FFFFFF`, Inter 14pt weight 700
- Padding: 12pt vertical, 22pt horizontal (Send in the URL bar is full-height of the bar)
- Pressed: background `#E55A2B` + scale 0.98
- Disabled: background `#3D2417`, text `#6E6E6E`

**Secondary / Ghost Button (Cancel / Reset)**
- Background: transparent
- Text: `#E8E8E8` dark / `#212121` light, Inter 14pt weight 500
- Border: 1pt `#444444` dark / `#D6D8D8` light
- Padding: 11pt vertical, 20pt horizontal
- Corner radius: 8pt
- Pressed: background `#2A2A2A`

**Outline Orange Button (Save Request / secondary action)**
- Background: transparent
- Text & border: `#FF6C37` 1pt
- Padding: 11pt vertical, 20pt horizontal, 8pt radius
- Pressed: background `rgba(255,108,55,0.10)`

**Text Button (Add to Collection / link action)**
- Background: none
- Text: `#FF6C37`, Inter 14pt weight 600
- No underline by default; underline on press

**Icon Button**
- 20pt glyph in 44pt hit area
- Color: `#A6A6A6` default → `#E8E8E8` active (orange if it's a primary action like Send)

### Core Atoms

**URL / Request Bar**
- Container: `#212121`, 1pt `#444444` border, 10pt corner radius, overflow hidden
- Method pill: left segment, JetBrains Mono 13pt 700, method-colored text, 1pt right divider, 12pt horizontal padding — tappable to open the method picker
- URL field: JetBrains Mono 12–13pt `#E8E8E8`, single-line, horizontal scroll
- Send button: right segment, full-height, `#FF6C37` fill, white Inter 13pt 700

**HTTP Method Chip**
- JetBrains Mono 13pt 700, method hue text, method-hue background at 12%, 6pt corner radius, 6×12pt padding
- Used in collection rows, history entries, request tabs

**Status Code Chip**
- JetBrains Mono 12pt 700, class-colored text + 1pt class-colored border at 40% + class fill at 8%, 999pt pill, 5×10pt padding
- 2xx green, 3xx blue, 4xx yellow, 5xx red

**Key-Value Row (Params / Headers editor)**
- Two cells side-by-side: key cell (flex ~38%, JetBrains Mono 12pt, key-blue `#74AEF6` text) + value cell (flex, JetBrains Mono 12pt `#E8E8E8`)
- Each cell: `#212121` fill, 1pt `#3A3A3A` border, 6pt radius, 9×10pt padding
- Leading checkbox (enable/disable row), trailing delete on swipe

**Response Body Block**
- Background `#161616`, 1pt `#3A3A3A`, 8pt radius, 12pt padding
- JetBrains Mono 12pt, syntax-colored JSON, line wrap or horizontal scroll toggle
- Header row: status chip + timing (Mono `#A6A6A6`) + size + copy/raw/preview toggle

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(26,26,26,0.94)` with `backdrop-filter: blur(20px)`, 0.5pt top border `#3A3A3A`
- Tabs (4): Request, Collections, History, Environments
- Icon size: 21pt, 2pt stroke
- Active: `#FF6C37` / Inactive: `#6E6E6E`
- Labels: Inter 10pt 500, always shown

**Request Editor Tab Strip**
- Horizontal scroll strip under the URL bar: Params / Headers / Body / Auth / Pre-request / Tests
- Inter 13pt; active 600 `#E8E8E8` with a 2pt `#FF6C37` underline; inactive 500 `#A6A6A6`
- A dot badge on a tab indicates it has content (e.g. Headers has 4 keys)

**Top Bar (Request)**
- Height: 44pt + safe area
- Leading: Postman roundel (26pt) + request name (Inter 16pt 600)
- Trailing: `Save` text action (Inter 13pt 600 `#FF6C37`, shows a dot when unsaved) + overflow `⋯`

### Input Fields

**Text Field**
- Height: 40pt, background `#212121` dark / `#F4F5F5` light, 1pt `#444444` border, 8pt radius
- Text: JetBrains Mono 13pt for URL/header/value fields; Inter 15pt for name/label fields
- Placeholder: `#6E6E6E`
- Focus: border `#FF6C37` + 2pt `rgba(255,108,55,0.35)` ring
- Error: border `#F79090`, helper text `#F79090` 12pt

**Method Picker (sheet)**
- Bottom sheet listing GET/POST/PUT/PATCH/DELETE/HEAD/OPTIONS
- Each row: method name in its hue (JetBrains Mono 15pt 700) + short description (Inter 12pt `#A6A6A6`)
- Selected row: `#2A2A2A` background, leading orange check

**Environment Variable Row**
- Key (Mono 13pt `#74AEF6`) + current value (Mono 13pt `#E8E8E8`), scope label (Inter 11pt `#A6A6A6`)
- `{{variable}}` tokens inside URL/body render in orange `#FF6C37` to show interpolation

**Search Field**
- Height: 38pt, leading magnifier 16pt `#6E6E6E`, `#212121`, 1pt `#3A3A3A`, 8pt radius
- Placeholder "Search collections" `#6E6E6E`

### Distinctive Components

**Method-Colored URL Bar**
- The atomic Postman element: a single pill+field+Send unit where the method's color is the first thing the eye reads.

**Request → Response Split**
- The screen is conceptually two halves: builder (top) and response (bottom). Sending animates the Send button into a spinner; the response slides up.

**HTTP Method Color System**
- The semantic core — see Color Palette. Consistent in URL bars, collection rows, history. Color is read before text.

**Syntax-Highlighted JSON Viewer**
- Pretty-printed, collapsible nodes (▸/▾), keys blue / strings green / numbers purple / punctuation dim. Copy + Raw + Preview toggle.

**Collection Tree Row**
- Folder/request hierarchy: leading method chip (for requests) or folder glyph, request name (Inter 15pt), trailing run/⋯. Nested rows indent 16pt per level.

**Status Code Chip + Timing**
- After a send: `200 OK` (green pill) · `182 ms` · `1.2 KB`, all JetBrains Mono — the developer's instant verdict.

**Environment Pill**
- A pill in the top area showing the active environment (e.g. `Staging`); tap to switch. `{{vars}}` interpolate against it.

**Tests / Assertions Result List**
- Each assertion row: green check + name (passed) or red ✕ + name + failure detail; counts summarized "5 passed, 1 failed".

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 6, 8, 10, 12, 14, 16, 20, 24, 28, 32
- URL bar internal segment padding: 12pt; key-value row gap: 8pt; section gap: 12–16pt
- Screen horizontal inset: 14–18pt

### Grid & Container
- iPhone: single column; the screen splits vertically into builder (top ~55%) and response (bottom ~45%) after a send
- iPad: optional 2-pane — collection sidebar (left ~300pt) + request/response detail (right)
- Key-value editors use a fixed 2-column proportion (key ~38% / value flex)

### Whitespace Philosophy
- **Dense but legible**: this is a power tool — rows are compact, but 8pt gaps and 1pt dividers keep the JSON and key-value grids scannable
- **Color carries hierarchy**: method/status colors do work that whitespace would in a consumer app — the eye navigates by hue
- **Code gets its own surface**: response JSON sits on a darker `#161616` block so it reads as "output," distinct from the chrome
- **Orange is rationed**: a single orange affordance per context keeps "the action" unambiguous

### Border Radius Scale
- Square (0pt): dividers, full-bleed code panes
- Subtle (4pt): inline code chips
- Chip (6pt): method chips, key-value cells
- Standard (8pt): buttons, inputs, response block
- Bar (10pt): the URL/request bar
- Comfortable (16pt): sheets, modals, large containers
- Pill (500pt): status-code chips, environment pill
- Circle (50%): the Postman roundel, avatars

## 6. Depth & Elevation

Postman is mostly flat with restrained elevation — it's a console, not a card-based consumer app.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No border, no shadow | Canvas, JSON code pane |
| Bordered (Level 1) | 1pt `#3A3A3A`/`#444444`, bg `#212121` | URL bar, key-value cells, inputs, cards |
| Floating (Level 2) | `rgba(0,0,0,0.5) 0 8px 24px` + 1pt `#444444` | Method picker, environment switcher, dropdowns, bottom sheets |
| Modal Overlay | `rgba(0,0,0,0.6)` scrim | Behind sheets and destructive-confirm dialogs |

**Shadow Philosophy**: On the neutral grey canvas, separation is primarily a 1pt border + a slightly lighter surface; shadows are reserved for genuinely floating layers (the method picker, environment switcher, bottom sheets) so they read as "above" the request. The response code block goes the other way — it's *darker* than the canvas, signaling "machine output" rather than "elevated UI." This inversion (data sinks, controls float) is a Postman tell.

### Motion
- **Send → response**: the Send button label cross-fades into a 14pt spinner (orange) for the request duration; on completion the response section slides up 220ms ease-out and the status chip pops (scale 0.9→1.0)
- **Method picker**: bottom sheet slides up 280ms ease-out; selecting a method animates the URL-bar pill's color with a 180ms cross-fade
- **Tab strip switch**: the `#FF6C37` underline slides horizontally to the new tab over 200ms ease-out; content cross-fades
- **JSON node collapse**: chevron rotates 90° in 150ms; children height 0→auto over 180ms ease-out
- **Unsaved indicator**: the `Save` action's dot fades in (120ms) when the request is modified
- **Pull-to-refresh (History/Collections)**: native rubber-band; an orange progress arc
- **Test results**: each assertion row staggers in (40ms apart), green checks / red ✕ with a soft scale-in
- **Haptics**: light impact on Send tap; success notification on 2xx, error notification on 4xx/5xx; selection tick on method change and tab switch

## 7. Do's and Don'ts

### Do
- Use the neutral grey canvas `#1A1A1A` (dark) / `#FFFFFF` (light) — a console, not a branded surface
- Make the request builder the home screen — method pill + monospace URL + orange Send
- Apply the HTTP-method color system consistently (GET green / POST yellow / PUT blue / PATCH purple / DELETE red) everywhere a method appears
- Reserve Postman Orange `#FF6C37` strictly for actions (Send / active tab / primary CTAs)
- Set every URL, header, param, JSON, status code, and timing in JetBrains Mono
- Color JSON by syntax role (key blue / string green / number purple / punctuation dim)
- Use status-code chips (2xx green / 3xx blue / 4xx yellow / 5xx red) as pills
- Put the response JSON on a darker `#161616` surface so it reads as machine output
- Show the active environment as a pill and render `{{variables}}` in orange
- Soften method hues on dark vs light but keep their hue identity

### Don't
- Don't use orange decoratively — it means "do the thing," nothing else
- Don't introduce a second brand accent — method colors carry semantics, orange carries action
- Don't set URLs/headers/JSON in the proportional sans — machine data is always Mono
- Don't change a method's hue between screens — GET is always the same green everywhere
- Don't bold JSON for emphasis — syntax meaning is hue, not weight
- Don't use a pure-black or charcoal-blue canvas — Postman dark is neutral grey `#1A1A1A`
- Don't elevate the response code block — data sinks (darker), controls float (lighter)
- Don't uppercase body or data — only tiny section eyebrows
- Don't drop the timing/size meta after a send — `ms` + size is the developer's verdict
- Don't animate aggressively — motion is functional (slide/cross-fade, 150–280ms)

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | URL truncates earlier; response pane shorter; key cell ~40% |
| iPhone 13/14/15 | 390pt | Standard single-column builder/response split |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in the compact top bar |
| iPhone 15/16 Pro Max | 430pt | More URL visible inline; taller response pane |
| iPad (portrait) | 768pt | Optional collection sidebar (~300pt) + detail |
| iPad (landscape) | 1024pt+ | 3-pane: collections, request builder, response side-by-side |

### Dynamic Type
- Display, Title, Section, Body, Label, Meta: full scale
- Method pills, status chips, tab labels, JSON body, ≤12pt mono: FIXED (column-alignment-sensitive)
- URL/header Mono: scales up but stays monospace; columns stay aligned

### Orientation
- All screens rotate
- iPhone landscape widens the URL bar and shows builder + response side-by-side
- iPad landscape adds the 3-pane console layout

### Touch Targets
- Method pill: tappable, ≥40pt tall (full URL-bar height)
- Send button: ≥44pt tall
- Key-value cells: ≥40pt tall, full-cell tap to edit
- Tab strip items: ≥44pt hit, 13pt label
- Tab bar icons: 21pt glyph, 44pt hit

### Safe Area Handling
- Top: compact top bar respects safe area + Dynamic Island
- Bottom: tab bar + home indicator respected; blurred bar floats over content
- Keyboard: the active key-value editor and URL field scroll above the keyboard; the response pane collapses to make room
- Sides: 14pt content inset on iPhone, paned layout on iPad

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#1A1A1A` (neutral grey) / light `#FFFFFF`
- Surface 1 (card/URL bar): `#212121`
- Code surface (JSON body): `#161616`
- Divider / border: `#3A3A3A` / `#444444`
- Text primary: `#E8E8E8` dark / `#212121` light
- Text secondary: `#A6A6A6`
- Brand orange (Send / active / CTA): `#FF6C37`
- GET: `#6BDD9A` · POST: `#FFE47A` · PUT: `#74AEF6` · PATCH: `#C0A8E1` · DELETE: `#F79090`
- Status 2xx: `#6BDD9A` · 3xx: `#74AEF6` · 4xx: `#FFE47A` · 5xx: `#F79090`
- JSON key `#74AEF6` · string `#6BDD9A` · number `#C0A8E1` · punctuation `#6E6E6E`

### Example Component Prompts
- "Build the Postman URL/request bar in SwiftUI: a single rounded container (`#212121` fill, 1pt `#444444` border, 10pt radius). Left segment: the HTTP method in JetBrains Mono 13pt w700 colored by method (GET `#6BDD9A`, POST `#FFE47A`, PUT `#74AEF6`, PATCH `#C0A8E1`, DELETE `#F79090`) with a 1pt right divider and 12pt padding — tappable to open the method picker. Middle: the URL in JetBrains Mono 12pt `#E8E8E8`, single-line, horizontal scroll. Right segment: a full-height `#FF6C37` Send button, white Inter 13pt w700."

- "Create a Postman HTTP method chip: JetBrains Mono 13pt w700, text in the method's hue, background that hue at 12% alpha, 6pt corner radius, 6pt vertical / 12pt horizontal padding. GET=`#6BDD9A`, POST=`#FFE47A`, PUT=`#74AEF6`, PATCH=`#C0A8E1`, DELETE=`#F79090`, OPTIONS/HEAD=`#A6A6A6`. This color is consistent everywhere the method appears."

- "Render the Postman response viewer: a header row with a status-code pill (JetBrains Mono 12pt w700; 2xx text/border `#6BDD9A`, 4xx `#FFE47A`, 5xx `#F79090`; 999pt radius, class color at 8% fill + 40% border), then timing `182 ms` and size `1.2 KB` in JetBrains Mono `#A6A6A6`. Below: the JSON body on a `#161616` block, 1pt `#3A3A3A`, 8pt radius, JetBrains Mono 12pt with syntax colors — keys `#74AEF6`, strings `#6BDD9A`, numbers/booleans `#C0A8E1`, punctuation `#6E6E6E`."

- "Build the Postman key-value editor row: two cells side-by-side. Key cell flex 38%, JetBrains Mono 12pt `#74AEF6`. Value cell flex 1, JetBrains Mono 12pt `#E8E8E8`. Each cell `#212121` fill, 1pt `#3A3A3A` border, 6pt radius, 9pt vertical / 10pt horizontal padding, 8pt gap between cells. Leading enable checkbox, swipe-to-delete."

- "Create the Postman primary Send button: `#FF6C37` background, white Inter 14pt w700, 8pt corner radius, 12pt vertical / 22pt horizontal padding. Pressed: `#E55A2B` + scale 0.98. On tap, cross-fade the label into a 14pt white spinner for the request duration. Orange is reserved for actions only — never decorative."

- "Render the Postman request editor tab strip: a horizontal scroll row of Params / Headers / Body / Auth / Pre-request / Tests in Inter 13pt. Active tab: w600 `#E8E8E8` with a 2pt `#FF6C37` underline that slides to the selected tab over 200ms. Inactive: w500 `#A6A6A6`. A small dot on a tab signals it has content."

### Iteration Guide
1. Canvas is neutral grey `#1A1A1A` (dark) / `#FFFFFF` (light) — a developer console, not a branded surface
2. The request builder is the home screen — method pill + monospace URL + orange Send button
3. The HTTP-method color system (GET green / POST yellow / PUT blue / PATCH purple / DELETE red) is the semantic core — consistent everywhere, read before text
4. Postman Orange `#FF6C37` is the ONLY brand accent and is reserved for actions (Send / active tab / primary CTAs) — never decorative
5. Every URL, header, param, JSON, status code, and timing is JetBrains Mono; chrome is Inter
6. JSON is colored by syntax role (key blue / string green / number purple / punctuation dim), never bolded
7. Status-code chips are pills: 2xx green, 3xx blue, 4xx yellow, 5xx red
8. The response JSON sits on a darker `#161616` block — data sinks, controls float (the depth inversion is a Postman tell)
9. Show the active environment as a pill; render `{{variables}}` in orange to signal interpolation
10. Method hues soften on dark vs the saturated light versions, but hue identity never changes
