# Design System Inspiration of Snapchat (iOS)

## 1. Visual Theme & Atmosphere

Snapchat's iOS app is a camera that happens to have messaging, maps, and a video feed attached — not the other way around. The app opens directly into the live camera viewfinder, full-screen, with the lens capturing your face before you've even decided what to do. This is the single most defining characteristic of Snapchat's design philosophy: the camera IS the home screen. Every other app in this category (X, Threads, Facebook, Instagram) opens to a feed; Snapchat opens to a viewfinder. The result is an app whose design language is built around photography — edge-to-edge imagery, floating chromeless controls, and a yellow accent so saturated it functions as a signal flare.

Snap Yellow (`#FFFC00`) is the most aggressive accent color in any major social app. It's a pure primary hue — not a branded shade with hex poetry behind it, but a near-max-brightness yellow that pops off a black canvas like a hazard sign. It appears only on brand moments: the ghost logo, the snap-capture button (a 82pt yellow circle surrounded by a yellow ring), the Snapstreak 🔥 badges, and certain navigation pills. The rest of the UI is monochrome white-on-dark or white-on-photo, with specific color codes reserved for the Snap type system (red = photo snap, purple = video snap, blue = chat message, green = audio) so users can glance at their inbox and instantly parse conversation types.

The navigation model is unlike any other app: instead of a bottom tab bar, Snapchat uses a horizontal swipe-between-four-screens metaphor, where Map, Chat, Camera (center, default), Stories (Discover), and Spotlight each occupy their own full-screen canvas. The user moves between them by swiping left/right from the camera. A small tab bar at the bottom exists, but acts more as a progress indicator showing your current screen than a persistent navigation dock. Typography is Avenir Next — a humanist geometric sans with warm curves and a distinctive lowercase `a` — at Bold and Medium weights almost exclusively, creating a friendly, young-feeling type voice that contrasts against the app's dramatic photography. Bitmoji characters (user's personalized cartoon avatar) replace profile photos throughout — your bitmoji waves from the chat avatar, sleeps on the Snap Map, and features in every UI moment where other apps would use a profile photo.

**Key Characteristics:**
- Camera-first home screen — app opens directly to the live viewfinder full-screen
- Snap Yellow (`#FFFC00`) as the aggressive primary accent — ghost logo, capture button, streak flame
- Swipe-between-five-screens navigation — Map | Chat | Camera | Discover | Spotlight (no traditional tab bar)
- Color-coded snap types — Red photo, Purple video, Blue chat, Green audio (in the Chat inbox)
- 82pt yellow capture button with concentric yellow ring — the signature center-screen CTA
- Bitmoji integration — user's custom cartoon avatar replaces profile photos app-wide
- Stories ring — pulsing yellow ring around unread story avatars
- Friend emojis — 🔥 streak, 💛 best friends, 👑 super BFF, 😎 mutual best friends, 🎂 birthday
- Ghost logo — the flat cartoon ghost (Ghostface Chillah) as the brand mark, used on splash and loading
- Floating chromeless controls on the camera — no surrounding UI frame, just icons in empty corners
- Avenir Next font at Bold/Medium weights — warm humanist sans, no thin weights
- Chat auto-delete — the signature "ephemeral by default" feel where messages disappear on close

## 2. Color Palette & Roles

### Primary Brand
- **Snap Yellow** (`#FFFC00`): The only brand accent. Ghost logo background, capture button fill, streak flame, unread story ring pulse, Snap-added-me badges. Saturated and aggressive.
- **Yellow Pressed** (`#E6E300`): Pressed state on yellow interactive elements (very rare — most yellow is logo/capture).
- **Pure Black** (`#000000`): Primary canvas for Chat, Map UI chrome, Discover background, loading screens.
- **Pure White** (`#FFFFFF`): Primary text on dark surfaces, chromeless icons floating on camera viewfinder, capture-button inner ring fill (when not active).

### Canvas & Surfaces (Dark / Default)
- **Canvas Black** (`#000000`): Primary canvas across Chat, Stories (Discover chrome), Spotlight.
- **Surface 1** (`#1A1A1A`): Chat bubbles (received), Discover card backgrounds, bottom-sheet modals.
- **Surface 2** (`#2C2C2C`): Elevated surfaces — selected chat row, settings cells.
- **Divider** (`#333333`): Hairlines between chat rows, settings sections.
- **Camera Viewfinder**: full-bleed live-camera feed (any colors from the real world)

