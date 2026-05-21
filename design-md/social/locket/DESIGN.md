# Design System Inspiration of Locket (iOS)

## 1. Visual Theme & Atmosphere

Locket's iOS app is built around a single, radical idea: the app is not the destination — **the Home Screen widget is**. Locket Widget lets a tiny circle of your closest friends post a live photo directly to a square widget on your phone's Home Screen. You don't open an app to "check a feed"; a friend's face just appears on your home screen, intimately, like a locket photo you carry. Every design decision flows from this: the aesthetic is warm, cozy, analog-nostalgic, and tiny-circle-intimate — the opposite of a cold, public, infinite social network. The world is a soft **cream-and-gold** palette: a cream canvas (`#FFF7EC`) under a gentle peach gradient (`#FFE9CC → #FFD9A8`), with **Locket Gold** (`#FFB02E`) as the one warm brand accent. It is light-UI by design — there is no dark mode in spirit; the warmth IS the brand.

The signature surface, in-app, is the **camera capture screen**: a large **square, full-bleed friend photo** in a very rounded viewfinder (40pt corners), with the sender's name on a frosted chip ("from Ava") and an optional caption pill ("golden hour on the roof ✨") floating over the image. Below it sits the **big white circular capture button** — an 84pt white disc with a gold inner ring, flanked by a flip-camera and a small effects/flash control. This capture button is the most physical, recognizable interaction in the app: press it and the photo is instantly sent to your whole tiny friend circle and lands on their Home Screen widgets. The third signature is the **friends photo-history grid** — a tidy 4-column grid of square thumbnails of the recent photos your friends have sent, each with a small avatar dot, that you scroll back through like flipping a photo album.

Color is warm and singular. Locket Gold (`#FFB02E`) is the brand; a deeper amber (`#F08A1D`) is used for text/icons on cream where gold lacks contrast; a friendly **warm coral** (`#FF7A59`) is the only secondary accent (used sparingly for hearts/reactions and avatar gradients). White (`#FFFFFF`) cards and the capture button pop off the warm gradient. Text is a warm near-black brown (`#2C2014`) — never pure black, to keep the cozy feel — with a muted taupe (`#8A7A63`) for metadata. Shadows are **honey-tinted** (`rgba(180,120,30,…)`), never neutral gray, so depth feels warm. Typography is **Poppins** — a warm, rounded, geometric sans — set at friendly weights (600–800). Everything about Locket is small, warm, close, and personal: a few friends, one photo, on your Home Screen.

**Key Characteristics:**
- **Widget-first** — the real product surface is the Home Screen widget; the app is the camera + history behind it
- Warm cream-and-gold world — cream canvas (`#FFF7EC`), peach gradient (`#FFE9CC → #FFD9A8`); light-UI by design, no dark mode in spirit
- **Square full-bleed friend photo** in a very-rounded viewfinder (40pt corners) — the in-app hero
- Sender chip + caption pill — "from Ava" + optional caption floating over the photo on frosted glass
- **Big white circular capture button** — 84pt white disc with a gold inner ring; instant send to your tiny circle
- **Friends photo-history grid** — 4-up square thumbnails with avatar dots; scroll back like a photo album
- Single warm brand accent — Locket Gold (`#FFB02E`), with deep amber (`#F08A1D`) on cream and warm coral (`#FF7A59`) secondary
- Honey-tinted shadows (`rgba(180,120,30,…)`) — never gray; depth feels cozy
- Poppins typography at warm rounded weights (600–800)
- Tiny-circle intimacy — close-friends-only, no public profiles, no followers, no infinite feed

## 2. Color Palette & Roles

### Primary (Interactive)
- **Locket Gold** (`#FFB02E`): The single brand accent — primary CTA fill, capture-button inner ring, active tab, widget chrome.
- **Locket Amber** (`#FF9E2C`): Slightly deeper gold for gradient partners and hover/secondary fills.
- **Locket Deep** (`#F08A1D`): The on-cream accent — text links, active labels, "See all" — used where `#FFB02E` lacks contrast on cream.
- **Gold Pressed** (`#DC7A12`): Pressed state for gold buttons.
- **On-Gold Ink** (`#3A2400`): Text/glyph placed on a Locket Gold fill (warm dark brown, not black).

