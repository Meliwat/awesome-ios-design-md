# Design System Inspiration of Vimeo (iOS)

## 1. Visual Theme & Atmosphere

Vimeo's iOS app is a screening room, not a feed. Where consumer video apps fight for attention with bright chrome, autoplay walls, and infinite scroll, Vimeo does the opposite: it builds a near-black theatre (`#0D0E12`) and then gets out of the way so the *video itself* — and the human craft inside it — is the brightest thing on the screen. The entire design philosophy is "let the work breathe." Open any video and the 16:9 player is the hero: edge-to-edge or inset by 16pt, rounded at 14pt, with a single translucent play affordance dead-center and a minimal scrubber that fades away during playback. The UI is deliberately quiet because Vimeo's audience is filmmakers, motion designers, agencies, and studios who care about color fidelity and presentation — a loud interface would be an insult to the frame.

The signature emotional moment is the **Staff Pick**. Vimeo's human curation team has, for over a decade, hand-selected exceptional work, and that gold star is the most recognizable artifact in the brand. In the app it appears as a `#FFD24C` gold badge on thumbnails, a curated "Staff Picks" rail on Home, and a status line on creator profiles ("Staff · 12.4K followers"). It is the one place the app raises its voice — gold against ink — because curation *is* the product. The second signature moment is the **player + curated watch feed** pairing: a cinematic player on top, a vertically scrolling list of beautifully thumbnailed films below, each with creator attribution. This "watch, then discover more like it" rhythm is the core loop.

Color is restrained to the point of austerity. The canvas is a deep blue-black ink (`#0D0E12`), surfaces lift exactly one step (`#16181F`), and there is exactly one accent: **Vimeo Blue** (`#00ADEF`, with a legacy `#1AB7EA` still present on older surfaces and the wordmark). Blue is reserved — it appears only on the scrubber fill, the primary Upload CTA, the active tab, and follow actions. There is no secondary brand color competing for the eye; Staff Gold is the only other saturated hue and it means exactly one thing (curated). Text is pure white (`#FFFFFF`) at the top of the hierarchy and a cool blue-gray (`#AEB6C2`) for metadata, keeping chrome readable but visually subordinate to thumbnails. Typography is **Inter** — Vimeo's clean, neutral UI sans — sized for editorial calm: a 17–18pt video title, 13pt metadata, generous line height, never shouty.

**Key Characteristics:**
- Near-black ink canvas (`#0D0E12`) — a theatre that makes video glow; surfaces lift one step only (`#16181F`)
- Cinematic 16:9 player as the hero — 14pt corners, translucent center play button, auto-fading minimal scrubber
- Single accent — **Vimeo Blue** `#00ADEF` (legacy `#1AB7EA`) — scrubber fill, primary CTA, active tab, follow
- The **Staff Pick gold star** (`#FFD24C`) — the brand's most iconic artifact; the only other saturated color, means exactly "curated"
- Player + curated watch feed pairing — cinematic player on top, beautifully thumbnailed discovery list below
- Creator-forward attribution — avatar + name + follower count + Follow button on every video
- Inter typography sized for editorial calm — 17–18pt titles, 13pt metadata, generous leading
- Quiet chrome — minimal top bar (wordmark + search + notifications), 5-slot bottom tab with a center Upload action
- Tabular-figure runtimes and play counts — respect for precise, professional metadata
- Thumbnails never dimmed — only UI chrome recedes; the frame is sacred

## 2. Color Palette & Roles

### Primary (Interactive)
- **Vimeo Blue** (`#00ADEF`): The single interactive accent — primary CTA fill (Upload), player scrubber fill, active tab tint, Follow button, links.
- **Vimeo Blue Legacy** (`#1AB7EA`): The heritage brand blue — still used in the wordmark and older marketing surfaces; acceptable as a gradient partner.
- **Vimeo Blue Pressed** (`#008FC4`): Pressed/active state for blue fills.
- **On-Blue Ink** (`#04121A`): Text/glyph color placed on a Vimeo Blue fill (near-black for maximum contrast — Vimeo Blue is bright).