### Canvas & Surfaces (Light — limited use)
Snapchat is effectively dark/camera-default. A light canvas appears only inside certain settings screens and Memories grid.
- **Light Canvas** (`#FFFFFF`)
- **Light Surface 1** (`#F2F2F2`)
- **Light Text** (`#000000`)

### Text
- **Text Primary Dark** (`#FFFFFF`): Primary text on dark and on camera overlays.
- **Text Primary Light** (`#000000`): Primary text on light surfaces (settings, memories).
- **Text Secondary** (`#8A8A8F`): Timestamps, status ("Tap to reply", "Opened 2m ago"), metadata.
- **Text Tertiary** (`#555555`): Disabled / very low-emphasis.

### Snap Type Colors (Color-Coded Chat Inbox)
Each Snap type has a distinct icon color — this is Snapchat's signature inbox-at-a-glance signal.
- **Photo Snap Red** (`#FF2E3D`): Received photo snap icon in inbox (unread square with triangle point).
- **Video Snap Purple** (`#9B51FF`): Received video snap icon.
- **Chat Blue** (`#4DA7FF`): Text chat message icon.
- **Audio Green** (`#4CD964`): Audio note / voice message icon.
- **Opened State (empty outline)**: Once opened, the filled square becomes an outline of the same color — signaling "already viewed".

### Semantic
- **Error Red** (`#FF3B30`): Destructive actions, failed sends, "Clear streak" warnings.
- **Success Green** (`#00D873`): Snap sent confirmation, "Streak saved" toast.
- **Live Red** (`#FF2E3D`): Live Story red dot, Snap Map live event marker.
- **Blue Link** (`#4DA7FF`): Inline links in chat (same blue as Chat type).

### Story Ring Colors
- **Unread Story Ring** (`#FFFC00`): Snap Yellow with a subtle animated pulse around unread Stories on Discover page.
- **Read Story Ring** (`#555555`): Gray ring around viewed Stories.
- **Live Story Ring** (`#FF2E3D`): Red ring for Live Stories (Spotlight or partnered live events).

### Map Accent
- **Snap Map Zones**: Heat-map coloring on Snap Map — warmer zones (dense snaps) render in orange `#FF9A1F` / red `#FF2E3D`; cool zones in yellow `#FFFC00`.

## 3. Typography Rules

### Font Family
- **Primary**: `Avenir Next` (by Adrian Frutiger, published by Linotype) — a humanist geometric sans with warm curves and a distinctive two-story lowercase `a`. Snapchat ships Avenir Next Bold and Avenir Next Medium as the primary UI faces.
- **Display / Branded Moments**: `Avenir Next Heavy` for the largest type (splash, streak counts).
- **Fallback Stack**: `'Avenir Next', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **CJK / Non-Latin**: Apple system fonts handle CJK; Avenir Next has Latin-plus-extended coverage.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Splash Title | Avenir Next Heavy | 48pt | 900 | 1.0 | -0.5pt | "Snapchat" wordmark on launch |
| Screen Title | Avenir Next Bold | 24pt | 700 | 1.15 | -0.2pt | "Chat", "Stories", "My Profile" |
| Section Header | Avenir Next Bold | 20pt | 700 | 1.2 | -0.1pt | "Best Friends", "My Story", "For You" |
| Chat Row Name | Avenir Next Bold | 16pt | 700 | 1.25 | 0pt | Friend name in inbox row |
| Chat Message | Avenir Next Medium | 16pt | 500 | 1.3 | 0pt | Chat bubble text |
| Chat Status | Avenir Next Medium | 13pt | 500 | 1.25 | 0pt | "Received · 2m ago", "Tap to reply" — color `#8A8A8F` |
| Story Name | Avenir Next Bold | 14pt | 700 | 1.2 | 0pt | Friend name under story thumbnail |
| Timestamp | Avenir Next Medium | 12pt | 500 | 1.2 | 0pt | "2m", "1h", "Yesterday" |
| Streak Count | Avenir Next Bold | 18pt | 700 | 1.0 | 0pt | Number beside 🔥 on friend row |
| Camera HUD Label | Avenir Next Bold | 13pt | 700 | 1.0 | 0.3pt | "NEW CHAT", "MEMORIES" — tracked uppercase |
| Primary Button | Avenir Next Bold | 16pt | 700 | 1.0 | 0.2pt | "Send To", "Add", "Save Story" |
| Lens Label | Avenir Next Bold | 13pt | 700 | 1.0 | 0.3pt | Under camera lens carousel — tracked small caps |
| Bitmoji Callout | Avenir Next Medium | 14pt | 500 | 1.3 | 0pt | Bitmoji caption bubbles on chat |
| Spotlight Caption | Avenir Next Medium | 14pt | 500 | 1.3 | 0pt | Creator username + caption overlay on video |
| Discover Tile Title | Avenir Next Bold | 16pt | 700 | 1.2 | -0.1pt | Story tile name "Snapchat News", "NBA" |

