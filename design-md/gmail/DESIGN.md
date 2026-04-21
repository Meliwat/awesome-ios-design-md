# Design System Inspiration of Gmail (iOS)

## 1. Visual Theme & Atmosphere

Gmail's iOS app is a Material You inbox that inherits Google's multicolor brand DNA while staying clean enough to spend all day in. The canvas is pure white (`#FFFFFF`) on light mode — the screen feels like paper, deliberately unbranded, so the *contents* of your inbox (subjects, snippets, labels) carry the visual information. The only structural saturation comes from the Gmail red (`#D93025`) reserved almost exclusively for the Compose FAB (the floating plus button bottom-right), the Gmail logotype, and destructive states like "Delete forever". Outside of that single red accent, the UI leans on Google's 4-color palette — Red `#EA4335`, Yellow `#FBBC04`, Green `#34A853`, Blue `#4285F4` — applied sparingly on category chips, avatar fallbacks, and Smart Reply affordances.

The identity-defining moment is the **Compose FAB** — a red squircle-ish rounded square floating in the bottom-right corner, filled with a white `+` (and the word "Compose" on tablet widths). It sits above the email list, casts a soft Material shadow, and lifts on scroll to stay in reach. Gmail's history as a mobile-first rewrite of webmail shows here: the compose action is the most important verb in the app, and the FAB guarantees it's always one tap away. Every other interaction (swipe to archive, tap to open, pinch to zoom) is touch-native and treats the email list as a dense scrolling surface rather than a designed page.

Typography is a two-family stack: **Google Sans** for titles and buttons (proprietary; the geometric humanist sans used on google.com, Android, Pixel devices) and **Roboto** for body and metadata (also proprietary to Google, optimized for on-screen legibility at small sizes). On iOS, Gmail falls back to SF Pro when Google Sans isn't bundled, keeping the system-native feel. Hierarchy is compact: email-list subject lines at 14pt weight 500, snippets at 13pt weight 400 in gray, sender names at 14pt weight 500 (bold if unread). The expressive type moment is the inbox section header ("Today", "Yesterday", "This week"), which appears in Google Sans 13pt weight 500 uppercase tracked, acting as a temporal ruler through the day's mail.

**Key Characteristics:**
- Pure white canvas (`#FFFFFF`) light / near-black (`#202124`) dark — unbranded surface, content-first
- Gmail red (`#D93025`) reserved for the Compose FAB and destructive actions — everything else is calm
- Material You influenced: soft shadows, rounded squircle icons, floating action button as primary CTA
- Google 4-color palette (`#EA4335` red, `#FBBC04` yellow, `#34A853` green, `#4285F4` blue) applied to avatars, labels, Smart Reply chips
- Email list rows with 36pt circular avatars — color-coded by sender, show initial or photo
- Unread state = bold sender + bold subject + vertical inbox indicator stripe
- Swipe actions: archive (left, light teal), delete (right, red) — customizable by user
- Label chips in user-picked colors, 500pt pill radius, Google Sans
- Bottom tab bar: Mail, Chat, Spaces, Meet — Chat/Spaces/Meet optional per user
- Smart Reply — 3 auto-generated chip suggestions above the keyboard in thread view
- Thread view: collapsible quoted replies, in-thread inline compose at bottom

## 2. Color Palette & Roles

### Primary
- **Gmail Red** (`#D93025`): The Compose FAB, destructive "Delete forever" actions, error toast backgrounds, the Gmail "M" logo accent.
- **Gmail Red Pressed** (`#B3261E`): Pressed state for the Compose FAB.
- **Gmail Red Subtle** (`#FCE8E6`): Selected/hover tint for destructive rows (appears on "Delete" confirmation dialogs).