### Canvas & Surfaces (Dark — primary mode)
- **Ink Canvas** (`#0D0E12`): The primary app background — a deep blue-black theatre.
- **Surface 1** (`#16181F`): Cards, sheets, the elevated rows in feeds — exactly one step up.
- **Surface 2** (`#1F222B`): Nested surfaces, scrubber demo backgrounds, pressed list rows.
- **Divider** (`#262A34`): Hairline separators between sections and tab-bar top border.
- **Scrim** (`rgba(8,21,29,0.85)`): Gradient scrim under the player controls for legibility over any frame.

### Canvas & Surfaces (Light — secondary, rare)
Vimeo is dark-first; a light mode exists primarily for embedded/web parity.

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Canvas | `#FFFFFF` | `#0D0E12` |
| Surface 1 | `#F5F6F8` | `#16181F` |
| Surface 2 | `#ECEEF1` | `#1F222B` |
| Divider | `#E2E5EA` | `#262A34` |
| Text Primary | `#10131A` | `#FFFFFF` |
| Text Secondary | `#5A6472` | `#AEB6C2` |

### Text
- **Text Primary** (`#FFFFFF`): Video titles, screen titles, creator names — pure white, highest emphasis.
- **Text Secondary** (`#AEB6C2`): Body copy, descriptions, follower counts — a cool blue-gray.
- **Text Tertiary** (`#6B7280`): Metadata, play counts, timestamps, inactive tab labels — quiet.
- **Text on Blue** (`#04121A`): On Vimeo Blue fills.
- **Text on Gold** (`#04121A`): On the Staff Pick gold badge.

### Accent & Curation
- **Staff Gold** (`#FFD24C`): The Staff Pick badge / star — the brand's iconic curation mark. Used ONLY for curated picks. Never a generic warning or highlight.
- **Plus Purple** (`#8B5CF6`): Vimeo's premium/Plus tier and avatar gradient partner. Subscription upsell accents only.

### Semantic
- **Success** (`#2ECC71`): Upload-complete toast, "Saved" confirmation.
- **Error** (`#FF4D4F`): Upload failure, destructive confirm, validation.
- **Warning** (`#FFB020`): Encoding/processing notice (distinct from Staff Gold by being more amber).
- **Live Red** (`#FF2D55`): "LIVE" badge on Vimeo livestreams.

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (by Rasmus Andersson, 2020, SIL OFL) — a neutral humanist UI sans with excellent small-size hinting and tabular figures. Closest free Google Font to Vimeo's clean product type; bundle or load via Google Fonts.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Use tabular (monospaced) figures for all runtimes, timestamps, and play counts so the scrubber and metadata don't jitter.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Inter | 32pt | 800 | 1.15 | -0.6pt | Top-level tab name ("Watch") |
| Screen Title | Inter | 26pt | 700 | 1.2 | -0.4pt | "Staff Picks", section landing |
| Section Header | Inter | 22pt | 700 | 1.25 | -0.2pt | "From creators you follow" |
| Video Title | Inter | 18pt | 700 | 1.3 | -0.2pt | Title under the player |
| Video Title (Compact) | Inter | 17pt | 600 | 1.35 | -0.1pt | Card titles in the feed |
| Body | Inter | 15pt | 500 | 1.5 | 0pt | Video descriptions |
| Body (Reading) | Inter | 15pt | 400 | 1.6 | 0pt | Long descriptions, comments |
| Metadata | Inter | 13pt | 400 | 1.4 | 0pt | Plays · date · counts |
| Creator Name | Inter | 13pt | 600 | 1.4 | 0pt | Channel/creator label |
| Button | Inter | 15pt | 700 | 1.0 | 0pt | Primary/secondary buttons |
| Pill / Follow | Inter | 12pt | 700 | 1.0 | 0.1pt | Follow, chips |
| Overline | Inter | 11pt | 600 | 1.0 | 0.6pt | "STAFF PICKS" uppercase rail label |
| Runtime / Timecode | Inter | 10–11pt | 700 | 1.0 | 0pt | Tabular figures, on-scrim |
| Tab Label | Inter | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |

### Principles
- **Editorial calm, not UI shouting**: Titles top out at 18pt in context — the video is the headline, type supports it.
- **Tabular figures everywhere numeric**: Runtimes, scrubber timecodes, play counts must not shift width.
- **Weight, not size, for emphasis**: 600/700 carry hierarchy at small sizes; reserve 800 for the single Display.
- **Metadata recedes**: 13pt `#6B7280` keeps "21K plays · 4 comments" present but never competing with the thumbnail.
- **Dynamic Type**: Titles, body, descriptions scale; timecodes, tab labels, overlines, pill text stay fixed (layout-critical).

## 4. Component Stylings

### Buttons

**Primary Button (Upload / Confirm)**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#00ADEF` (Vimeo Blue)
- Text: `#04121A` (near-black on bright blue), Inter 15pt weight 700
- Padding: 13pt vertical, 26pt horizontal
- Pressed: background `#008FC4` + scale 0.98
- Disabled: `#00ADEF` at 38% opacity, text at 50%

**Secondary / Outline Button (Save to Watch Later)**
- Background: transparent
- Border: 1pt `#AEB6C2`
- Text: `#FFFFFF`, Inter 14pt weight 600
- Padding: 11pt vertical, 20pt horizontal
- Corner radius: 8pt
- Pressed: background `rgba(255,255,255,0.06)`

**Ghost / Surface Button (Share)**
- Background: `#1F222B`
- Border: 1pt `#262A34`
- Text: `#FFFFFF`, Inter 14pt weight 600
- Corner radius: 8pt
- Pressed: background `#262A34`

**Follow Pill**
- Background: `#00ADEF`
- Text: `#04121A`, Inter 12pt weight 700
- Padding: 7pt vertical, 16pt horizontal
- Corner radius: 999pt (full pill)
- Following state: background `#1F222B`, text `#FFFFFF`, 1pt `#262A34` border, label "Following"

**Text Button (See all staff picks)**
- Text: `#00ADEF`, Inter 14pt weight 600
- No background, no underline
- Pressed: 70% opacity

### Core Atoms

**Video Player**
- Aspect ratio: 16:9
- Corner radius: 14pt (inset player) / 0pt (full-bleed/landscape)
- Background while loading: linear gradient `#16384A → #08151D`
- Center play: 52pt circle, `rgba(13,14,18,0.55)` + 4pt blur, 1.5pt `rgba(255,255,255,0.55)` border, white triangle glyph offset +3pt
- Controls bar: 34pt tall, bottom-anchored, gradient scrim `rgba(8,21,29,0.85) → transparent`
- Scrubber: see Distinctive Components
- Quality chip: "HD" / "4K", 9pt weight 700 white, 1pt `rgba(255,255,255,0.5)` border, 3pt radius
- Tap player: toggles controls (fade 200ms); double-tap edges: ±10s seek

**Thumbnail**
- Aspect ratio: 16:9
- Corner radius: 8pt
- Duration chip: bottom-right, `rgba(0,0,0,0.7)` bg, white 9pt weight 700 tabular, 3pt radius, 1pt/4pt padding
- Staff Pick badge: top-left (see Distinctive Components)
- Never dimmed, never overlaid with heavy gradients except the duration chip

**Creator Avatar**
- Circle, 34pt default (28pt in lists, 44pt on profile header)
- Fallback: linear gradient `#00ADEF → #8B5CF6`
- Optional 1.5pt ring `#262A34`

**Card (Surface 1)**
- Background: `#16181F`
- Corner radius: 12pt
- No border in dark mode; 1pt `#262A34` only if on same-color background
- Internal padding: 12–14pt

### Navigation

