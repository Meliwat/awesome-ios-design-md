# Design System Inspiration of Threema (iOS)

## 1. Visual Theme & Atmosphere

Threema's iOS app is a privacy-first messenger, and its entire visual language is engineered around one promise: you can communicate without revealing who you are. There is no phone number, no email, no Google or Apple sign-in — your identity is an anonymous, randomly-generated 8-character **Threema ID** (e.g. `ECHOX9P2`). Because the product's value is trust and confidentiality rather than reach or feed-scrolling, the UI is deliberately calm, utilitarian, and Swiss-engineered. It looks like a serious tool, not a social toy: a clean white canvas (`#FFFFFF`), a single confident green (`#088A29`) for every action, generous spacing, and almost no decoration. The aesthetic says "encrypted, audited, made in Switzerland" — closer to a banking or password-manager app than to a consumer chat app.

The one piece of UI that carries Threema's whole identity is the **trust-level indicator** — three small dots that sit next to every contact and conversation. Red (Level 1) means an unknown contact whose ID has not been verified. Orange (Level 2) means the contact was matched from your address book via a server-side hash (the ID is linked to a known phone/email, but you have not personally checked it). Green (Level 3) means you scanned the person's QR code face-to-face and the cryptographic key fingerprint matched — the only fully-trusted state. These three dots appear in the conversation list, the chat navigation bar, the contact detail screen, and the verify screen. Learning to "get someone to green" is the core ritual of the product, and the dots are the single most recognizable Threema visual.

The other signature surface is the **QR / verify screen**: a large QR code encoding your Threema ID + public key fingerprint, your ID in a monospaced string, and a scanner that, on a successful in-person match, animates the contact up to three green dots. Identity and verification — not feeds, not stories, not reactions — are the product's emotional center, and the design spends its richest moments here.

Color is restrained to the extreme. Threema Green is the only brand color; it is the send button, the outgoing message bubble, the primary CTA, links, switches, and the active tab tint. There is no secondary accent. Status is communicated through the trust palette (red / orange / green) and through standard system semantics (a destructive red for delete/block). Outgoing bubbles are a soft green tint; incoming bubbles are a neutral gray. Everything else is white, near-black text, and hairline dividers. The result is a screen where the eye is never pulled anywhere except the content and the trust state.

Typography is Apple's **SF Pro** — Threema does not ship a custom typeface; it leans entirely on the iOS system font and Dynamic Type. The hierarchy is conventional iOS: large titles for top-level screens, semibold for names and section headers, regular for message body, and a **monospaced** treatment specifically for the Threema ID and key fingerprint (so the random character string is unambiguous and easy to read aloud during verification). The type system is invisible by design — the brand is the green and the trust dots, not the letterforms.

**Key Characteristics:**
- Pure white canvas (`#FFFFFF`) light / dark charcoal (`#121212`) dark — calm, utilitarian, Swiss
- Single brand color: Threema Green (`#088A29`) — send, CTA, links, active tab, switches
- **Trust-level dots** (red `#E5453A` / orange `#EF8B2C` / green `#15A33A`) — the signature element, on every contact
- **Threema ID** — anonymous 8-char identity, shown in **monospace**; no phone number or email
- **QR verify flow** — scan in person to reach Level 3 (three green dots)
- Outgoing bubble = soft green tint; incoming bubble = neutral gray; tails on the speaker side
- SF Pro system font + Dynamic Type — no custom typeface
- Minimal chrome: a 4-5 tab bottom bar (Chats / Contacts / Calls / My ID / Settings), system nav bar
- End-to-end encryption messaging surfaced everywhere: lock glyphs, "verified" labels, key-fingerprint strings
- Destructive actions in system red; success/verified states in trust green

## 2. Color Palette & Roles