### Canvas & Surfaces
- **Cream Canvas** (`#FFF7EC`): The base background tone.
- **App Gradient** (`#FFE9CC → #FFD9A8`): The full-bleed warm peach world behind content (top → bottom).
- **Card White** (`#FFFFFF`): Pills, the capture button, info cards — pops off the warm gradient.
- **Surface Warm** (`#FFF1DD`): Secondary surfaces (pressed rows, sub-panels) — a soft tan.
- **Divider** (`#F2E2C9`): Hairline dividers on cream — a warm wheat.
- **Frosted Chip** (`rgba(44,32,20,0.32)` + blur): Sender/caption chips over photos — a warm dark glass, never gray.
- **Scrim** (`rgba(44,32,20,0.45)`): Dim behind modals/sheets — warm brown, never gray-black.

> Locket is light-and-warm by design. There is no separate dark theme; the cozy cream-gold identity *is* the brand. (If a system dark accommodation is ever required, deepen to a warm espresso `#1A1409` canvas keeping gold accents and white capture button — but product intent is always warm/light.)

### Text
- **Text Primary** (`#2C2014`): Sender names, titles, captions — a warm near-black brown (never pure black).
- **Text Secondary** (`#8A7A63`): Body, descriptions, metadata — a muted taupe.
- **Text Tertiary** (`#B8A88E`): Timestamps, inactive labels, hints — light warm gray.
- **Text on Gold** (`#3A2400`): On Locket Gold fills.
- **Text on Photo** (`#FFFFFF`): On the sender/caption frosted chips over a photo.

### Accent & Reactions
- **Warm Coral** (`#FF7A59`): The single secondary accent — reaction hearts, avatar gradient partner, occasional emphasis. Used sparingly.
- **Reaction Set**: Emoji reactions (❤️ 😂 🔥 😮) — rendered as emoji, not tinted shapes.

### Semantic
- **Success** (`#38C172`): "Sent!" confirmation toast.
- **Error** (`#FF5A5A`): Failed send, validation (a soft warm red, never harsh).
- **Warning** (`#F08A1D`): Reuses Locket Deep for soft warnings (palette discipline).
- **Online Dot** (`#38C172`): Friend-active indicator.

## 3. Typography Rules

### Font Family
- **Primary**: `Poppins` (by Indian Type Foundry / Jonny Pinhorn, SIL OFL) — a geometric sans with rounded, friendly proportions and even color. Its warm, approachable geometry matches Locket's cozy, personal feel. Closest free Google Font to Locket's rounded product type.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Rounded', 'SF Pro Display', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Weight philosophy**: Locket runs warm-medium — 600 (SemiBold) to 800 (ExtraBold) for emphasis, 500 for body. Avoid light weights; the friendly heft is part of the warmth.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Poppins | 32pt | 800 | 1.15 | -0.4pt | "Locket", big onboarding titles |
| Screen Title | Poppins | 26pt | 700 | 1.2 | -0.3pt | "From your friends", "History" |
| Section Header | Poppins | 22pt | 700 | 1.25 | -0.2pt | "Add to Home Screen" |
| Sender Name | Poppins | 18pt | 700 | 1.3 | -0.1pt | "from Ava" over the photo |
| Caption | Poppins | 16pt | 600 | 1.35 | 0pt | The optional caption on a photo |
| Body | Poppins | 15pt | 500 | 1.45 | 0pt | Card body, onboarding copy |
| Big Number | Poppins | 28pt | 800 | 1.0 | -0.5pt | Friend counts, streak counts |
| Metadata | Poppins | 13pt | 400 | 1.3 | 0pt | "2 minutes ago · sent to 5 friends" |
| Label | Poppins | 12pt | 700 | 1.0 | 0.4pt | "FROM YOUR FRIENDS" uppercase |
| Button | Poppins | 16pt | 700 | 1.0 | 0.2pt | Primary CTA |
| Pill | Poppins | 14pt | 700 | 1.0 | 0pt | Friends pill, soft buttons |
| Tab Label | Poppins | 10pt | 700 | 1.0 | 0.1pt | Bottom tab labels |
| Link / Action | Poppins | 13pt | 700 | 1.0 | 0pt | "See all ›", text links (`#F08A1D`) |

### Principles
- **Warm and friendly, never austere**: Poppins at 600–800 — rounded and cozy; avoid thin weights entirely.
- **Photo-first, type-supporting**: type frames the photo (sender, caption, time) — it never competes with the image.
- **Brown, not black**: primary text is `#2C2014` to preserve the warm, analog mood.
- **Emoji are first-class**: captions and reactions use emoji freely — they ARE the personal voice.
- **Dynamic Type**: sender names, captions, body scale; tab labels, uppercase labels, the capture button stay fixed (layout-critical).