**Top Bar**
- Height: 44pt + safe area
- Leading: Vimeo wordmark (`#FFFFFF`, ~96pt wide) — NOT centered, left-aligned
- Trailing: search glyph + notifications bell, 21pt, `#FFFFFF`, 1.8pt stroke
- Background: `#0D0E12` (transparent over player; solid on scroll)
- No bottom border on Home; hairline `#262A34` when content scrolls under it

**Bottom Tab Bar**
- Height: 52pt + safe area (68pt total visual)
- Background: `rgba(13,14,18,0.94)` + `saturate(180%) blur(20px)`
- Top border: 0.5pt `#262A34`
- 5 slots: Home, Search, **Upload (center, emphasized)**, Inbox, Profile
- Icon size: 22pt (Upload center action 26pt, filled Vimeo Blue)
- Active color: `#00ADEF` (icon fills); inactive: `#6B7280`
- Labels: Inter 10pt weight 500, always shown (Upload label hidden/blank — icon-only emphasis)

**Profile / Channel Header**
- 44pt avatar, creator name 22pt weight 700, "Staff · 12.4K followers" 13pt `#6B7280`
- Follow pill trailing
- Tab strip below: "Videos", "Showcases", "Likes" — underlined 2pt `#00ADEF` active

### Input Fields

**Search Field**
- Height: 44pt
- Background: `#16181F`
- Border: none (focus: 1pt `#00ADEF`)
- Corner radius: 10pt
- Leading magnifier 16pt `#6B7280`
- Placeholder: "Search videos, people" Inter 14pt `#6B7280`

**Comment Composer**
- Background: `#16181F`, 12pt radius, 1pt `#262A34`
- Text Inter 15pt `#FFFFFF`
- Trailing send glyph `#00ADEF` when non-empty, `#6B7280` when empty

### Distinctive Components

**Cinematic Player Scrubber**
- Track: 3–4pt tall, `rgba(255,255,255,0.22)` unfilled
- Fill: `#00ADEF`, left-anchored, width = progress
- Knob: 9–12pt white circle on the fill edge, soft shadow `0 1px 4px rgba(0,0,0,0.6)`
- Timecodes flank the track: elapsed (left) / total (right), Inter 10–11pt weight 600/700, white, tabular
- Scrubbing: knob enlarges to 16pt, a frame-preview thumbnail can float above the knob
- Idle: entire control bar fades out after 3s; tap restores over 200ms

**Staff Pick Badge**
- Pill form (rail/card): gold `#FFD24C` background, `#04121A` star glyph + "Staff Pick" Inter 10pt weight 700, 3/8pt padding, 999pt radius
- Mini form (thumbnail corner): 16pt gold circle with `#04121A` star glyph, top-left at 5pt inset
- This badge is sacred to the brand — gold is used for nothing else

**Staff Pick Card**
- Surface 2 (`#1F222B`) container, 12pt radius, clipped
- 16:9 thumbnail with the gold pill badge top-left
- Body: title Inter 14pt weight 600 `#FFFFFF`, meta "by Studio · 37K plays" Inter 11pt `#6B7280`
- Used in the curated rail and "Staff Picks" landing

**Curated Watch Feed Row**
- Horizontal layout: 124pt 16:9 thumbnail (left) + text block (right)
- Thumbnail carries the mini gold badge + duration chip
- Text: title 13pt weight 600, "by Creator" 11pt `#6B7280`, "21K plays · 4 comments" 11pt `#6B7280`
- 14pt vertical gap between rows

**Video Meta Block (under player)**
- Title Inter 18pt weight 700, "48K plays · 2 days ago" 13pt `#AEB6C2`
- Creator row: 34pt avatar + name (13pt weight 600) + "Staff · 12.4K followers" (11pt `#6B7280`) + Follow pill (trailing, pushed right)

**Like / Comment / Share Action Row**
- Icon + count, 21pt glyphs `#AEB6C2`, count Inter 13pt
- Liked state: heart fills `#00ADEF`
- Evenly spaced, no background

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 16, 20, 24, 32, 40, 48, 64
- Screen horizontal inset: 16pt
- Player inset: 16pt (or 0pt full-bleed/landscape)
- Feed row vertical gap: 14pt
- Section header to content: 12pt