### Primary (Interactive)
- **Threema Green** (`#088A29`): The single brand color — send button, primary CTA fill, links, switches, active tab tint, selection.
- **Threema Green Pressed** (`#066B20`): Pressed/active state for primary buttons.
- **Threema Green Bright** (`#1FA53C`): Dark-mode variant of the brand green — used for tint, links, and active tab on the dark canvas where `#088A29` is too dim.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas — conversation list, chat background, settings.
- **Surface Gray** (`#F4F4F6`): Grouped table section background, search field, settings cells.
- **Surface Pressed** (`#E8E8EB`): Pressed/selected list rows.
- **Divider** (`#E2E2E5`): Hairline separators between list rows and sections.
- **Incoming Bubble** (`#EBEBED`): Received message bubble fill.
- **Outgoing Bubble** (`#D6F0DC`): Sent message bubble fill — a soft green tint of the brand.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#121212`): Primary dark canvas.
- **Dark Surface 1** (`#1C1C1E`): Grouped section background, nav bar, settings cells.
- **Dark Surface 2** (`#262628`): Incoming bubble, search field, pressed rows.
- **Dark Divider** (`#2C2C2E`): Hairline separators on dark.
- **Outgoing Bubble (Dark)** (`#0C5E22`): Sent bubble on dark — a deep green.

### Text
- **Text Primary Light** (`#111113`): Names, message body, titles — near-black, not pure black.
- **Text Secondary Light** (`#6B6B70`): Timestamps, subtitles, metadata, placeholder.
- **Text Tertiary Light** (`#A6A6AB`): Disabled, very low-emphasis labels.
- **Text Primary Dark** (`#ECECEC`): Primary text on dark canvas.
- **Text Secondary Dark** (`#9A9A9F`): Secondary text on dark canvas.

### Trust Level Palette (the signature roles)
| Role | Color | Meaning |
|------|-------|---------|
| Trust Level 1 (Red) | `#E5453A` | Unknown contact — ID not verified, no shared key |
| Trust Level 2 (Orange) | `#EF8B2C` | Matched from address book — server-linked, not personally checked |
| Trust Level 3 (Green) | `#15A33A` | Fully verified — QR code scanned in person, fingerprint matched |
| Trust Track (empty) | `#D2D2D7` light / `#3A3A3E` dark | Inactive dot slot in some compact indicators |