### Principles
- **Two weights only**: Avenir Next Bold (700) and Medium (500) carry nearly everything. Heavy (900) only on splash / streak hero. No regular, no light, no thin.
- **Tracked caps for HUD moments**: Camera HUD labels ("MEMORIES", "NEW CHAT") use uppercase with +0.3pt tracking — evokes the "capture" feeling
- **Text as overlay on imagery**: Camera HUD type sits on live photo/video, so Snapchat uses a subtle drop shadow (`rgba(0,0,0,0.4) 0 1px 2px`) or a semi-transparent pill background on text labels to keep legibility across any scene
- **Dynamic Type respected on Chat**: chat bubbles, friend names, bios scale with user preference. Camera HUD, streak counts, and lens labels stay fixed
- **Bitmoji faces are the "characters"**: Where other apps use type+photo hierarchy, Snapchat often uses bitmoji-as-character with minimal type — a friend row is "Bitmoji + Name" with almost no other text

## 4. Component Stylings

### Buttons

**The Snap Capture Button (The Big Yellow Circle)**
The most recognizable component in the app — the center-bottom button on the camera screen that captures snaps.
- Outer ring: 82pt diameter, 6pt wide ring, `#FFFC00` Snap Yellow — this is the always-visible "chrome" around the inner button
- Inner circle: 64pt diameter, `#FFFFFF` (white) — the actual tap target
- Tap (photo snap): inner circle scales 0.92 → 1.0 spring + `.impact(.soft)` haptic + camera shutter sound; flash animation white overlay 100ms
- Long-press (video snap): inner circle fills with `#FFFC00` over the press duration (up to 60s max), the outer ring becomes a progress indicator (yellow fills clockwise over 60s), `.impact(.medium)` haptic on start, `.impact(.soft)` ticks every second
- Double-tap: flips front/rear camera — shutter icon spins 180° + haptic
- Positioned: bottom-center, 20pt from bottom safe area

**Primary "Send" Button (On Snap Preview)**
After capturing a snap, user sees a preview screen with the "Send To →" button.
- Shape: Pill, 500pt corner radius
- Background: `#FFFC00` Snap Yellow
- Text: "Send To" in Avenir Next Bold 16pt `#000000`
- Trailing icon: `arrow.right` 16pt black
- Padding: 12pt vertical, 20pt horizontal
- Positioned: bottom-right corner of preview screen
- Pressed: scale 0.97 + soft haptic

**Camera HUD Icon Buttons**
Floating chromeless controls scattered around the camera viewfinder.
- No background by default — icon only, 28pt glyph, white with subtle shadow `rgba(0,0,0,0.4) 0 1px 2px`
- Size: 44pt hit area
- Icons in corners: flash top-left, flip-camera top-right, music track top-center, memories bottom-left, chat bottom-right
- When activated (e.g., flash on), icon turns `#FFFC00` yellow

**Chat Send Button**
- Shape: circle, 40pt diameter
- Background: `#FFFC00` when text is present, `#2C2C2C` when empty
- Icon: `paperplane.fill` rotated 45° in black when yellow, white when gray
- Pressed: scale 0.95 + `.impact(.light)` haptic

**Add Friend / Add Button**
- Shape: Pill, 500pt corner radius
- Not-friends: `#FFFC00` fill, black text + add icon
- Friends/added: `#2C2C2C` fill, white text "Added" + checkmark
- Font: Avenir Next Bold 14pt
- Padding: 6pt vertical, 14pt horizontal

**Lens Carousel Button (Camera)**
The horizontal strip of lens thumbnails above the capture button.
- Each lens: 60pt circle, bitmoji or lens icon thumbnail fill
- Active lens: 72pt (pulsed larger), with a `#FFFC00` outer ring 3pt
- Inactive: 60pt, no ring
- Scroll: horizontal, 12pt gap
- Below active: small caps label in Avenir Next Bold 13pt white