## 4. Component Stylings

### Buttons

**Primary Button (Send to friends)**
- Shape: Full pill, 999pt corner radius
- Background: `#FFB02E` (Locket Gold)
- Text: `#3A2400` (warm dark brown), Poppins 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal
- Pressed: background `#F08A1D` + scale 0.97
- Shadow: `0 8px 20px rgba(180,120,30,0.30)` — a warm honey glow

**Soft Button (Add widget / secondary)**
- Background: `#FFFFFF`
- Text: `#F08A1D`, Poppins 15pt weight 700
- Corner radius: 999pt
- Shadow: `0 6px 16px rgba(180,120,30,0.18)` (white card lifts off the warm world)
- Pressed: background `#FFF1DD`

**Outline Button (Invite a friend)**
- Background: transparent
- Border: 2pt `#FFB02E`
- Text: `#F08A1D`, Poppins 15pt weight 700
- Corner radius: 999pt
- Pressed: background `rgba(255,176,46,0.12)`

**Text Button (See all photos)**
- Text: `#F08A1D`, Poppins 13–15pt weight 700
- Often paired with "›"
- No background; pressed: 70% opacity

### Core Atoms

**Capture Button (the signature interaction)**
- Outer: 84pt white circle (`#FFFFFF`) with a 5pt `rgba(255,255,255,0.55)` translucent border ring
- Inner: a 66pt circle with a 4pt `#FFB02E` gold ring (the ring is the recognizable mark)
- Shadow: `0 10px 26px rgba(180,120,30,0.32)`
- Press: scales to 0.92 then springs back; a quick gold ring pulse + shutter haptic on capture
- Centered in the capture row, flanked by 50pt translucent-white side controls (flip camera, flash/effects)

**Square Friend Photo / Viewfinder**
- Aspect ratio: 1:1 (always square — like a locket)
- Corner radius: 40pt (very round — soft and friendly)
- Full-bleed image; subtle inner radial warm glow
- Shadow: `0 24px 48px -22px rgba(160,100,20,0.55)`

**Sender Chip (over photo)**
- Frosted: `rgba(44,32,20,0.32)` + 6pt blur, 999pt radius
- Leading 24pt avatar circle + "from {name}" Poppins 13pt weight 700 white
- Top-left at 16pt inset

**Caption Pill (over photo)**
- Frosted: `rgba(44,32,20,0.36)` + 6pt blur, 16pt radius
- Caption text Poppins 15pt weight 600 white, centered
- Bottom-center at 18pt inset

**Friends Pill (top bar)**
- White (`#FFFFFF`) pill, soft honey shadow
- Overlapping avatar stack (22pt circles, 2pt white borders, -8pt overlap) + "{n} friends" Poppins 13pt weight 700 `#2C2014`

**History Cell**
- 1:1 square thumbnail, 14pt corner radius
- Small 14pt avatar dot bottom-left (who sent it)
- Tap → opens that photo full-screen with sender/caption

**Card (generic)**
- Background: `#FFFFFF`, 20–24pt radius
- Honey shadow `0 12px 26px -14px rgba(180,120,30,0.30)`
- Internal padding 18–20pt

### Navigation

**Top Bar**
- Height: 44pt + safe area, transparent over the gradient
- Leading: a 38pt white circle icon button (add friend)
- Center: the Friends pill (avatar stack + count)
- Trailing: a 38pt white circle icon button (messages/chat)
- No border (floats over the warm world)

**Bottom Tab Bar**
- Height: ~48pt + safe area (64pt visual), transparent over the gradient (no frosted bar — Locket keeps it minimal)
- 3 slots: **Camera** (default/center-of-attention), History, You
- Icon size: 24pt
- Active color: `#F08A1D` (icons fill); inactive: `#B8A88E`
- Labels: Poppins 10pt weight 700, always shown
- Camera is the home tab — the app opens straight to the viewfinder

**Widget (the real product surface — outside the app)**
- Square Home Screen widget (small/medium): full-bleed latest friend photo, 24pt system-rounded corners
- Optional tiny frosted "from {name}" chip bottom-left
- Tap → deep-links into the Locket camera

### Input Fields

**Caption Field (on capture)**
- A frosted pill over the photo before sending; tap to type
- Text Poppins 15pt weight 600 white, placeholder "Add a message…" at 70% white