### Semantic
- **Error / Destructive Red** (`#E5453A`): Delete chat, block contact, failed send, revoke. (Shares hue with Trust Level 1 — destructive and untrusted both read as red.)
- **Success / Verified Green** (`#15A33A`): Verification success, "Verified" badge, delivered+read confirmation accents.
- **Warning Orange** (`#EF8B2C`): "Not verified" caution banners, downgrade warnings.
- **Link** (`#088A29` light / `#1FA53C` dark): Inline links, "Add via ID", profile links.
- **Read Receipt Check** (`#3A3A3E` light / `#6FD68C` dark accent): Double-check delivery/read glyph in the bubble meta row.

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro` (Apple system font) — Threema ships no custom typeface; it uses the iOS system font with Dynamic Type throughout. SF Pro Text for body sizes, SF Pro Display for large titles.
- **Monospace**: `SF Mono` — used specifically for the Threema ID, public-key fingerprint, and any cryptographic string, so random characters are unambiguous and easy to read aloud during in-person verification.
- **Fallback Stack (non-Apple / web mirror)**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Inter', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Not user-switchable**: The face is always the system font; only Dynamic Type size scales.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | SF Pro Display | 32pt | 800 | 1.15 | -0.4pt | Top-level screen header ("Chats", "Contacts") |
| Screen Title | SF Pro Display | 26pt | 700 | 1.2 | -0.3pt | "Verify Contact", "My ID" detail screens |
| Section Header | SF Pro Text | 22pt | 700 | 1.25 | -0.2pt | "Your Threema ID", grouped header |
| Nav Title / Name | SF Pro Text | 18pt | 600 | 1.3 | -0.1pt | Contact name in chat nav bar, list title |
| Message Body | SF Pro Text | 16pt | 400 | 1.35 | 0pt | Chat bubble text — the most-read style |
| List Title | SF Pro Text | 15pt | 500 | 1.3 | 0pt | Conversation list primary line |
| Secondary / Subtitle | SF Pro Text | 14pt | 400 | 1.35 | 0pt | Last-message preview, trust caption, metadata |
| Threema ID / Fingerprint | SF Mono | 14pt | 600 | 1.4 | 1pt | Anonymous ID + key fingerprint — monospaced |
| Caption / Timestamp | SF Pro Text | 12pt | 500 | 1.2 | 0pt | Bubble timestamp, day separators |
| Button | SF Pro Text | 16pt | 600 | 1.0 | 0pt | Primary / secondary buttons |
| Tab Label | SF Pro Text | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Badge / Unread Count | SF Pro Text | 12pt | 700 | 1.0 | 0pt | Unread count pill |

### Principles
- **System font, always**: Threema never overrides SF Pro — respect Dynamic Type and the user's preferred text size globally.
- **Monospace is for identity strings only**: The Threema ID and key fingerprint use SF Mono so characters never get confused (0/O, 1/l) when read aloud during verification; nothing else is monospaced.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: Regular body, medium for list rows, semibold for names and buttons, bold for titles and trust labels.
- **Hierarchy via weight + position, not color**: Color is reserved for the green and the trust dots. Text hierarchy comes from size and weight only.
- **Dynamic Type first-class**: Titles, names, message body, captions all scale. Tab labels, trust-dot indicators, and the monospaced ID string stay fixed (layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary Button (Scan QR / Save / Send Verification)**
- Shape: Rounded rectangle, 10pt corner radius
- Background: `#088A29` (Threema Green) / `#1FA53C` (dark mode)
- Text: `#FFFFFF`
- Padding: 14pt vertical, 28pt horizontal
- Font: SF Pro Text 16pt weight 600
- Pressed: background `#066B20` + scale 0.98 + light haptic
- Disabled: background `#088A29` at 35% opacity, text at 60%

**Secondary / Outline Button (Share My ID / Cancel)**
- Background: transparent
- Text: `#088A29` light / `#1FA53C` dark
- Border: 1.5pt `#088A29` / `#1FA53C`
- Padding: 12pt vertical, 22pt horizontal
- Corner radius: 10pt
- Font: SF Pro Text 15pt weight 600
- Pressed: background `#088A29` at 8% opacity

**Pill Button (New Chat / Filter)**
- Background: `#F4F4F6` light / `#262628` dark
- Text: `#111113` light / `#ECECEC` dark
- Border: 1pt `#E2E2E5` / `#2C2C2E`
- Padding: 10pt vertical, 18pt horizontal
- Corner radius: 999pt (full pill)
- Font: SF Pro Text 14pt weight 500

**Text Button (Add via Threema ID / Forgot)**
- Font: SF Pro Text 15pt weight 600
- Color: `#088A29` light / `#1FA53C` dark
- No underline, no background
- Pressed: color `#066B20` + 0.6 opacity

**Send Button (composer)**
- Circular, 36pt diameter
- Background: `#088A29` / `#1FA53C`
- Glyph: paper-plane (SF Symbol `paperplane.fill`) 17pt white
- Disabled (empty field): background `#D2D2D7` / `#3A3A3E`

### Trust-Level Indicator (the core atom)

The signature Threema component. A horizontal row of three dots that encodes contact verification.

- **Dot size**: 7pt in compact contexts (list row, nav subtitle), 10pt in detail/verify screens, 14pt on the contact-detail hero
- **Spacing**: 3-4pt between dots
- **Colors**: all three dots share the level color — Level 1 = three `#E5453A` red, Level 2 = three `#EF8B2C` orange, Level 3 = three `#15A33A` green
- **Placement**: leading the contact name in lists, in the chat nav subtitle ("Verified · ECHOX9P2"), and as a large badge on the contact-detail screen
- **Accompanying label**: a 14pt secondary-color caption ("Unknown" / "Server-matched" / "Verified") sits beside or below the dots
- **State change**: when verification succeeds, the dots crossfade from red/orange to green over 200ms with a soft success haptic
- **Accessibility**: never communicated by color alone — always paired with the text label and a VoiceOver string ("Trust level: verified")