### Grid & Container
- iPhone: single column, 16pt insets, full-width player
- Feed cards: full-width minus 16pt insets; thumbnail 16:9
- iPad: 2-column grid for staff-pick browsing; player max-width 900pt centered; landscape goes full-bleed cinema
- Profile grid: 2-up video thumbnails on phone, 3-up on iPad

### Whitespace Philosophy
- **The frame is the focus**: generous negative space around the player; chrome never crowds the video
- **One accent, used sparingly**: Vimeo Blue appears only on interactive elements — never decoratively
- **Gold is rationed**: Staff Gold appears only where curation is asserted, making it instantly meaningful
- **Editorial rhythm**: metadata sits quietly beneath titles with consistent 13pt `#6B7280`, never bold

### Border Radius Scale
- Square (0pt): full-bleed/landscape player, dividers
- Subtle (4pt): duration/quality chips, small tags
- Standard (8pt): thumbnails, primary/secondary buttons
- Card (12pt): cards, sheets, comment composer
- Player (14pt): the inset video player
- Comfortable (20pt): bottom sheets, modals
- Pill (500pt): Follow, chips, Staff Pick badge
- Circle (50%): avatars, the center play button

## 6. Depth & Elevation

Vimeo is cinematically flat. The screen is a dark theatre; the only real "depth" is the player's light against the ink, and translucent scrims that keep controls legible over any frame.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Feed rows, cards on ink, list content |
| Player Glow (Level 1) | The video itself is the brightest element; no cast shadow | The 16:9 player against `#0D0E12` |
| Control Scrim (Level 1.5) | Gradient `rgba(8,21,29,0.85) → transparent` | Behind player controls for legibility |
| Floating (Level 2) | `rgba(0,0,0,0.5) 0 8px 24px` | Context menus, frame-preview popover, toasts |
| Sheet (Level 3) | `rgba(0,0,0,0.6) 0 -6px 28px` + 20pt top radius | Share sheet, comment sheet, settings |
| Modal Scrim | `rgba(0,0,0,0.6)` | Dim behind sheets/modals |

**Shadow Philosophy**: In a near-black room, drop shadows barely register, so Vimeo uses *contrast and translucency* instead of elevation. Cards lift via a one-step lighter surface (`#16181F`), not a shadow. The player needs no shadow — it glows. Floating elements get a heavier `0.5–0.6` shadow plus a subtle `#262A34` hairline so they're distinguishable on dark.

### Motion
- **Controls fade**: player controls + scrubber fade out after 3s idle (250ms ease-out), fade in on tap (200ms)
- **Scrub**: knob scales 9pt → 16pt over 120ms on touch-down; frame preview pops above with 150ms fade
- **Tab switch**: cross-fade content 200ms; active tab icon tints to `#00ADEF` instantly (no morph)
- **Follow**: pill label cross-fades "Follow" → "Following" 180ms; background color animates 200ms
- **Sheet present**: bottom sheet slides up 300ms ease-out, scrim fades in parallel
- **Like**: heart scales 1.0 → 1.25 → 1.0 over 260ms spring, fills `#00ADEF`
- **Thumbnail load**: cross-fade from gradient placeholder to image over 220ms (never a hard pop)
- **Pull to refresh**: a subtle Vimeo-blue spinner; haptic tick on trigger
- **Haptics**: soft impact on Follow, on scrub-start, on Like; selection tick on tab change

## 7. Do's and Don'ts