**Friend Search / Invite**
- Background: `#FFFFFF`, 999pt pill, soft honey shadow
- Leading magnifier `#B8A88E`
- Placeholder: "Search contacts" Poppins 15pt weight 500 `#B8A88E`

### Distinctive Components

**Capture → Send Flow (the signature)**
- Square viewfinder → press the 84pt capture button → photo freezes with a quick gold ring pulse → optional caption pill → "Send to friends" gold pill → instant delivery to every friend's Home Screen widget
- Confirmation: a brief "Sent ✨" toast + success haptic; no feed to return to — you just go back to the viewfinder

**Friends Photo-History Grid**
- 4-column square grid, 8pt gaps, 14pt cell radius
- Each cell: a friend's photo + a small avatar dot (sender)
- Scrolls vertically like an album; tap any cell → full-screen with sender + caption + reactions

**Home Screen Widget Mock (in onboarding)**
- A 116pt rounded-24pt square showing a sample friend photo with a "from {name}" chip
- Used heavily in onboarding because the widget IS the product — "Add to Home Screen" is the core activation

**Reaction Bar (on a full-screen photo)**
- A frosted pill with emoji reactions (❤️ 😂 🔥 😮) + a quick "send a photo back" shortcut
- Tapping an emoji floats it up with a small bounce; warm coral accent for the heart

**Friends Pill / Tiny Circle**
- Locket caps your circle small — the pill (avatar stack + count) reinforces intimacy ("5 friends", not "5,000 followers")

**Streak / Moments (optional)**
- Lightweight warm streak counter for daily photo exchange — gold flame-ish, Poppins 28pt weight 800 number — but secondary to the core loop

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 18, 20, 24, 28, 40, 48
- Viewfinder side inset: 18pt; capture row vertical padding 24/14pt
- History grid gap: 8pt
- Sender chip inset: 16pt; caption pill inset: 18pt

### Grid & Container
- iPhone: single column — viewfinder (square) above, capture row, then a 4-up history strip
- History: strict 4-column square grid (album feel)
- iPad: viewfinder centered with a max-width (~480pt) — Locket stays phone-shaped and intimate; never a wide multi-pane
- One photo in focus at a time; history is the only scroll

### Whitespace Philosophy
- **The photo is everything**: the warm gradient is generous negative space framing one square photo
- **Cozy, not sparse**: warm tones make even empty space feel intimate, not cold
- **Minimal chrome**: transparent top/bottom bars, white pills floating — the photo and capture button dominate
- **Album rhythm**: the history grid is tidy and even, like flipping a physical photo book

### Border Radius Scale
- Square (0pt): rare — full-bleed gradient only
- Standard (8pt): tiny chips
- Comfortable (14pt): history cells, small panels
- Card (20–24pt): info cards, the Home Screen widget (24pt system)
- Pill (16pt): caption pill over photo
- Hero (40pt): the square friend photo / viewfinder — the signature very-round corner
- Full Pill (500pt): primary CTAs, friends pill, sender chip, search
- Circle (50%): the capture button, avatars, side controls

## 6. Depth & Elevation

Locket uses warm, soft depth — white elements float gently off the cream-gold world with honey-tinted shadows (never neutral gray), reinforcing the cozy, tactile feel.

| Level | Treatment | Use |
|-------|-----------|-----|
| World (Level 0) | The warm gradient, no shadow | App background |
| Photo (Level 1) | `rgba(160,100,20,0.55) 0 24px 48px -22px` | The square friend photo / viewfinder |
| Capture Button (Level 1.5) | `rgba(180,120,30,0.32) 0 10px 26px` | The 84pt white capture disc |
| White Pill / Card (Level 1) | `rgba(180,120,30,0.18–0.30) 0 6–12px 16–26px` | Friends pill, soft buttons, info cards |
| Sheet (Level 2) | `rgba(160,100,20,0.4) 0 -8px 28px` + 28pt top radius | Bottom sheets (share, invite) |
| Scrim | `rgba(44,32,20,0.45)` | Dim behind modals — warm brown, never gray |

**Shadow Philosophy**: Every shadow carries a *honey/amber tint* (`rgba(180,120,30,…)` or `rgba(160,100,20,…)`) rather than neutral black, so depth feels warm and analog — like sunlight, not a UI drop shadow. The square photo gets the deepest, softest shadow so it reads as a physical print you could hold. White pills lift gently off the gradient with a subtle warm glow.