### Threema ID Chip / Field

- Monospaced string (SF Mono 14pt w600), letter-spacing 1pt
- Background: `#F4F4F6` light / `#262628` dark, 8pt corner radius, 8pt × 12pt padding (when shown as a chip)
- Often paired with a copy icon (SF Symbol `doc.on.doc`) and a share action
- The ID is always 8 characters, uppercase alphanumeric, randomly assigned

### QR Verify Card

- Large QR code: white quiet-zone card, 12pt corner radius, QR modules rendered in `#088A29` on white (or pure black on white for max scanner compatibility — Threema uses brand green in-app, black for export)
- Below: the Threema ID in SF Mono, plus the public-key fingerprint
- Scanner view: full-bleed camera with a centered rounded-square reticle (`#FFFFFF` 2.5pt stroke) and a green corner-pulse animation
- Success: reticle flashes `#15A33A`, contact animates to three green dots, success haptic

### Message Bubbles

**Incoming Bubble**
- Background: `#EBEBED` light / `#262628` dark
- Text: `#111113` / `#ECECEC`, SF Pro Text 16pt w400
- Corner radius: 16pt with a 4pt tail corner on the bottom-leading side
- Max width: ~78% of screen
- Meta row: timestamp 12pt `#6B6B70` / `#9A9A9F`, trailing-aligned

**Outgoing Bubble**
- Background: `#D6F0DC` (soft green tint) light / `#0C5E22` (deep green) dark
- Text: `#0B3D17` / `#EAFBEF`, SF Pro Text 16pt w400
- Corner radius: 16pt with a 4pt tail corner on the bottom-trailing side
- Meta row: timestamp + delivery state — single check (sent), double check (delivered), double check tinted (`#15A33A` / `#6FD68C`) when read

### Navigation

**Chat Navigation Bar**
- Height: 54pt + safe area
- Leading: back chevron in brand green (`#1FA53C` on dark) + circular 34pt contact avatar
- Center-leading: contact name (18pt w600) with a subtitle row = trust dots + Threema ID ("Verified · ECHOX9P2")
- Trailing: call glyph (SF Symbol `phone`) in brand green; tap opens encrypted call
- Background: blurred canvas (`rgba(18,18,18,0.96)` dark), 0.5pt bottom divider

**Bottom Tab Bar**
- Height: 68pt incl. home indicator
- Tabs: Chats, Contacts, Calls, My ID, Settings (some builds: 4 tabs without Calls)
- Icon size: 22pt
- Active color: `#088A29` / `#1FA53C` (filled glyph); inactive: `#818185`
- Labels: SF Pro Text 10pt w500, always shown
- Background: blurred (`saturate(180%) blur(20px)`), 0.5pt top divider
- Unread badge: red `#E5453A` pill with white 12pt w700 count on the Chats tab

### Input Fields

**Composer**
- Height: 56pt
- Leading: `+` attach glyph (24pt, brand green)
- Center: rounded text field — `#F4F4F6` / `#262628`, 19pt corner radius (pill), 16pt horizontal padding, placeholder "Message" in secondary color
- Trailing: circular green send button (see Buttons)

**Search Field**
- Height: 38pt
- Background: `#F4F4F6` / `#262628`
- Corner radius: 10pt
- Leading magnifier glyph 16pt secondary color, placeholder "Search" 14pt secondary
- Focus: 1.5pt `#088A29` / `#1FA53C` border

**Threema ID Entry (Add Contact)**
- Monospaced input, SF Mono 16pt, auto-uppercasing, 8-char limit
- Inline validation: green check when the ID resolves, red "ID not found" otherwise

### Distinctive Components

**Trust-Level Dots** — see core atom above. The single most recognizable Threema element.

**QR Verify Flow** — large QR + scanner + the green-dot upgrade animation. The product's emotional center.