### Do
- Use the near-black ink canvas (`#0D0E12`) — Vimeo is a screening room
- Make the 16:9 player the hero — 14pt corners inset, full-bleed in landscape
- Keep Vimeo Blue (`#00ADEF`) as the *only* interactive accent — scrubber, CTA, active tab, Follow
- Reserve Staff Gold (`#FFD24C`) exclusively for the Staff Pick badge — it must stay meaningful
- Attribute every video to a creator (avatar + name + follower count + Follow)
- Use tabular figures for all runtimes, timecodes, and play counts
- Let controls auto-fade during playback — the frame should be unobstructed
- Lift cards with a one-step surface (`#16181F`), not a drop shadow
- Keep metadata quiet — 13pt `#6B7280`, never bold, never colored
- Use Inter at editorial sizes — 17–18pt titles, generous line height

### Don't
- Don't use pure black (`#000000`) for the canvas — `#0D0E12` is the warm blue-black theatre
- Don't add a second brand accent — there is only Vimeo Blue (no orange, no green CTA)
- Don't reuse Staff Gold for warnings, highlights, or decoration — it means "curated", nothing else
- Don't dim or heavily gradient thumbnails — only the duration chip overlays the frame
- Don't shout with type — titles never exceed 18pt in context; the video is the headline
- Don't use heavy drop shadows on dark — they don't read; use surface lift + hairline
- Don't autoplay aggressively or stack autoplay walls — Vimeo respects the viewer
- Don't center the wordmark — it's left-aligned in the top bar
- Don't bold metadata or tint it blue — it stays `#6B7280`, subordinate to thumbnails
- Don't crowd the player — generous negative space; chrome never hugs the frame

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Player 16pt inset; feed thumbnail 110pt; tighter 13pt titles |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; player respects top inset |
| iPhone 15/16 Pro Max | 430pt | Larger thumbnails; player can go 18pt inset; 124pt feed thumbs |
| iPad (portrait) | 768pt | 2-column staff-pick grid; player max-width 760pt centered |
| iPad (landscape) | 1024pt+ | Cinema mode: full-bleed player; 3-column discovery grid alongside |

### Dynamic Type
- Scales: screen titles, video titles, body, descriptions, comments
- Fixed: timecodes, tab labels, overlines, duration/quality chips, pill text (layout-critical)
- Long titles wrap to 2 lines then truncate with ellipsis

### Orientation
- Portrait: inset 14pt-corner player + scrolling feed below
- Landscape: player goes full-bleed edge-to-edge cinema, controls overlay only; rotating mid-playback animates 300ms
- iPad supports Split View; player continues in PiP when navigating away

### Touch Targets
- Player center play: 52pt visual, 64pt hit
- Scrubber knob: 12pt visual, 44pt vertical hit band
- Tab bar items: 22pt glyph, 48pt hit
- Follow pill: 32pt tall minimum
- Like/Comment/Share: 44pt hit each

### Safe Area Handling
- Top: top bar + Dynamic Island respected; player can extend under status bar in landscape with safe-area-aware controls
- Bottom: tab bar + home indicator respected; sheets respect bottom inset
- Sides: 16pt content inset; full-bleed player ignores side insets in landscape with inset controls
- Keyboard: comment composer rises above keyboard; feed scrolls clear

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#0D0E12`
- Surface 1: `#16181F`
- Surface 2: `#1F222B`
- Divider: `#262A34`
- Text primary: `#FFFFFF`
- Text secondary: `#AEB6C2`
- Text tertiary / metadata: `#6B7280`
- Vimeo Blue (accent): `#00ADEF`
- Vimeo Blue legacy: `#1AB7EA`
- Vimeo Blue pressed: `#008FC4`
- On-blue ink: `#04121A`
- Staff Gold (curation only): `#FFD24C`
- Plus Purple: `#8B5CF6`
- Success: `#2ECC71` · Error: `#FF4D4F` · Live: `#FF2D55`