### Motion
- **Capture press**: button scales 1.0 → 0.92 (90ms) then springs back; a gold ring pulse expands outward (250ms) + shutter haptic
- **Photo freeze → send**: captured photo does a tiny scale-down "snapshot" bounce; on send it lifts and fades upward (320ms ease-in) with a "Sent ✨" toast
- **Incoming photo**: the new friend photo cross-dissolves into the viewfinder/widget with a soft 280ms fade + gentle scale 1.03 → 1.0
- **History open**: tapped cell scales up into a full-screen photo (shared-element-style, 300ms spring)
- **Reaction**: tapped emoji bounces and floats up ~40pt with fade (500ms), warm coral heart pulse
- **Friends pill**: avatars do a subtle staggered pop-in when the circle updates
- **Tab switch**: warm cross-fade 220ms; active icon eases to `#F08A1D`
- **Widget refresh**: on the Home Screen, the photo updates with a gentle fade (system-paced)
- **Haptics**: a satisfying shutter impact on capture, soft tick on reaction, success haptic on "Sent", light tick on tab change

## 7. Do's and Don'ts

### Do
- Treat the **Home Screen widget as the primary product** — the app is the camera + history behind it
- Use the warm cream-gold world (`#FFF7EC` / `#FFE9CC → #FFD9A8`) — Locket is light & warm by design
- Make the friend photo a **square, full-bleed, very-rounded (40pt)** hero
- Use the big white circular capture button with a gold inner ring as the signature interaction
- Float the sender chip + optional caption on **warm frosted glass** over the photo
- Show a tidy 4-up friends photo-history grid — scroll it like an album
- Keep Locket Gold (`#FFB02E`) as the single brand accent (deep amber `#F08A1D` on cream, coral `#FF7A59` secondary)
- Use honey-tinted shadows (`rgba(180,120,30,…)`) — never neutral gray
- Use warm brown text (`#2C2014`) — never pure black
- Keep the circle tiny — close-friends-only; reinforce intimacy ("5 friends", not followers)

### Don't
- Don't ship a dark theme — Locket's identity is warm cream-gold; light by design
- Don't build a public feed or follower graph — it's a tiny private circle posting to your Home Screen
- Don't make the photo rectangular or sharp-cornered — it's a square with a very round (40pt) corner, like a locket
- Don't use neutral gray shadows — all elevation is honey/amber tinted
- Don't use pure black text — warm brown `#2C2014` keeps the cozy mood
- Don't add a second brand color beyond gold (+ the single warm coral secondary)
- Don't bury the "Add widget" activation — the widget IS the product; surface it prominently in onboarding
- Don't make it feel corporate, cold, or minimal-techy — Locket is warm, analog, and personal
- Don't add infinite scroll — only the history grid scrolls; the core is one photo at a time
- Don't crop a friend's photo aggressively — keep the square frame honest and full-bleed

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Viewfinder 36pt corners; capture button 76pt; 4-up history stays 4-up |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; top pills clear it |
| iPhone 15/16 Pro Max | 430pt | Larger viewfinder; 88pt capture button; more breathing room |
| iPad (portrait) | 768pt | Viewfinder centered, max-width ~480pt — phone-shaped & intimate, not a wide pane |
| iPad (landscape) | 1024pt+ | Same centered square viewfinder; warm gradient fills the rest |

### Dynamic Type
- Scales: sender names, captions, body, onboarding copy
- Fixed: tab labels, uppercase labels, the capture button, history-cell chrome (layout-critical)
- Long captions wrap to 2 lines on the pill then truncate

### Orientation
- Portrait-first (a quick one-handed camera app)
- Landscape supported for capture only; viewfinder stays square and centered
- The Home Screen widget is square in all cases (system-driven)

### Touch Targets
- Capture button: 84pt visual, ≥ 84pt hit (the primary action — generous)
- Side controls (flip/flash): 50pt circle, 50pt hit
- Top icon buttons: 38pt circle, 44pt hit
- History cells: full cell tappable
- Tab items: 24pt glyph, 48pt hit