### Cards & Containers

**Chat Inbox Row**
- Full-width, `#000000` canvas, divided by 1pt `#333333` hairline
- Height: 72pt
- Leading: 48pt bitmoji avatar (circular frame)
- Next: friend name in Avenir Next Bold 16pt `#FFFFFF`
- Below name: **snap type icon** (colored square for photo/video/chat/audio) + status text in Avenir Next Medium 13pt `#8A8A8F`
- Trailing: timestamp "2m", streak flame + number "🔥 47" (if streak exists), "Tap to reply" subtle arrow indicator
- Tap row: opens chat
- Swipe left: reveals "Clear" action

**Snap Type Indicator (Square Icon on Chat Row)**
- 16pt filled square (unopened) or outlined square (opened)
- Colors: Red `#FF2E3D` (photo), Purple `#9B51FF` (video), Blue `#4DA7FF` (chat), Green `#4CD964` (audio)
- Direction marker: a small triangular arrow-pointing-in-direction (incoming = pointing into the square; outgoing = pointing out)
- Double-chat (you sent AND they sent unread): double-stacked icons

**Story Thumbnail (On Discover + Friends' Stories)**
- 120pt × 200pt vertical rectangle, 16pt corner radius
- Full-bleed story-preview image as background
- 3pt ring border: `#FFFC00` yellow (unread), `#555555` gray (read), `#FF2E3D` red (live)
- Top-left: 40pt circular bitmoji of creator
- Bottom-left: creator name in Avenir Next Bold 14pt white + shadow
- Horizontally scrolling row on Stories page with 12pt gaps

**Chat Bubble (Received)**
- Background: `#1A1A1A` (dark) with 20pt corner radius
- Max width: 75%
- Text: Avenir Next Medium 16pt `#FFFFFF`
- Padding: 10pt vertical, 14pt horizontal
- No tail — just rounded rect
- Aligned to leading side, 60pt indent from left (after bitmoji avatar area)

**Chat Bubble (Sent)**
- Background: `#2C2C2C` (slightly lighter dark) with 20pt corner radius
- Text: Avenir Next Medium 16pt `#FFFFFF`
- Aligned to trailing side
- Note: Snapchat chat bubbles are NOT color-coded like some messaging apps — both sent and received are dark gray variants

**Snap Map Zone Marker**
- Circular bitmoji of a friend pinned to their last-known location
- 48pt circular bitmoji with 2pt yellow ring around it
- "Sleeping" state: bitmoji closed-eyes + ZZZ emoji overlay
- "Driving" state: bitmoji in driver-seat pose
- Bottom label: friend name in 12pt white with subtle shadow

**Memories Grid Cell**
- Square 110pt × 110pt thumbnail with 8pt corner radius
- Stacked corner badges for multi-snap stories
- Tap opens full-screen player

**Spotlight Card (Full-Screen Vertical Video)**
- Edge-to-edge vertical video player (like TikTok)
- Trailing action column: favorite heart, comment, share, more — stacked at 28pt with count beneath
- Bottom: creator username + caption (2-line clamp) in Avenir Next Medium 14pt white
- Swipe up: next video; swipe down: previous

**Discover Tile (Publisher Story)**
- Card 120pt wide × 200pt tall, 16pt corner radius
- Top: publisher logo in top-left
- Background: cover image
- Bottom: tile title "Snapchat News" in Avenir Next Bold 16pt white + dark gradient overlay for legibility

### Navigation

**Bottom Navigation Strip (NOT a traditional tab bar)**
Unlike most apps, Snapchat uses a horizontal swipe between 5 screens with a minimal indicator strip at the bottom.
- Height: 56pt + safe area
- Background: semi-transparent `rgba(0,0,0,0.5)` over camera OR solid `#000000` on non-camera screens
- 5 tabs left-to-right: Map (map pin), Chat (speech bubble), Camera (center — larger, often just the outline of capture button), Stories (play icon), Spotlight (diamond)
- Icon size: 28pt for side icons, 48pt visualized for camera (actually the capture button sits above this strip)
- Active: `#FFFFFF`
- Inactive: `#555555` (lower contrast)
- Horizontal swipe transition between screens with a 300ms spring + rubber-band edges
- No labels

**Camera Top Bar (Floating)**
- No background — just icons floating over the viewfinder
- Leading: user's bitmoji (32pt) — tap opens profile
- Trailing: search (`magnifyingglass` 28pt), three-dots overflow
- All white with subtle drop shadow for legibility on any background

**Chat Top Bar**
- Height: 56pt + safe area
- Background: `#000000`
- Leading: user bitmoji + name in Avenir Next Bold 24pt "Chat"
- Trailing: plus icon 28pt (new chat)
- Hairline divider below

### Input Fields

**Chat Input Bar (Bottom of Chat Screen)**
- Height: 48pt, grows on multi-line
- Corner radius: 24pt (pill)
- Background: `#1A1A1A`
- Leading: camera icon 24pt white (opens in-chat camera)
- Center: "Send a chat" placeholder in Avenir Next Medium 15pt `#8A8A8F`
- Trailing when empty: emoji sticker icon 24pt + gallery icon 24pt
- Trailing when text entered: yellow send button 40pt circular

**Search Bar**
- Height: 44pt
- Corner radius: 22pt
- Background: `#1A1A1A`
- Leading: `magnifyingglass` 18pt `#8A8A8F`
- Placeholder: "Search" in Avenir Next Medium 16pt `#8A8A8F`

### Distinctive Components

**The Snap Capture Flow**
The center-point of the app. User on camera screen taps/holds the yellow capture button, a snap (photo or video) is captured, preview screen with filters/stickers/text overlay appears, user taps "Send To →" and selects recipients from the contacts grid.
- Flash animation: white overlay 100ms on capture
- Preview transition: captured image fades into full-screen preview over 200ms
- Edit tools (top-right): text, sticker, drawing pen, timer, save — all floating chromeless icons

**Bitmoji Everything**
- User's personalized 3D cartoon avatar (via Bitmoji app, integrated by default)
- Used as: profile photo (replaces any "user" icon), Snap Map marker, chat avatar, sticker in chats, streak flame character
- Default appearance: head-shot with expressive pose
- On Snap Map: full-body pose showing current activity ("driving", "sleeping", "at the beach")

**Streak Flame 🔥**
The signature habit-loop mechanic.
- 🔥 emoji + number next to friend names with active streaks
- Triggered: daily Snap-back-and-forth between two users for 3+ consecutive days
- Hourglass ⌛ replaces flame when streak is at risk (fewer than 4 hours to expire)
- Streak save toast: "Streak saved! 🔥" in Avenir Next Bold 16pt white on `#00D873` background
- Streak lost animation: flame turns gray + shatter particles + sad haptic

**Friend Emoji System**
Snapchat awards emoji badges next to friend names based on interaction patterns.
- 🔥 (fire): Active streak, number indicates days
- 💛 (yellow heart): #1 Best Friends (mutual top-sent-to)
- ❤️ (red heart): Best Friends for 2+ weeks
- 💕 (pink hearts): Best Friends for 2+ months
- 👶 (baby): You just became friends
- 😎 (sunglasses): One of their best friends is also one of yours (mutual)
- 🎂 (cake): It's their birthday
- 🏆 (trophy): You achieved a milestone together

**Ghost Logo (Ghostface Chillah)**
- Yellow rounded-pentagon ghost shape with simple black eyes
- Used: splash screen (`#FFFC00` background + white ghost), loading states, friend-request empty states
- Never colored other than yellow or white

**Snap Map View**
- Full-screen map with styled tiles (muted colors)
- Friend bitmojis pinned to their last known locations
- Heat-map overlay showing public snap activity density (warm = dense, cool = sparse)
- Search bar top: "Search location" with snap-location results
- Tap bitmoji: shows "Last updated 4m ago" popup

**Lens Carousel (Camera)**
- Horizontal scrollable row of circle thumbnails 4-5 visible at a time above the capture button
- Active lens thumbnail: 72pt with yellow ring + small caps label
- Inactive: 60pt, no ring
- Swipe left/right on camera viewfinder switches lenses (the whole screen's AR overlay changes in real time)
- "Explore" button at the end of carousel opens lens browser

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 56, 72, 82
- Standard horizontal margin: 16pt on Chat/Stories screens
- Camera screen: edge-to-edge viewfinder, floating controls with 16pt inset from edges
- Capture button: 20pt from bottom safe area

### Grid & Container
- Chat rows: full-width at 72pt height
- Story thumbnails: 120pt × 200pt, 12pt horizontal gap
- Discover tiles: 2-column grid on Stories page, 8pt gap
- Spotlight: full-screen single-video column
- Snap Map: edge-to-edge with floating search

### Whitespace Philosophy
- **Chat breathes**: 72pt tall rows give the color-coded snap icons and bitmoji room to read
- **Camera is empty**: viewfinder is fully cleared of UI — controls float in the corners with purpose, never cluttering the center
- **Stories are dense**: the horizontal story strip packs 4-5 thumbnails per viewport to encourage browsing
- **Discover is a grid**: 2-column publisher tiles with compact 8pt gaps — magazine-like density

### Border Radius Scale
- Small (4pt): Lens carousel labels, small badges
- Card (8pt): Memories grid thumbnails
- Thumbnail (16pt): Story thumbnails, discover tiles, snap preview video corners
- Chat Bubble (20pt): Chat message bubbles (rounder than posts — messaging feel)
- Search / Pill (22pt / 500pt): Search bar, chat input bar pill, add-friend button
- Circle (50%): Bitmoji avatars, capture button, lens carousel thumbnails, send button

## 6. Depth & Elevation

Snapchat's depth is flat on UI surfaces but dramatic on imagery. The app privileges photography over chrome — the camera viewfinder is the most elevated surface because it's literally the primary content.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Chat, Stories list, Spotlight (surfaces are just dark canvas) |
| Text-on-imagery (Level 0.5) | `rgba(0,0,0,0.4) 0 1px 2px` drop shadow on text | Camera HUD labels, story creator names, map pin names |
| Chat Bubbles (Level 1) | No shadow, pure background color contrast | Sent/received dark gray variants |
| Capture Button (Level 2) | No shadow, but the 6pt yellow ring acts as visual mass | Center-screen capture CTA |
| Snap Preview Tools (Level 2) | `rgba(0,0,0,0.4) 0 2px 8px` | Floating edit icons on snap preview screen |
| Sheet (Level 3) | `rgba(0,0,0,0.6) 0 -8px 24px` | Bottom sheet menus (send to friends list, emoji picker) |
| Modal Backdrop | `rgba(0,0,0,0.7)` | Behind story viewer, chat pop-in |

**Shadow Philosophy**: Shadows are used almost exclusively to keep text legible on live camera imagery — a soft drop shadow on HUD icons and labels so that on a bright sunny beach or a dark indoor shot, the UI stays readable. Chat and menu surfaces rely on solid color contrast, not elevation.

### Motion
- **Screen swipe (5-screen nav)**: horizontal page transition 300ms spring with rubber-band at edges; haptic `.impact(.soft)` on boundary resistance
- **Capture tap (photo)**: inner circle scale 0.92 → 1.0 spring 250ms + white flash overlay 100ms + `.impact(.soft)` + shutter sound
- **Capture hold (video)**: yellow progress ring fills clockwise over max 60s, `.impact(.medium)` on start, pulsing `.impact(.soft)` tick every 1s, release to stop
- **Camera flip (double-tap)**: shutter icon spins 180° 400ms + `.impact(.light)` + camera feed swaps
- **Lens swipe**: camera overlay cross-fades between AR effects over 200ms; center lens scales up 60pt → 72pt with yellow ring fade-in
- **Snap send**: preview screen slides left off-screen 300ms spring, confetti particles emit, success chime, return to camera
- **Streak save**: flame emoji bounces scale 1.0 → 1.3 → 1.0 + "+1 🔥" floats up 20pt fade-out over 600ms + success haptic
- **Bitmoji sticker tap in chat**: sticker pops in scale 0 → 1.0 spring 300ms + selection haptic
- **Story ring pulse (unread)**: yellow ring subtle glow-pulse every 2s (opacity 1.0 ↔ 0.7)
- **Snap Map zoom**: pinch-to-zoom with momentum deceleration

## 7. Do's and Don'ts

### Do
- Open the app directly into the live camera viewfinder — that's the Snapchat-native home state
- Use Snap Yellow `#FFFC00` for the capture button ring, streak flame, unread story ring, add-friend CTA — it's the single brand accent
- Structure the capture button as a 82pt outer yellow ring + 64pt inner white circle — the precise double-circle is iconic
- Color-code snap types in the inbox: red (photo), purple (video), blue (chat), green (audio) — this is the at-a-glance inbox language
- Use bitmoji as the default profile photo everywhere — replace generic user icons with bitmoji throughout
- Use Avenir Next Bold and Medium as the primary UI type — no thin, no regular (400)
- Float camera controls chromelessly with subtle drop shadows — no UI frame around the viewfinder
- Include friend emojis (🔥, 💛, 👑, 😎, 🎂) next to friend names based on interaction context
- Use swipe-between-five-screens navigation (Map | Chat | Camera | Stories | Spotlight) — not a traditional tab bar
- Pulse the unread story ring yellow with subtle opacity animation — signals "new content"
- Pair the capture flow with haptic + sound — shutter haptic + click sound on photo snap

### Don't
- Don't open to a feed — Snapchat always opens to the camera
- Don't use a bottom tab bar like Instagram/Facebook — Snapchat uses swipe navigation
- Don't reduce the yellow on the capture button — the 82pt ring is the brand signature
- Don't colorize chat bubbles like iMessage blue/gray — Snapchat bubbles are dark gray on dark canvas
- Don't replace bitmoji with photo avatars as the default — bitmoji IS the profile photo in Snapchat
- Don't add permanent UI chrome around the viewfinder — camera is always edge-to-edge
- Don't use light mode as the default — the app's gravity is dark/camera-focused
- Don't use thin font weights — Avenir Next weights in the app are 500 and 700 only
- Don't use uppercase everywhere — uppercase is reserved for HUD labels like "MEMORIES" with +0.3pt tracking
- Don't show read receipts as checkmarks — Snapchat shows them as the snap-type icon transitioning from filled to outline
- Don't skip the streak mechanics — the 🔥 and accompanying emoji system IS the social habit loop
- Don't make chat messages persist by default — the signature is ephemerality; messages delete on close unless saved

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Capture button 72pt (smaller); lens carousel 4 visible |
| iPhone 13/14/15 | 390pt | Standard 82pt capture button; lens carousel 5 visible |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated — top camera icons shift around it |
| iPhone 15/16 Pro Max | 430pt | 82pt capture + larger lens thumbnails 72pt; more discover tiles per row |
| iPad | 768pt+ | Camera stays centered at max 768pt width; Chat opens to split-view with conversation list + thread |

### Dynamic Type
- Chat messages, chat row names, bio, settings text: full scale
- Camera HUD labels, streak counts, lens labels, tab indicators: fixed (layout-sensitive)
- Timestamps: fixed 12pt
- Story thumbnail names: fixed 14pt

### Orientation
- Camera: **portrait-locked** (the app's gravity)
- Chat / Stories / Spotlight / Map: **portrait-locked**
- Video playback in Spotlight / Stories: **rotates to landscape** for fullscreen
- Snap capture while video recording: can rotate if user physically rotates device, but interface stays portrait

### Touch Targets
- Capture button: 82pt — the largest in any app
- Lens thumbnails: 60-72pt
- Chat row: 72pt tall, full-row tappable
- Camera HUD icons: 28pt glyph with 44pt hit area
- Bottom nav icons: 28pt glyph, 56pt effective hit
- Chat send button: 40pt

### Safe Area Handling
- Top: camera icons respect safe area + Dynamic Island; bitmoji shifts slightly around island on Pro devices
- Bottom: capture button 20pt above home indicator; bottom nav strip respects safe area
- Sides: 16pt insets on Chat and Stories; camera is edge-to-edge

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas black: `#000000`
- Surface 1: `#1A1A1A`
- Surface 2: `#2C2C2C`
- Divider: `#333333`
- Text primary: `#FFFFFF`
- Text secondary: `#8A8A8F`
- Snap Yellow: `#FFFC00`
- Photo red: `#FF2E3D`
- Video purple: `#9B51FF`
- Chat blue: `#4DA7FF`
- Audio green: `#4CD964`
- Success green: `#00D873`
- Error red: `#FF3B30`

### Example Component Prompts
- "Build Snapchat's capture button in SwiftUI: concentric double-circle. Outer: 82pt diameter, 6pt wide ring in `#FFFC00` Snap Yellow. Inner: 64pt white circle centered inside. On tap (photo): inner circle scales 0.92 → 1.0 spring 250ms, white screen flash overlay 100ms, `.impact(.soft)` haptic + camera shutter sound. On long-press (video): inner fills with `#FFFC00` while outer ring becomes a clockwise progress indicator filling over max 60s, `.impact(.medium)` on start, soft ticks every 1s. On double-tap: camera flips front/rear with shutter icon 180° spin + light haptic. Position: bottom-center of camera viewfinder, 20pt from bottom safe area."
- "Create a Snapchat chat inbox row: 72pt height on `#000000` canvas, 1pt `#333333` divider bottom. Leading 48pt circular bitmoji avatar (16pt from left). Right of avatar: friend name 'Sarah Jones' in Avenir Next Bold 16pt `#FFFFFF`, below it the snap type indicator — 16pt filled square in red `#FF2E3D` for photo, purple `#9B51FF` for video, blue `#4DA7FF` for chat, green `#4CD964` for audio, with a triangular arrow pointing into the square (received) or out of it (sent) — followed by status 'Received · 2m ago' in Avenir Next Medium 13pt `#8A8A8F`. Trailing: timestamp '2m' in 12pt `#8A8A8F`, streak emoji '🔥 47' in Avenir Next Bold 18pt if streak exists."
- "Design the Snapchat Stories section: horizontal scrollable row of story thumbnails, each 120pt × 200pt rounded 16pt, full-bleed story preview image background. 3pt ring border: `#FFFC00` yellow for unread with subtle 2s opacity-pulse animation (1.0 ↔ 0.7), `#555555` gray for read, `#FF2E3D` red for live. Top-left: 40pt circular bitmoji of creator. Bottom-left: creator name in Avenir Next Bold 14pt white with `rgba(0,0,0,0.4) 0 1px 2px` drop shadow. 12pt horizontal gap between thumbnails."
- "Build the Snapchat camera HUD in SwiftUI: full-screen live camera viewfinder as background, chromeless controls floating in corners with `rgba(0,0,0,0.4) 0 1px 2px` drop shadow on icons for legibility over any scene. Top-left: user bitmoji 32pt (profile). Top-right: `magnifyingglass` 28pt (search). Bottom-left: `photo.on.rectangle` 28pt (memories). Bottom-right: `bubble.left.fill` 28pt (chat). Above capture button: horizontal lens carousel of 60pt circular lens thumbnails, active lens scaled to 72pt with 3pt `#FFFC00` ring + 'Beach' label in Avenir Next Bold 13pt w700 tracked 0.3pt uppercase white. Center-bottom: capture button (see separate prompt)."
- "Create the Snapchat chat bubble pair: received bubble on leading side — `#1A1A1A` background, 20pt corner radius, 10pt×14pt padding, Avenir Next Medium 16pt `#FFFFFF` text, max 75% width, 60pt left indent to leave room for 32pt circular bitmoji on trailing edge of leading gutter. Sent bubble on trailing side — `#2C2C2C` background, same radius/padding/font, aligned to right edge of screen. No tails — just rounded rectangles. Between-bubbles gap: 4pt for same sender, 12pt for sender change."
- "Build the Snap Map view: full-screen styled map (muted pastel tiles), user's friends displayed as 48pt circular bitmoji pins at their last-known locations with a 2pt yellow `#FFFC00` ring around each. Each pin shows friend's bitmoji in context pose ('driving' pose with car, 'sleeping' pose with ZZZ, 'beach' pose with sunglasses). Bottom-right: friend name label in 12pt white with drop shadow. Heat-map overlay in corners: yellow `#FFFC00` in cool zones, warming through orange `#FF9A1F` to red `#FF2E3D` in dense public-snap zones. Search bar top: `magnifyingglass` + 'Search location' in a pill at 44pt with `#1A1A1A` fill."

### Iteration Guide
1. Camera IS the home screen — the app opens directly to the live viewfinder full-screen
2. Snap Yellow `#FFFC00` is the aggressive brand accent — capture ring, streak flame, unread story ring, ghost logo
3. Capture button is the 82pt outer yellow ring + 64pt inner white circle — the precise double-circle is iconic
4. Navigation is swipe-between-five-screens (Map | Chat | Camera | Stories | Spotlight) — NOT a tab bar
5. Color-code snap types in chat: red (photo), purple (video), blue (chat), green (audio)
6. Bitmoji replaces profile photos app-wide — user's personalized cartoon avatar is the "face"
7. Typography is Avenir Next at Medium (500) and Bold (700) only — warm humanist geometric sans
8. Camera UI is chromeless — floating icons with drop shadow, no UI frame around the viewfinder
9. Friend emojis are the habit-loop mechanic: 🔥 streaks, 💛 best friends, 😎 mutual BFFs, 🎂 birthdays
10. Ephemerality is the UX signature — chats delete on close, stories expire in 24h; never persist content by default