**Verified Banner** — a full-width inset bar above the composer when a contact is unverified: orange `#EF8B2C` left rule, "This contact is not verified. Scan their QR code to verify." with a "Verify" text button.

**Encrypted-Call Pill** — in-call screen shows a lock glyph + "End-to-end encrypted" caption and a short verification phrase (emoji/word sequence) both parties can read aloud.

**My ID Card** — the user's own identity surface: avatar, nickname, the Threema ID in SF Mono, QR code, public-key fingerprint, and "Revoke ID" (destructive red).

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 56
- List row height: 64pt (avatar + two text lines + trust dots)
- Section gap (grouped lists): 32pt
- Screen content inset: 16pt horizontal
- Bubble vertical gap: 8pt; grouped same-sender bubbles tighten to 3pt

### Grid & Container
- iPhone: content at 16pt horizontal insets; bubbles cap at 78% width
- iPad: chat list and conversation in a split view (list ~340pt, conversation flexible)
- Grouped settings/contacts use inset-grouped table style, 16pt outer inset, 10pt cell corner radius

### Whitespace Philosophy
- **Utilitarian calm**: generous vertical rhythm; nothing decorative competes with content
- **Trust state always visible**: the dots are never hidden behind a tap — they sit inline on every contact row
- **Identity gets the hero**: the My ID and Verify screens are the most spacious, with the QR code as the focal element
- **Color is scarce**: white/charcoal dominates; green and the trust palette are the only saturated pixels

### Border Radius Scale
- Square (0pt): full-bleed scanner camera, dividers
- Subtle (4pt): bubble tail corner, small chips
- Small (8pt): Threema ID chip, search field, code blocks
- Standard (10pt): primary/secondary buttons, grouped table cells, QR card
- Comfortable (16pt): message bubbles, bottom sheets
- Pill (999pt): pill buttons, composer field, unread badge
- Circle (50%): avatars, send button, trust dots

## 6. Depth & Elevation

Threema is nearly flat — it is a security tool, not a glossy consumer app. Elevation is used sparingly and functionally.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, bubbles, settings cells, page content |
| Bar (Level 1) | Backdrop blur + 0.5pt hairline divider | Nav bar, bottom tab bar (translucent over content) |
| Floating (Level 2) | `rgba(0,0,0,0.12) 0 4px 16px` | Context menus, the attach sheet, action popovers |
| Sheet (Level 3) | `rgba(0,0,0,0.18) 0 -4px 24px` | Bottom sheets (share ID, verify options, QR export) |
| Modal Overlay | `rgba(0,0,0,0.4)` scrim | Dim behind sheets and full-screen scanner |

**Shadow Philosophy**: Shadow exists only to say "this floats, tap outside to dismiss" — never as decoration. Bars use translucency + a hairline divider instead of a drop shadow. On dark mode shadows nearly vanish, so floating panels add a 0.5pt `#2C2C2E` border as the elevation cue.

### Motion
- **Trust upgrade (verify success)**: dots crossfade red/orange → green over 200ms ease-out + soft success haptic; a brief green ring pulse around the avatar
- **QR scan acquire**: reticle corners pulse green (`#15A33A`) on a 1.2s loop while scanning; on match, single 250ms flash + haptic
- **Send message**: bubble scales 0.96 → 1.0 over 180ms ease-out, slides up from the composer; delivery checks fade in as state updates
- **Bubble appear (incoming)**: fade + 6pt slide-up over 200ms ease-out
- **Tab switch**: instant; active glyph crossfades fill in 120ms
- **Sheet present**: slides up 300ms ease-out, scrim fades 250ms
- **Push navigation**: native iOS push, 300ms horizontal slide
- **Pull-to-refresh / sync**: subtle green spinner; no skeletue noise — Threema syncs quietly
- **Haptics**: soft impact on send, success notification haptic on verification, light tick on tab change

## 7. Do's and Don'ts