### Safe Area Handling
- Top: friends pill + icon buttons respect the Dynamic Island
- Bottom: tab bar + home indicator respected; nothing crowds the capture button
- Sides: 18pt gutter from screen edge to the viewfinder
- The warm gradient extends fully edge-to-edge (it's the world)

## 9. Agent Prompt Guide

### Quick Color Reference
- App gradient: `#FFE9CC → #FFD9A8`
- Cream canvas: `#FFF7EC`
- Locket Gold (brand): `#FFB02E`
- Locket Amber: `#FF9E2C`
- Locket Deep (on-cream accent): `#F08A1D`
- Gold pressed: `#DC7A12`
- On-gold ink: `#3A2400`
- Warm Coral (secondary): `#FF7A59`
- Card white: `#FFFFFF`
- Surface warm: `#FFF1DD`
- Divider: `#F2E2C9`
- Text primary: `#2C2014` (warm brown, not black)
- Text secondary: `#8A7A63`
- Text tertiary: `#B8A88E`
- Frosted chip: `rgba(44,32,20,0.32)` + blur
- Success: `#38C172` · Error: `#FF5A5A`

### Example Component Prompts
- "Build the Locket capture button in SwiftUI: an 84pt white (`#FFFFFF`) circle with a 5pt `rgba(255,255,255,0.55)` translucent border; inside, a 66pt circle bordered with a 4pt `#FFB02E` gold ring. Apply a warm shadow `rgba(180,120,30,0.32)` blur 26 y 10. On press, scale to 0.92 then spring back, and on capture pulse an expanding gold ring outward over 250ms with a shutter haptic."

- "Create the Locket square viewfinder: a 1:1 aspect container with a 40pt corner radius, full-bleed friend photo, a subtle inner radial warm glow, and a soft shadow `rgba(160,100,20,0.55)` blur 48 y 24 spread -22. Overlay a top-left frosted sender chip: `rgba(44,32,20,0.32)` + 6pt blur, 999pt radius, a 24pt avatar circle + 'from {name}' in Poppins 13pt weight 700 white. Overlay a bottom-center caption pill: `rgba(44,32,20,0.36)` + 6pt blur, 16pt radius, caption in Poppins 15pt weight 600 white."

- "Build the Locket friends photo-history grid: a 4-column grid of 1:1 square cells, 8pt gaps, 14pt corner radius each, on the warm cream world. Each cell shows a friend's photo with a 14pt avatar dot bottom-left (sender). Tapping a cell scales it up into a full-screen photo (300ms spring) showing sender + caption + a frosted reaction bar."

- "Create the Locket primary button: a full pill (999pt) filled `#FFB02E`, text `#3A2400` in Poppins 16pt weight 700, 14pt vertical / 28pt horizontal padding, with a warm shadow `rgba(180,120,30,0.30)` blur 20 y 8. Pressed: background `#F08A1D` + scale 0.97."

- "Build the Locket friends pill for the top bar: a white (`#FFFFFF`) pill with a soft honey shadow `rgba(180,120,30,0.18)`, containing an overlapping avatar stack (22pt circles, 2pt white borders, -8pt overlap, gradient fills) and '{n} friends' in Poppins 13pt weight 700 `#2C2014`. 8pt vertical / 16pt horizontal padding, 999pt radius."

- "Create the Locket Home Screen widget mock for onboarding: a 116pt square with 24pt corner radius, a full-bleed sample friend photo, and a small bottom-left frosted 'from {name}' chip in Poppins 10pt weight 700 white. Add a warm shadow `rgba(160,100,20,0.40)` blur 26 y 12. Caption it 'The widget IS the product — tap it to open the camera.'"

### Iteration Guide
1. The **widget is the product** — design the Home Screen widget first; the app is the camera + history behind it
2. The world is warm cream-gold (`#FFF7EC`, `#FFE9CC → #FFD9A8`) — Locket is LIGHT & WARM by design; never a dark theme
3. The friend photo is a SQUARE, full-bleed image with a very round 40pt corner — like a locket, never rectangular/sharp
4. The signature interaction is the 84pt white circular capture button with a gold (`#FFB02E`) inner ring
5. Sender + caption float on WARM frosted glass (`rgba(44,32,20,…)`) over the photo — never gray glass
6. Locket Gold `#FFB02E` is the single brand accent (deep amber `#F08A1D` on cream, warm coral `#FF7A59` secondary only)
7. The friends photo-history is a tidy 4-up square grid scrolled like a photo album
8. All shadows are honey/amber tinted (`rgba(180,120,30,…)`) — never neutral gray
9. Text is warm brown `#2C2014`, never pure black; type is Poppins at warm 600–800 weights
10. Keep the circle TINY and private — close-friends-only, no public feed, no followers; intimacy over scale