### Google Brand Palette (Applied to Chips, Labels, Avatars)
- **Google Red** (`#EA4335`): Category "Primary" indicator, avatar fallback for senders whose name starts A-F.
- **Google Yellow** (`#FBBC04`): Starred emails, "Important" marker, avatar fallback G-L.
- **Google Green** (`#34A853`): "Verified sender" check, avatar fallback M-R.
- **Google Blue** (`#4285F4`): Link color, Meet-integration chips, avatar fallback S-Z.

### Canvas & Surfaces
- **Canvas Light** (`#FFFFFF`): Primary light canvas — email list, thread view background.
- **Surface Light** (`#F6F8FC`): Section card backgrounds, quoted reply blocks, tonal buttons.
- **Surface Elevated Light** (`#FFFFFFF`): Hovered rows fall to `#F1F3F4` — very subtle.
- **Surface Pressed Light** (`#F1F3F4`): Tonal-button pressed, row pressed.
- **Divider Light** (`#DADCE0`): Hairline dividers between email rows on thread view; omitted in inbox list (rows separated by whitespace only).

### Text
- **Text Primary Light** (`#202124`): Primary body — sender names, subjects, body copy.
- **Text Secondary Light** (`#5F6368`): Snippets, timestamps, metadata, secondary UI.
- **Text Disabled Light** (`#9AA0A6`): Placeholder, disabled state, low-emphasis labels.
- **Text Link** (`#1A73E8`): Blue hyperlinks in email body — slightly deeper blue than Google Blue.