### Do
- Use pure white (`#FFFFFF`) as the light canvas and dark charcoal (`#121212`) for dark — calm and utilitarian
- Use Threema Green (`#088A29`) as the *only* brand color; brighten to `#1FA53C` on dark
- Render the trust-level indicator as three dots, colored by level (red / orange / green), on every contact
- Always pair the trust dots with a text label and VoiceOver string — never color alone
- Show the Threema ID and key fingerprint in **monospace** (SF Mono) so characters are unambiguous
- Make the QR verify flow the emotional center — large QR, in-person scanner, green-dot upgrade animation
- Tint outgoing bubbles a soft green; keep incoming bubbles neutral gray
- Use SF Pro + Dynamic Type — respect the user's text-size setting globally
- Reserve red for both destructive actions and Trust Level 1 (untrusted) — same hue, consistent meaning
- Keep chrome minimal: translucent bars, hairline dividers, no decorative shadows
- Surface "end-to-end encrypted" affordances (lock glyphs, verified labels) where trust matters

### Don't
- Don't introduce a secondary brand color — Threema has exactly one accent (the green)
- Don't communicate trust state by color alone — always add the label (accessibility + clarity)
- Don't render the Threema ID in a proportional font — it must be monospaced for verification
- Don't ask for a phone number or email as identity — the Threema ID *is* the identity
- Don't ship a custom typeface — Threema uses the system font; respect Dynamic Type
- Don't use heavy drop shadows or glossy gradients — this is a security tool, keep it flat
- Don't bury the trust dots behind a tap — they belong inline on every contact and conversation
- Don't use green for anything that isn't a Threema action or the Trust Level 3 state
- Don't animate aggressively — motion is quiet, 120-300ms ease-out
- Don't auto-upgrade a contact to green — Level 3 requires an explicit in-person QR scan
- Don't use pure black (`#000000`) for body text — `#111113` near-black is correct

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Trust dots 7pt; bubbles cap 80%; compact nav |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in nav bar |
| iPhone 15/16 Pro Max | 430pt | Bubbles cap 76%; larger QR card; bigger avatars |
| iPad (portrait) | 768pt | Split view: conversation list (~340pt) + chat detail |
| iPad (landscape) | 1024pt+ | Persistent 3-column option (list + chat + contact info) |

### Dynamic Type
- Large titles, names, message body, captions: full scale
- Tab labels, trust-dot indicators, the monospaced Threema ID string: FIXED (layout-sensitive)
- Bubbles grow vertically with text size; max width stays a screen-width percentage

### Orientation
- All screens support rotation
- iPad uses split-view: primary list + secondary conversation; QR scanner remains full-screen modal
- Scanner locks to portrait on iPhone for reliable QR acquisition

### Touch Targets
- Trust-dot row: full row tappable (opens contact detail), 44pt min hit
- Tab bar icons: 22pt glyph, 49pt hit
- List row: 64pt tall, full-row tap
- Send button: 36pt visual, 44pt hit
- Composer attach `+`: 24pt glyph, 44pt hit
- QR scanner reticle: full camera area is the scan target

### Safe Area Handling
- Top: nav bar respects safe area and Dynamic Island
- Bottom: tab bar and composer respect the home indicator
- Keyboard: chat scrolls above keyboard; composer pins to keyboard top
- Sides: 16pt content inset
- Scanner: full-bleed including safe areas, with controls inset to safe area

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#121212`
- Surface gray: `#F4F4F6` light / `#262628` dark
- Divider: `#E2E2E5` light / `#2C2C2E` dark
- Text primary: `#111113` light / `#ECECEC` dark
- Text secondary: `#6B6B70` light / `#9A9A9F` dark
- Threema Green: `#088A29` light / `#1FA53C` dark
- Green pressed: `#066B20`
- Incoming bubble: `#EBEBED` light / `#262628` dark
- Outgoing bubble: `#D6F0DC` light / `#0C5E22` dark
- Trust Level 1 (red): `#E5453A`
- Trust Level 2 (orange): `#EF8B2C`
- Trust Level 3 (green): `#15A33A`
- Error / destructive: `#E5453A`
- Link: `#088A29` light / `#1FA53C` dark