### Example Component Prompts
- "Build the Vimeo video player in SwiftUI: 16:9 aspect, 14pt corner radius, gradient placeholder `#16384A → #08151D` while loading. Centered 52pt play button: `rgba(13,14,18,0.55)` fill with 4pt blur, 1.5pt `rgba(255,255,255,0.55)` border, white play triangle offset x+3pt. Bottom controls bar 34pt tall with gradient scrim `rgba(8,21,29,0.85) → transparent`: elapsed timecode (Inter 11pt weight 700, white, tabular), a scrubber track (3pt, `rgba(255,255,255,0.22)` with `#00ADEF` fill and a 9pt white knob with `0 1px 4px rgba(0,0,0,0.6)` shadow on the fill edge), total timecode, and an 'HD' chip (9pt weight 700 white, 1pt `rgba(255,255,255,0.5)` border, 3pt radius). Controls fade out after 3s idle (250ms), fade in on tap (200ms)."

- "Create the Vimeo Staff Pick badge: a pill with `#FFD24C` background, a `#04121A` star glyph + 'Staff Pick' label in Inter 10pt weight 700, 3pt vertical / 8pt horizontal padding, 999pt corner radius. Provide a mini variant: a 16pt `#FFD24C` circle containing only the `#04121A` star, positioned top-left at 5pt inset on a 16:9 thumbnail."

- "Build a Vimeo curated watch-feed row: horizontal layout on `#0D0E12`. Left: a 124pt-wide 16:9 thumbnail, 8pt corners, with the mini gold Staff Pick circle top-left and a duration chip bottom-right (`rgba(0,0,0,0.7)` bg, white Inter 9pt weight 700 tabular, 3pt radius). Right: title in Inter 13pt weight 600 `#FFFFFF`, 'by Creator' in Inter 11pt `#6B7280`, and '21K plays · 4 comments' in Inter 11pt `#6B7280`. 14pt vertical gap between rows."

- "Create the Vimeo video meta block under the player: title in Inter 18pt weight 700 `#FFFFFF`, '48K plays · 2 days ago' in Inter 13pt `#AEB6C2`, then a creator row — 34pt circular avatar (gradient `#00ADEF → #8B5CF6` fallback), name in Inter 13pt weight 600 `#FFFFFF`, 'Staff · 12.4K followers' in Inter 11pt `#6B7280`, and a trailing Follow pill (`#00ADEF` bg, `#04121A` Inter 12pt weight 700 text, 7/16pt padding, 999pt radius) pushed to the right edge."

- "Build the Vimeo bottom tab bar: 52pt + safe area, background `rgba(13,14,18,0.94)` with `saturate(180%) blur(20px)`, 0.5pt `#262A34` top border. Five slots: Home, Search, Upload (center, emphasized — a 26pt filled `#00ADEF` play-in-circle, no label), Inbox, Profile. Inactive icons 22pt `#6B7280`; active icon fills `#00ADEF`. Labels Inter 10pt weight 500."

- "Create the Vimeo primary button: `#00ADEF` background, `#04121A` text in Inter 15pt weight 700, 13pt vertical / 26pt horizontal padding, 8pt corner radius. Pressed: background `#008FC4` + scale 0.98. Disabled: 38% opacity."

### Iteration Guide
1. Canvas is near-black ink `#0D0E12` — NOT pure black, NOT charcoal gray; it's a blue-black theatre
2. There is exactly ONE accent: Vimeo Blue `#00ADEF` (`#1AB7EA` legacy) — scrubber, CTA, active tab, Follow; nothing else is blue
3. Staff Gold `#FFD24C` is reserved for the Staff Pick badge ONLY — never a warning, never decoration
4. The 16:9 player is the hero — 14pt corners inset, full-bleed in landscape, translucent center play, auto-fading scrubber
5. Every video is creator-attributed: avatar + name + follower count + Follow pill
6. Use tabular figures for all runtimes, timecodes, and play counts so nothing jitters
7. Cards lift one step to `#16181F` — no drop shadows on dark; use surface + `#262A34` hairline
8. Type is editorial and quiet — Inter, titles 17–18pt, metadata 13pt `#6B7280`, never shouty
9. Thumbnails are sacred — never dimmed; only the duration chip overlays the frame
10. Chrome recedes — minimal left-aligned wordmark top bar, controls fade during playback