### Dark Mode
- **Dark Canvas** (`#202124`): Primary dark background — a near-black charcoal (Google's dark-mode standard, NOT pure `#000`).
- **Dark Surface 1** (`#28292C`): Elevated surfaces, tonal button backgrounds.
- **Dark Surface 2** (`#303134`): Higher elevation — quoted blocks, sheets, modals.
- **Dark Divider** (`#3C4043`): Hairline dividers.
- **Dark Text Primary** (`#E8EAED`): Body text.
- **Dark Text Secondary** (`#BDC1C6`): Metadata, timestamps.
- **Dark Text Disabled** (`#9AA0A6`): Placeholder.
- **Dark Gmail Red** (`#F28B82`): Compose FAB shifts to lighter red on dark for contrast — Google's established dark-mode red.

### Semantic
- **Unread Indicator** (`#D93025`): The 4pt vertical red stripe at the left of an unread email row (only on "Important" or styled variants; default unread is bold text only).
- **Selected Row** (`#E8F0FE`): Row highlight when multi-selected for bulk action.
- **Label Chips**: User-defined — rendered as filled pills with user-picked hex + auto-contrast text (Google's picker offers 24 preset hues).
- **Starred Yellow** (`#F5BA18`): Star-fill color when user stars an email.
- **Snooze Purple** (`#A142F4`): Snooze button accent color.
- **Archive Teal** (`#1E8E3E` or user-picked): Left-swipe archive action color (default green-teal).

## 3. Typography Rules

### Font Family
- **Display / Titles / Buttons**: `Google Sans` (proprietary, by Google) — geometric humanist sans with open apertures and warm curves. The logo face and all nav/button type.
- **Body / UI**: `Roboto` (proprietary, by Google; available free under Apache 2.0 for web) — neutral grotesque optimized for screen at small sizes. All email body, metadata, list rows.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'SF Pro Display', 'Segoe UI', Helvetica, Arial, sans-serif`
- **Monospace (code in emails)**: `Roboto Mono`
- **Non-Latin**: Gmail ships Noto family localizations per script

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Google Sans | 28pt | 400 | 1.2 | 0pt | "Inbox", "Starred" nav large title |
| Screen Title (Compact) | Google Sans | 20pt | 500 | 1.2 | 0.15pt | Compressed title when scrolled |
| Section Header | Google Sans | 13pt | 500 | 1.2 | 0.8pt | "Today", "Yesterday", "This week" — uppercase tracked |
| Email Subject (Unread) | Google Sans | 14pt | 500 | 1.3 | 0pt | Bold when unread, Roboto regular when read |
| Email Sender (Unread) | Google Sans | 14pt | 500 | 1.2 | 0pt | Bold for unread |
| Email Sender (Read) | Roboto | 14pt | 400 | 1.2 | 0pt | Regular for read |
| Email Snippet | Roboto | 13pt | 400 | 1.3 | 0pt | 1-line clamp with ellipsis |
| Thread Subject | Google Sans | 22pt | 400 | 1.25 | 0pt | Thread view hero title |
| Thread Body | Roboto | 15pt | 400 | 1.5 | 0pt | Email body in thread |
| Button (FAB) | Google Sans | 16pt | 500 | 1.0 | 0.1pt | Compose FAB label (on tablet widths) |
| Button (Tonal) | Google Sans | 14pt | 500 | 1.0 | 0.1pt | "Reply", "Reply all", "Forward" |
| Smart Reply Chip | Google Sans | 14pt | 500 | 1.0 | 0pt | Auto-suggestion chips |
| Label Chip | Google Sans | 12pt | 500 | 1.0 | 0.2pt | User label pills |
| Timestamp | Roboto | 13pt | 400 | 1.2 | 0pt | Right-aligned time in row |
| Tab Label | Google Sans | 12pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Meta / Microcopy | Roboto | 12pt | 400 | 1.3 | 0pt | "3 of 1,234", "From: Sarah" |

### Principles
- **Two families, clear division**: Google Sans carries emotion and structure (titles, buttons, section headers, labels); Roboto carries information (email body, snippets, metadata). Never mix them within a single text block.
- **Unread = weight change**: Bold sender + bold subject when unread; regular weight when read. The weight is the dominant unread signal, NOT color change.
- **Weight concentrated at 400 / 500 / 700**: Roboto Regular, Google Sans Medium for emphasis, Bold only for Smart Compose highlights.
- **Dynamic Type first-class**: Body, subjects, and snippets scale; section headers, tab labels, timestamps stay fixed.
- **Section headers tracked**: "TODAY", "YESTERDAY" at 0.8pt letter-spacing — gives the temporal ruler a calendar-like signal.

## 4. Component Stylings

### Buttons

**Compose FAB (The Gmail Red Button)**
The single most recognizable Gmail component. A floating red square with a white plus icon.
- Shape: Rounded square, 16pt corner radius (Material You squircle feel, not a pure circle)
- Size: 56pt × 56pt on iPhone; extended to 96pt × 56pt with "Compose" label visible on tablet widths and in landscape
- Background: `#D93025` (Gmail red)
- Icon: SF Symbol `pencil` or `square.and.pencil` 24pt in white
- Position: fixed bottom-right, 16pt from right edge, 16pt above tab bar (or above safe area on non-tab screens)
- Shadow: `rgba(0,0,0,0.2) 0 4px 12px`
- Pressed: background `#B3261E`, scale 0.96, medium haptic
- Scroll behavior: on downward scroll, FAB shrinks to icon-only (56×56), re-expands on scroll-up
- Used only for: creating a new email

**Tonal Button (Reply / Reply All / Forward)**
- Shape: Pill, 500pt corner radius
- Background: `#F6F8FC` (Surface Light) in light, `#28292C` in dark
- Text: `#1A73E8` (link blue) — this is the one place link blue appears as button text
- Padding: 10pt vertical, 24pt horizontal
- Font: Google Sans, 14pt, weight 500
- Pressed: background `#E1E5EA` + scale 0.98
- Leading SF Symbol: `arrowshape.turn.up.left` (Reply), `arrowshape.turn.up.left.2` (Reply all), `arrowshape.turn.up.right` (Forward) — 18pt

**Icon Button (Archive / Delete / Mark Unread)**
- 40pt circular hit area, 24pt SF Symbol
- Default color: `#5F6368` (Text Secondary)
- Pressed: 36pt circle background in `#F1F3F4` appears underneath on tap
- Icons: `archivebox` (archive), `trash` (delete), `envelope.badge` (mark unread), `star` / `star.fill` (star)

**Smart Reply Chip**
- Shape: Pill, 500pt corner radius, 1pt outline `#DADCE0`
- Background: transparent (or `#F6F8FC` on tablet)
- Text: `#1A73E8` (link blue)
- Padding: 10pt vertical, 16pt horizontal
- Font: Google Sans, 14pt, weight 500
- Displayed in a horizontal scrolling row above the keyboard in thread view
- Tap: inserts suggestion as reply text and focuses compose box

### Cards & Containers

**Email List Row (Primary inbox row)**
- Height: 72pt (fixed)
- Background: `#FFFFFF` (or `#F1F3F4` when selected for bulk)
- Padding: 16pt horizontal
- Structure (leading to trailing):
  1. Leading: 36pt circular avatar — photo or initial on colored fallback background
  2. 12pt gap, then text column (flex)
     - Top line: Sender name (14pt w500 Google Sans if unread, w400 Roboto if read)
     - Bottom line: Subject (14pt, same weight as sender) + " — " + Snippet (13pt w400 `#5F6368`) wrapped in single line with ellipsis overflow
  3. Trailing: timestamp (13pt `#5F6368`), 2pt gap, Star icon (24pt)
  4. Unread indicator: if unread, sender + subject bold; optional 3pt `#D93025` vertical bar at leading edge of row (user-configurable "Important markers")
- Swipe left: reveals archive action pane (teal `#1E8E3E` background + archive icon)
- Swipe right: reveals delete action pane (red `#D93025` background + trash icon)
- Tap: opens thread view
- Press-hold: multi-select mode — avatar transforms to checkmark, row shows `#E8F0FE` selected state

**Email Avatar**
- 36pt circle
- Photo if available (remote image with placeholder)
- Fallback: initial letter in white, 16pt Google Sans 500, over colored circle (Google Red/Yellow/Green/Blue based on hash of sender email)

**Thread View Header**
- Height: 120pt (variable, grows with subject length)
- Background: `#FFFFFF`
- Subject: 22pt w400 Google Sans, 2-line clamp
- Below: Label chip row (user labels applied to thread) — horizontal scroll
- Star icon trailing

**Thread Email Card (expanded)**
- Background: `#FFFFFF`
- Top: sender avatar + name/email + timestamp + "to me" expand row
- Body: full email content, 15pt Roboto regular, with inline images and inline replies
- Bottom: Reply / Reply all / Forward tonal buttons row
- Collapsed (previous emails in thread): shows just sender + date on one 56pt row, ellipsis icon to expand

**Quoted Reply Block**
- Background: `#F6F8FC` (Surface Light)
- Left border: 3pt `#DADCE0` solid
- Padding: 12pt
- Font: Roboto 14pt w400 `#5F6368` (slightly darker on dark mode)
- Collapsed by default with "..." toggle at top to expand

**Label Chip (applied to thread)**
- Background: user-picked hex with 10% opacity
- Border: 1pt user hex
- Text: user hex
- Padding: 4pt vertical, 10pt horizontal
- Font: Google Sans 12pt w500
- Corner radius: 500pt (pill)

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` (light) / `#202124` (dark) with 0.5pt top border `#DADCE0` / `#3C4043`
- Tabs (2 default, up to 4 configurable): Mail, Chat, Spaces, Meet
- Icon size: 24pt
- Active tab: icon fills with `#1A73E8` (link blue) + `#E8F0FE` pill background tint behind icon (56pt wide × 32pt tall, 500pt radius) — Material You active-indicator treatment
- Inactive: icon `#5F6368`
- Labels: Google Sans 12pt w500 below icon (`#1A73E8` when active, `#5F6368` when inactive)

**Top Nav (Inbox)**
- Height: 56pt + safe area (collapses from 120pt when large-title is scrolled)
- Leading: menu hamburger icon (24pt `#202124`) — opens the left drawer with Inbox / Starred / Snoozed / Sent / Drafts / All Mail / Spam / Trash / Labels list
- Center: search bar (40pt tall, `#F6F8FC` fill, 500pt radius, leading magnifyingglass 18pt, placeholder "Search in mail" 14pt w400 `#5F6368`)
- Trailing: profile avatar 28pt (tap opens Google account switcher sheet)

**Top Nav (Thread)**
- Height: 56pt + safe area
- Leading: back chevron 24pt `#5F6368`
- Trailing action row: archive icon, trash icon, mark-unread icon, snooze icon, ellipsis — all 24pt, 40pt hit, 8pt gap

**Left Drawer (Mailboxes)**
- Full-height sheet sliding in from left, 320pt wide (or 80% screen width on small devices)
- Background: `#FFFFFF`
- Header: large Gmail logo + account avatar + email address
- Mailbox rows: 52pt tall, leading icon (24pt) + label (Roboto 14pt w400) + trailing unread count badge (red circle with white count for urgent, gray for standard)
- Active mailbox: filled icon + `#E8F0FE` row background

### Input Fields

**Search Bar (Top Nav)**
- Height: 40pt
- Background: `#F6F8FC` (Surface Light)
- Corner radius: 500pt (pill)
- Leading icon `magnifyingglass` 18pt `#5F6368`
- Placeholder "Search in mail" 14pt w400 `#5F6368`
- Focus: expand to full-width overlay with cancel button; search chips appear below (From:, To:, Has attachment, etc.)

**Compose Subject Field**
- Height: 48pt
- Background: `#FFFFFF`
- Border bottom: 1pt `#DADCE0`, 2pt `#1A73E8` on focus
- Placeholder: "Subject" 16pt w400 `#5F6368`

**Compose Body**
- Multi-line auto-growing text area
- Background: `#FFFFFF`
- Font: Roboto 15pt w400 `#202124`
- Placeholder: "Compose email"
- Smart Compose (gray ghost-text prediction): 15pt w400 `#9AA0A6` that appears inline as you type; tab/swipe right to accept

### Distinctive Components

**Compose FAB**
Red squircle (56×56, 16pt radius) bottom-right with white pencil icon. The signature element. Shrinks to icon-only on scroll-down, expands back on scroll-up. This is THE Gmail.

**Swipe Actions (Archive / Delete)**
- Left swipe on email row: row slides left revealing a teal `#1E8E3E` archive pane with white `archivebox` icon; full swipe archives, partial swipe confirms with the action label visible
- Right swipe: red `#D93025` delete pane with `trash` icon
- User can customize left/right actions (Archive, Delete, Mark as read, Move, Snooze) in Settings

**Smart Reply**
- In thread view above keyboard, a horizontal row of 3 chip-suggestions auto-generated by ML
- Each chip is a Smart Reply Chip (see above)
- Tap: inserts text into compose field, sends automatically after brief edit-window or on confirmation

**Star (Email Marker)**
- SF Symbol `star` (outline) → `star.fill` filled yellow `#F5BA18` when starred
- 24pt glyph, trailing position in email list rows and thread view
- Tap: toggle state + soft haptic

**Smart Compose (Inline Prediction)**
- While composing, gray ghost text appears after cursor: "Thanks for reaching out." ghost-gray `#9AA0A6`
- Accept: tab key or swipe-right on iOS
- Dismiss: keep typing, the ghost disappears

**Account Switcher Sheet**
- Tap profile avatar in top nav → opens bottom sheet showing all Google accounts
- Each row: large avatar 40pt + name + email address + "Manage your Google Account" link
- Trailing action: "Add another account", "Sign out of all accounts"

**Label Color Picker (when labeling thread)**
- Bottom sheet with 24 preset color swatches in a 6-column grid, 40pt circles, 8pt gap
- Selected swatch gets white check icon overlay
- Below: "Create new label" text input

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 56, 72, 96
- Email row: 16pt horizontal insets, 72pt fixed height
- Section gap: 24pt between inbox section headers
- Sheet internal padding: 16pt horizontal, 20pt vertical

### Grid & Container
- iPhone: full-width email rows (0pt horizontal margin), 16pt internal row padding
- iPad: 2-column split-view (left: mailbox list + inbox, right: thread view), edge gap 24pt
- Compose sheet: modal from bottom, 88% screen height
- Settings list: standard iOS grouped list with 16pt section insets

### Whitespace Philosophy
- **Dense inbox rows**: 72pt per row packs roughly 10 emails per viewport — browsing speed prioritized
- **Breathable thread view**: 24pt vertical padding around email body; quoted replies collapsed to save vertical space
- **FAB breathing room**: 16pt from right edge, 16pt above tab bar — never crowded into corner
- **Compose is spacious**: the subject and body get full screen width with 16pt insets, no chrome competing

### Border Radius Scale
- Square (0pt): Inbox row (full-bleed), email avatars-fallback-background is circle
- Soft (4pt): Inline attachment tiles inside email body
- Icon Squircle (8pt): Category tab indicators, small chips
- FAB Squircle (16pt): Compose FAB corner radius — Material You signature
- Comfortable (20pt): Bottom sheets, context menu cards
- Pill (500pt): All buttons (tonal, Smart Reply, tab active indicator), label chips
- Circle (50%): Avatars, icon button backgrounds on tap

## 6. Depth & Elevation

Gmail follows Material Design elevation — shadows are soft, layered, and communicate z-axis hierarchy.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Email list rows, thread body, drawer rows |
| Level 1 | `rgba(0,0,0,0.05) 0 1px 2px` + `rgba(0,0,0,0.1) 0 1px 3px 1px` | Elevated cards, top nav when scrolled |
| Level 2 | `rgba(0,0,0,0.1) 0 1px 2px` + `rgba(0,0,0,0.15) 0 2px 6px 2px` | Snooze / label picker sheets |
| Level 3 | `rgba(0,0,0,0.1) 0 4px 8px 3px` + `rgba(0,0,0,0.15) 0 1px 3px` | Compose FAB, floating menus |
| Level 4 | `rgba(0,0,0,0.1) 0 6px 10px 4px` + `rgba(0,0,0,0.15) 0 2px 3px` | Modal dialogs, full-screen sheets |
| Scrim | `rgba(0,0,0,0.32)` | Behind drawer and modal sheets |

**Shadow Philosophy**: Gmail's shadows are layered (two shadows per elevation — a crisp close shadow + a softer distant one) to mimic Material's physically-based depth model. The FAB uses Level 3 so it always feels "lifted" above the inbox surface. Dark mode slightly reduces shadow opacity (from `0.15` to `0.3` on the distant shadow) because pure black-on-black shadows are invisible; Material switches to a subtle surface-tint instead.

### Motion
- **FAB tap**: scale 0.96 → 1.0 spring 300ms damping 0.7 + `.impactOccurred(.medium)` haptic
- **FAB scroll behavior**: on downscroll, width animates from 120pt → 56pt in 200ms ease-out; on upscroll, reverses
- **Swipe action**: 60fps pan gesture; threshold of 40% screen width commits the action; under 40% snaps back with 250ms spring
- **Row tap to thread**: row stays in place; thread view slides in from right over 300ms ease-out; no shared-element transition
- **Star tap**: icon swaps state + scale 1.0 → 1.2 → 1.0 spring 300ms + `.impactOccurred(.light)`
- **Archive completion**: archived row collapses vertically (height 72 → 0) over 300ms ease-in-out, adjacent rows shift up
- **Smart Reply chip tap**: chip ripple animates + text fades into compose box over 200ms
- **Pull-to-refresh**: Gmail "M" logo in red swirls as circular progress indicator

## 7. Do's and Don'ts

### Do
- Use pure white (`#FFFFFF`) as the light canvas — content is the color, not the chrome
- Position the Compose FAB bottom-right at 16pt from edges — never inline, never centered
- Use Gmail red (`#D93025`) ONLY for the Compose FAB and destructive actions — it's sacred
- Apply the Google 4-color palette to avatars, category chips, Smart Reply chips — never to structural UI
- Use Google Sans for titles/buttons, Roboto for body/metadata — don't mix within a block
- Make unread state bold weight on sender + subject — color-change alone is insufficient
- Implement left-swipe archive, right-swipe delete — with user-configurable actions in Settings
- Show Smart Reply chips above keyboard in thread view — Google's ML affordance
- Layer shadows (close + distant) following Material elevation model
- Use the 16pt corner radius squircle shape on the FAB — Material You signature

### Don't
- Don't use pure black `#000000` on dark mode — use `#202124` (Google's standard)
- Don't color the inbox chrome — the FAB is the only chromatic element; everything else is grayscale
- Don't use circles for the Compose FAB — it's a 16pt squircle, not a pure circle
- Don't show red anywhere outside of destructive actions and the FAB
- Don't mix Google Sans and Roboto in the same text block — clear division
- Don't add drop-shadows to inbox rows — they're flat; separation is whitespace only
- Don't use bold weight liberally — it's the unread signal and must stay meaningful
- Don't hide the Compose FAB — even on scroll, it shrinks to icon-only but stays visible
- Don't use true-gold for starred (`#F5BA18` is the correct Gmail yellow for stars, not `#FFD700`)
- Don't treat the bottom tabs as always-visible — user can hide Chat/Spaces/Meet entirely

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Inbox row clamps to 2-line sender+snippet overflow; tab bar labels may hide |
| iPhone 13/14/15 | 390pt | Standard 72pt rows, 56pt FAB |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | FAB expands to Extended 96×56pt with "Compose" text visible |
| iPad | 768pt+ | Split view: mailbox list (left 320pt), inbox (center ~350pt), thread (right flex). FAB extended with "Compose" label. |

### Dynamic Type
- Email body, subjects, snippets, thread content: full scale
- Section headers, timestamps, tab labels, Smart Reply chips, label chips: FIXED (layout-sensitive)
- FAB icon: fixed size, FAB width adjusts

### Orientation
- iPhone inbox: portrait-preferred, rotates to landscape with same row layout
- Thread view: fully rotates
- iPad: fully rotates with split-view layout adjusting

### Touch Targets
- Compose FAB: 56pt — impossible to miss
- Email row: 72pt full-row tap
- Icon buttons in top nav: 24pt glyph, 40pt hit
- Tab bar: 24pt icon, 56pt effective
- Star toggle: 24pt icon, 44pt hit

### Safe Area Handling
- Top: top nav respects safe area and Dynamic Island
- Bottom: tab bar + home indicator respected; FAB floats above tab bar with 16pt clearance
- Compose keyboard: body area adjusts with `keyboardAvoidingView`, FAB hidden when compose sheet presented
- Sides: 16pt content inset on non-inbox screens; inbox is full-bleed

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#202124`
- Surface 1 (light): `#F6F8FC`
- Divider: `#DADCE0`
- Text primary: `#202124` light / `#E8EAED` dark
- Text secondary: `#5F6368` light / `#BDC1C6` dark
- Link blue: `#1A73E8`
- Gmail red (FAB): `#D93025`
- Gmail red pressed: `#B3261E`
- Google Red (brand): `#EA4335`
- Google Yellow: `#FBBC04`
- Google Green: `#34A853`
- Google Blue: `#4285F4`
- Starred yellow: `#F5BA18`
- Archive teal: `#1E8E3E`

### Example Component Prompts
- "Create the Gmail Compose FAB in SwiftUI: 56pt × 56pt rounded square with 16pt corner radius, `#D93025` background, white `square.and.pencil` SF Symbol at 24pt centered, shadow `rgba(0,0,0,0.2) 0 4px 12px`. Position: fixed bottom-right, 16pt inset from edges, 16pt above safe-area/tab-bar. Pressed: background `#B3261E`, scale 0.96, medium haptic. On tablet widths: expand to 96×56pt with 'Compose' label in Google Sans 16pt weight 500 white."

- "Build a Gmail email list row: 72pt tall, `#FFFFFF` background, 16pt horizontal padding. Leading: 36pt circular avatar (colored fallback with white initial in Google Sans 16pt w500 — color chosen by hash of sender email from Google Red/Yellow/Green/Blue). 12pt gap, then text column: sender name 14pt Google Sans w500 (bold if unread, regular Roboto if read) — then below it subject + ' — ' + snippet in 13pt Roboto w400 `#5F6368` (clamp 1 line). Trailing: timestamp 13pt `#5F6368`, 8pt gap, star icon (outline `#5F6368` or fill `#F5BA18` if starred). Left swipe reveals teal `#1E8E3E` archive pane; right swipe reveals red `#D93025` delete pane."

- "Design the Gmail bottom tab bar: 56pt + safe area, `#FFFFFF` / `#202124` dark, 0.5pt top border `#DADCE0`. 4 tabs: Mail (envelope), Chat (bubble.left), Spaces (person.3), Meet (video). Active tab: icon tinted `#1A73E8` with `#E8F0FE` pill background (56pt × 32pt, 500pt radius) behind the icon — Material You active-indicator. Inactive icon `#5F6368`. Labels below icons in Google Sans 12pt w500 matching tint."

- "Render the Gmail thread view: top nav with back chevron + archive/trash/mark-unread/snooze/ellipsis icon row. Subject in Google Sans 22pt w400 `#202124`, 2-line clamp. Below: label chips row. Then email card: sender avatar (36pt) + name + email + timestamp + 'to me' expand, followed by email body in Roboto 15pt w400 line-height 1.5. Quoted replies in `#F6F8FC` block with 3pt `#DADCE0` left border, collapsed by default with '...' toggle. Bottom: tonal buttons Reply / Reply all / Forward — pill shape, `#F6F8FC` bg, `#1A73E8` text, leading SF Symbol arrow."

- "Build the Gmail Smart Reply row: horizontal scrolling row of 3 pill chips above the keyboard in thread view. Each chip: 500pt radius, 1pt `#DADCE0` border, transparent background, `#1A73E8` text in Google Sans 14pt w500, 10pt vertical + 16pt horizontal padding. Tap: inserts suggestion into compose text field + soft haptic. Space between chips: 8pt."

- "Create a Gmail label chip: pill shape 500pt radius, user-picked hex at 10% opacity fill, 1pt border at full hex, text at full hex in Google Sans 12pt w500, 4pt vertical + 10pt horizontal padding. Applied inline below thread subject (horizontal scrolling row). Tap: opens label management sheet."

### Iteration Guide
1. Canvas is pure white `#FFFFFF` in light, charcoal `#202124` in dark — NOT true black, NOT pure off-white
2. Gmail red `#D93025` is reserved for the Compose FAB and destructive actions — nothing else
3. The Compose FAB is a 16pt corner-radius squircle (Material You), NOT a circle — bottom-right, 56×56
4. Google Sans for titles + buttons + labels; Roboto for body + metadata — never mix within a block
5. Unread state = bold weight change (Google Sans on sender + subject), NOT color alone
6. Inbox rows are flat — no shadow; separation is whitespace between rows
7. Shadows are layered (close + distant) per Material elevation model — FAB at Level 3
8. Swipe left archive (teal `#1E8E3E`), swipe right delete (red `#D93025`) — user-configurable
9. Smart Reply chips are `#1A73E8` text on transparent outline pill — above keyboard in thread
10. Tab bar active indicator is a `#E8F0FE` pill background behind the icon — Material You signature