### Example Component Prompts
- "Build the Threema trust-level indicator in SwiftUI: a horizontal `HStack` of three 8pt circles with 3pt spacing. Level 1 → all `#E5453A`, Level 2 → all `#EF8B2C`, Level 3 → all `#15A33A`. Beside the dots, a SF Pro Text 14pt w400 secondary-color label ('Unknown' / 'Server-matched' / 'Verified'). Expose a combined VoiceOver string 'Trust level: verified'. On a verification success, crossfade dots to green over 200ms with a `.success` sensory feedback."

- "Create the Threema chat navigation bar: 54pt + safe area, leading back chevron in `#1FA53C` (dark) + a 34pt circular avatar. Center-leading: contact name SF Pro Text 18pt w600 `#ECECEC`, subtitle row = three 7pt green trust dots + ' Verified · ECHOX9P2' in 11pt `#9A9A9F`. Trailing: `phone` SF Symbol 21pt in brand green. Background `rgba(18,18,18,0.96)` with 0.5pt `#2C2C2E` bottom divider."

- "Render a Threema outgoing message bubble: background `#0C5E22` (dark), text `#EAFBEF` SF Pro Text 16pt w400, max width 78%, corner radius 16pt with a 4pt tail on the bottom-trailing corner, trailing-aligned. Meta row below text: timestamp '09:41' 10pt `rgba(255,255,255,0.55)` + a double-check delivery glyph tinted `#6FD68C` when read. Append on send with a 0.96 → 1.0 scale over 180ms ease-out."

- "Build the Threema QR verify card: a white card 12pt corner radius containing a QR code with modules in `#088A29`, below it the Threema ID 'ECHOX9P2' in SF Mono 16pt w700 letter-spacing 1pt, and a caption 'Scan in person to reach three green dots' in 12pt `#9A9A9F`. Add a scanner mode: full-bleed camera, centered rounded-square reticle with a 2.5pt white stroke and green corner-pulse on a 1.2s loop; on match flash `#15A33A` for 250ms + success haptic."

- "Create the Threema composer: 56pt tall, leading `+` glyph 24pt `#1FA53C`, a pill text field (`#262628`, 19pt radius, 16pt padding, placeholder 'Message' in `#9A9A9F`), trailing a 36pt circular send button filled `#088A29` with a white `paperplane.fill` 17pt — disabled fill `#3A3A3E` when the field is empty."

- "Render the Threema bottom tab bar: 68pt incl. home indicator, tabs Chats / Contacts / Calls / My ID / Settings, 22pt glyphs, active tint `#1FA53C` (filled glyph) inactive `#818185`, labels SF Pro Text 10pt w500 always shown, translucent `saturate(180%) blur(20px)` background with a 0.5pt `#2C2C2E` top divider, and a red `#E5453A` unread-count pill (white 12pt w700) on the Chats tab."

### Iteration Guide
1. Canvas is pure white (`#FFFFFF`) light, charcoal `#121212` dark — calm, utilitarian, NOT off-white
2. Threema Green `#088A29` is the only brand color; brighten to `#1FA53C` on dark — never add a second accent
3. The trust-level dots (red `#E5453A` / orange `#EF8B2C` / green `#15A33A`) are the signature — on every contact, always with a label
4. Threema ID and key fingerprint are monospaced (SF Mono) — random characters must be unambiguous
5. Identity has no phone number or email — the 8-char Threema ID *is* the account
6. The QR verify flow is the emotional center — large QR + in-person scanner + green-dot upgrade animation
7. Outgoing bubbles are a soft green tint; incoming are neutral gray; tails on the speaker side
8. Typography is SF Pro + Dynamic Type — no custom typeface, respect the user's text size
9. Red means both destructive *and* untrusted (Trust Level 1) — consistent hue, consistent meaning
10. Keep it flat — translucent bars, hairline dividers, no decorative shadows; motion is quiet 120-300ms
