# Design System Inspiration of Google Calendar (iOS)

## 1. Visual Theme & Atmosphere

Google Calendar's iOS app is the densest information surface in the Google portfolio — a week of meetings, birthdays, reminders, and Goals compressed into a single phone screen without ever feeling cramped. The design philosophy is pure Material You with iOS manners: clean white surfaces, a tightly disciplined four-color event palette derived from Material primary tones, generous internal whitespace inside each event card, and the floating "+" button — the Material FAB — anchored bottom-right on every view. Where Apple's native Calendar leans on system gray and red restraint, Google Calendar leans into color as content: every event is a tinted rectangle, every category gets a hue.

The accent palette is the Material primary set, slightly desaturated for iOS — Google Blue (`#1A73E8`) for events, Red (`#D93025`) for declined, Yellow (`#F9AB00`) for tasks, Green (`#188038`) for goals. These four colors don't compete with the white canvas; they live inside event cards as left-bar indicators or 12pt tint blocks. Calendars (Work, Personal, Family) inherit their own user-chosen color from Google's full 24-swatch calendar palette (Tomato, Tangerine, Banana, Sage, etc.). The floating "+" FAB is Material Blue (`#1A73E8`), centered with the iconic concentric expansion ripple when tapped.

Typography is Google Sans (Product Sans's successor for product surfaces) for nav titles and prominent moments, Roboto for body content — but on iOS, the team intentionally falls back to SF Pro Display / SF Pro Text on most rows to feel native. The headline weight stack runs Regular (400) and Medium (500) — Google Calendar deliberately avoids Bold; the visual weight comes from color saturation and Material elevation, not type weight. Times in event cards use tabular figures so columns of "9:00 AM / 10:30 AM / 12:00 PM" align mathematically.

**Key Characteristics:**
- White canvas (`#FFFFFF`) with Surface Gray (`#F1F3F4`) section fills
- Material primary palette as the event color system: Blue `#1A73E8`, Red `#D93025`, Yellow `#F9AB00`, Green `#188038`
- 24-color user-selectable calendar palette (Tomato `#D50000`, Lavender `#7986CB`, Sage `#33B679`, etc.)
- Schedule, Day, 3-Day, Week, Month — five view modes accessible from a top-left hamburger menu
- The floating "+" FAB — bottom-right on every view, Material Blue with concentric ripple
- Event card: rounded 8pt rectangle with a 4pt left color bar OR a tinted fill — title 14pt Medium, time row 13pt Regular tabular
- Calendar tap-and-hold for quick event creation; long-press to drag/resize
- Google Sans Display for nav titles, Roboto/SF Pro Text for body
- The Schedule view — chronological list of events grouped by day, with day banners showing date and weather
- Material elevation: white surface (Level 0), event card (Level 1), FAB (Level 6 — the highest)

## 2. Color Palette & Roles

### Primary
- **Google Blue** (`#1A73E8`): Primary action — FAB, "Save" buttons, default event color, links, "Today" date highlight ring.
- **Blue Pressed** (`#1557B0`): Pressed/active state on the FAB and primary buttons.
- **Blue Tint** (`#E8F0FE`): Selected-state row backgrounds, "Today" ring background, low-emphasis containers.

### Event Colors (Material Primary Set)
- **Event Blue** (`#1A73E8`): Default calendar color, work meetings.
- **Event Red** (`#D93025`): Declined events, alerts, important meetings.
- **Event Yellow** (`#F9AB00`): Tasks, reminders.
- **Event Green** (`#188038`): Goals, completed sessions, focus blocks.

### Full Calendar Palette (User-Selectable, 24 Swatches)
These are the Google Calendar canonical user calendar colors:
- **Tomato** `#D50000`, **Flamingo** `#E67C73`, **Tangerine** `#F4511E`, **Banana** `#F6BF26`
- **Sage** `#33B679`, **Basil** `#0B8043`, **Peacock** `#039BE5`, **Blueberry** `#3F51B5`
- **Lavender** `#7986CB`, **Grape** `#8E24AA`, **Graphite** `#616161`, **Birch** `#A79B8E`
- **Eucalyptus** `#16A765`, **Cobalt** `#0277BD`, **Pumpkin** `#F4511E`, **Cherry Blossom** `#FAD165`
- **Avocado** `#7CB342`, **Citron** `#C0CA33`, **Pistachio** `#9CCC65`, **Wisteria** `#5C6BC0`
- **Amethyst** `#AB47BC`, **Rose** `#D81B60`, **Cocoa** `#795548`, **Slate** `#9E9E9E`

### Canvas, Surfaces & Dividers
- **Canvas White** (`#FFFFFF`): Primary canvas — Schedule list, Month grid, Day timeline.
- **Surface Gray** (`#F1F3F4`): Day header banners, section dividers, search field fill.
- **Surface Gray 2** (`#F8F9FA`): Subtle row alternation in Schedule view, sidebar drawer.
- **Divider** (`#DADCE0`): Hairline borders, 30-min gridlines in Day/Week view, calendar list divisions.
- **Shadow Black** (`rgba(60,64,67,0.15)`): Standard Material Level 1 elevation (slightly warm gray).

### Text
- **Ink** (`#202124`): Primary text — event titles, day numbers, nav titles. Google's near-black, slightly warm.
- **Secondary** (`#5F6368`): Times, location, secondary labels.
- **Tertiary** (`#80868B`): Placeholder, "All-day" labels in event details, disabled.
- **Inverse White** (`#FFFFFF`): Text inside saturated event cards.

### Semantic
- **Success Green** (`#188038`): "Event created" toast, confirmation banners.
- **Warning Amber** (`#F9AB00`): Sync warnings, "Time conflict" badges.
- **Error Red** (`#D93025`): Form validation, "Failed to RSVP".
- **Info Blue** (`#1A73E8`): Hyperlinks in event description, "Join with Google Meet" CTA.

### Dark Mode
Google Calendar's dark mode is a true near-black (`#202124`) — Google's signature warm-tinted dark gray, NOT pure black. Event tints brighten significantly.
- **Dark Canvas** (`#202124`): Primary dark background.
- **Dark Surface 1** (`#2D2E30`): Cards, day banners, search field.
- **Dark Surface 2** (`#3C4043`): Pressed states, selected rows.
- **Dark Divider** (`#3C4043`): Hairlines, gridlines.
- **Dark Text Primary** (`#E8EAED`): Event titles, nav titles.
- **Dark Text Secondary** (`#9AA0A6`): Times, secondary labels.
- **Google Blue (dark)** (`#8AB4F8`): Brighter blue for dark mode contrast — used on FAB, links, "Today" highlight.

## 3. Typography Rules

### Font Family
- **Display / Brand**: `Google Sans Display` for nav titles, dialog titles, marketing moments
- **Text**: `Roboto` on Android; on iOS deliberately falls back to `SF Pro Text` for body and rows (feels more native)
- **Weights available**: Regular (400), Medium (500) — Google Calendar avoids Bold on most surfaces
- **Fallback stack**: `'Google Sans', 'Google Sans Display', -apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', Roboto, sans-serif`
- **Web/marketing Google Fonts substitute**: `Google Sans` is available via Google Fonts (limited public release) — otherwise `Roboto` is the safe stand-in
- **Tabular figures**: ALL times in event cards use `font-variant-numeric: tabular-nums` so columns align — this is non-negotiable

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Nav Title (Month/Day) | Google Sans | 22pt | 500 | 1.2 | 0pt | "May 2026", "Today" |
| Schedule Day Banner | Google Sans | 36pt | 400 | 1.0 | -0.4pt | Large day number "14" |
| Schedule Day Label | Google Sans | 13pt | 500 | 1.2 | 0.8pt UPPERCASE | "THU · MAY" |
| Section Header | Google Sans | 14pt | 500 | 1.3 | 0pt | "Tasks", "Reminders" |
| Event Title (in card) | SF Pro Text | 14pt | 500 | 1.3 | 0pt | "Stand-up", "Lunch with K." |
| Event Title (Detail) | Google Sans | 22pt | 400 | 1.25 | -0.1pt | Event title at the top of the detail screen |
| Event Time (in card) | SF Pro Text | 13pt | 400 | 1.2 | 0pt tnum | "9:00 – 9:30 AM" |
| Event Location | SF Pro Text | 13pt | 400 | 1.3 | 0pt | "Conference Room B" |
| Day Number (Month Grid) | Google Sans | 14pt | 400 | 1.0 | 0pt tnum | The "14" in the month cell |
| Today Number | Google Sans | 14pt | 500 | 1.0 | 0pt | The number inside the blue "Today" ring |
| Weekday Header (M T W T F S S) | SF Pro Text | 12pt | 500 | 1.0 | 0.4pt UPPERCASE | "S M T W T F S" above the grid |
| Time Gutter (Day/Week) | SF Pro Text | 11pt | 400 | 1.0 | 0pt tnum | "9 AM" labels in the left gutter |
| Sidebar Item | SF Pro Text | 14pt | 500 | 1.25 | 0pt | "Schedule", "Day", "Week" in drawer |
| Button (Primary) | Google Sans | 14pt | 500 | 1.0 | 0.4pt | "SAVE", "JOIN WITH GOOGLE MEET" |
| Button (Secondary) | Google Sans | 14pt | 500 | 1.0 | 0.4pt | Outline buttons, same metrics |
| FAB | (no glyph text) | – | – | – | – | "+" SF Symbol 24pt |
| Tab Label | SF Pro Text | 10pt | 500 | 1.0 | 0.1pt | Bottom nav (iPad mostly) |
| Caption | SF Pro Text | 12pt | 400 | 1.3 | 0pt | "Goal completed" labels |

### Principles
- **Avoid Bold**: Hierarchy comes from size + color saturation, not weight. Almost everything is Regular (400) or Medium (500).
- **Tabular numerals everywhere times appear** — event cards, day numbers, time gutter, year labels.
- **Google Sans for display, SF Pro Text for body on iOS**: this is the deliberate hybrid stack Google uses to feel native.
- **UPPERCASE labels at 0.4-0.8pt tracking** on day banners, weekday headers, primary CTA buttons (`SAVE`, `JOIN`).
- **Ink `#202124` over pure black**: Google's slightly warm primary text — gentler than `#000000`, characteristically Google.
- **Color is the differentiator, not weight**: a Work event and a Personal event have the same type metrics; the difference is the calendar's color, applied as a 4pt left bar.
- **Dynamic Type respected on event titles, body, nav titles**; fixed on day numbers, time gutter, weekday headers (layout-sensitive).

## 4. Component Stylings

### The Event Card (The Hero Component)

The single most-repeated visual unit in Google Calendar. Two presentations depending on view:

**Schedule View Card (List)**
- Width: full content width minus 16pt horizontal margins
- Height: variable — minimum 56pt, expands for multi-line titles
- Background: `#FFFFFF`
- Left bar: 4pt wide, full-height, in the calendar's color (e.g., Blueberry `#3F51B5`)
- Padding: 12pt vertical, 16pt horizontal (after the 4pt bar)
- Title: SF Pro Text 14pt Medium `#202124`, 2-line truncate
- Time row: SF Pro Text 13pt Regular tabular `#5F6368` — "9:00 – 9:30 AM"
- Location row (if present): SF Pro Text 13pt Regular `#5F6368` with a pin icon prefix
- Tap: row background fades to `#F1F3F4` for 80ms, opens event detail

**Day / Week View Card (Tinted Block)**
- A tinted rectangle filling its time slot in the timeline
- Background: calendar color at 20% saturation (e.g., `rgba(63,81,181,0.2)` for Blueberry)
- Border: NONE; just the tint
- Left bar: 3pt of the saturated calendar color, full-height
- Padding: 4pt vertical, 6pt horizontal
- Title: SF Pro Text 12pt Medium in the calendar's full-saturation color
- Time row (only if card is tall enough): SF Pro Text 11pt Regular tabular, slightly lower opacity
- Corner radius: 4pt
- Stacking: overlapping events split the available width — 2 events at 50%, 3 at 33%, with a 2pt gap

**Multi-Day Event Bar**
- Spans across multiple days in Month/Week view as a continuous bar
- Pattern: same calendar color tint, full saturation on the leftmost edge, gradient fade is NOT used (always solid)
- Height: 18pt in Month view, 24pt in Week view
- Corner radius: 4pt (only on the outermost edges of the span — internal day cells get sharp corners on the breakpoint)

### Day Banner (Schedule View)

The signature visual unit that gives Schedule view its rhythm.

- Height: 80pt
- Layout: a 64pt × 64pt circular or square zone on the left with the day number; details on the right
- Day number: Google Sans 36pt Regular `#202124`, e.g., "14"
- Day label: Google Sans 13pt Medium UPPERCASE `#5F6368` with 0.8pt tracking, e.g., "THU · MAY"
- Optional weather glyph: 20pt SF Symbol (e.g., `sun.max.fill`) + temperature in 14pt Medium
- "Today" treatment: the day number sits inside a `#1A73E8` blue filled circle, number turns white; tap-target the whole banner
- Padding: 16pt horizontal, 12pt vertical

### Month Grid Cell

- Size: cell width = (screen width - 0pt margins) / 7; height ~52pt on phones
- Day number: Google Sans 14pt Regular tabular `#202124`, top-left of cell with 6pt inset
- Today: number sits inside a 28pt `#1A73E8` filled circle, number turns white and weight bumps to Medium
- Selected day (when tapping a future day): 28pt `#E8F0FE` ring around the number
- Event dots / mini-bars: 2-3 tiny 4pt circles in the calendar's color OR small 2pt height bars representing events; stacked vertically below the number
- Other-month numbers (greyed out): `#80868B`
- Cell tap: opens that day in Day view with spring zoom
- Long-press: opens quick event creation

### Day / Week Timeline

- Time gutter: 56pt wide on the left
- Time labels: SF Pro Text 11pt Regular tabular `#5F6368`, e.g., "9 AM", "10 AM"
- Gridlines: 0.5pt `#DADCE0` horizontal at every hour; subtler 0.25pt at the half-hour (some users disable these)
- Current time indicator: a 2pt red `#D93025` horizontal line across the full width with a 10pt filled circle on the left edge — animates smoothly with the system clock
- Day header row: weekday + date sticky at the top, e.g., "MON 14" — UPPERCASE 12pt Medium

### Floating Action Button (FAB)

- Diameter: 56pt
- Position: bottom-right, 16pt inset from right and bottom (or 16pt above tab bar)
- Background: `#1A73E8` solid Material Blue
- Glyph: SF Symbol `plus` 24pt Regular, color `#FFFFFF`
- Shadow: `rgba(60,64,67,0.15) 0 4px 8px` plus `rgba(60,64,67,0.30) 0 1px 2px` — Material Level 6 dual shadow
- Pressed: lifts to `rgba(60,64,67,0.30) 0 6px 12px`, scale stays 1.0 (Material doesn't scale on press)
- Tap: opens an action menu sheet — "Event", "Task", "Goal" — each as a 48pt row with an icon
- Haptic: `.impactOccurred(.medium)` on tap

### Quick Event Creation Modal

- Trigger: tap the FAB → choose "Event" from the action menu
- Sheet style: full-screen modal from bottom with `#FFFFFF` background
- Top bar: 56pt, with "X" close button left, "Save" text-button right
- Title input: large SF Pro Display 22pt Regular `#202124` placeholder "Add title"
- Time chips below: "Starts Thu, May 14 9:00 AM" and "Ends Thu, May 14 10:00 AM" — each a tap-target to open the time picker
- "All-day" toggle: standard iOS switch in Blue `#1A73E8` accent
- Calendar picker row: 24pt colored dot + calendar name + chevron — tap to switch calendar
- Add guests, Add location, Add description: each a 56pt row with 24pt leading SF Symbol icon
- Reminder row: shows current reminder ("10 minutes before") with edit option
- Tab-down sheet: drag to dismiss

### Sidebar Drawer (Hamburger Menu)

- Width: 280pt; full-height
- Background: `#FFFFFF`
- Header row: Google Calendar logo (28pt) + "Calendar" text in Google Sans 20pt Medium
- View selector: vertical list — Schedule, Day, 3 Days, Week, Month — each a 48pt row with leading icon and label; selected row has `#E8F0FE` background and `#1A73E8` text/icon
- Section divider: 0.5pt `#DADCE0`
- "My calendars" section: 14pt Medium UPPERCASE header `#5F6368` with 0.4pt tracking
- Calendar list rows: 48pt — 18pt color square checkbox + calendar name (14pt Medium); tap toggles visibility, square flips between filled (visible) and outlined (hidden)
- "Other calendars" section follows
- Footer: Settings, Help & feedback — each a 48pt row

### Buttons

**Primary Text Button (Material Style)**
- Background: transparent
- Text: `#1A73E8`, Google Sans 14pt Medium UPPERCASE with 0.4pt tracking — e.g., "SAVE", "JOIN"
- Padding: 8pt vertical, 12pt horizontal
- No border, no fill
- Tap area: 48pt
- Used heavily in the top app bar of the event detail and quick-creation modals

**Filled Button (Less Common — JOIN WITH GOOGLE MEET)**
- Background: `#1A73E8`
- Text: `#FFFFFF`, Google Sans 14pt Medium UPPERCASE 0.4pt tracking
- Padding: 10pt vertical, 24pt horizontal
- Corner radius: 4pt
- Height: 36pt
- Used for the prominent Meet CTA inside event details

**Tonal Button (Soft Blue Variant)**
- Background: `#E8F0FE`
- Text: `#1A73E8`, Google Sans 14pt Medium
- Padding: 10pt vertical, 16pt horizontal
- Corner radius: 4pt
- Used for "RSVP", "Add to my calendar" — less prominent than the filled blue

**Outline Button (Secondary)**
- Background: transparent
- Border: 1pt `#DADCE0`
- Text: `#1A73E8`, Google Sans 14pt Medium
- Padding: 10pt vertical, 16pt horizontal
- Corner radius: 4pt

### Distinctive Components

**Today Highlight Ring (Month/Day)**
- 28pt circle (or 24pt in smaller views)
- Filled `#1A73E8` background, day number turns white and bumps to Medium weight
- Always visible — the user's anchor in time

**RSVP Pills (Event Detail)**
- Three options: "Yes", "No", "Maybe"
- 40pt height pills, horizontal row with 8pt gap
- Selected: filled in calendar color or `#1A73E8`, white text
- Unselected: outlined `#DADCE0`, `#5F6368` text
- Tap toggles, with 200ms ease

**Reminder Bell Glyph**
- SF Symbol `bell.fill` 14pt — appears inline in event cards for events with reminders set
- Color: matches event tint at 60% opacity

**Google Meet "JOIN" Pill**
- Green-blue gradient or solid `#1A73E8` filled pill
- Camera icon + "JOIN WITH GOOGLE MEET" Google Sans 14pt Medium UPPERCASE
- Appears at the top of any event with a video conferencing link
- Pulses subtly when the event start is within 5 minutes

**Goals Progress Ring**
- Concentric ring at the top of a Goal detail
- Outer ring: full saturation green `#188038`, width 8pt
- Progress: percentage filled
- Center: large number "12 of 20" + label "sessions" in Google Sans

## 5. Layout Principles

### Spacing System
- Base unit: 4pt (Material's `dp` rhythm)
- Scale: 4, 8, 12, 16, 24, 32, 40, 48, 56, 64
- Standard horizontal margin: 16pt on phones, 24pt on tablets
- Section vertical gap: 24pt between Schedule day banners; 16pt between sections in the drawer

### Grid & Container
- Content width: full device width minus 0-16pt depending on view (Month grid is edge-to-edge)
- Schedule view: full-width event cards
- Month grid: 7 columns, edge-to-edge, no horizontal padding
- Day timeline: 56pt time gutter + remaining width for events

### Whitespace Philosophy
- **Dense but breathable**: Event cards are tight (12pt padding) but day banners breathe (16pt vertical)
- **Color is the visual rhythm, not whitespace**: alternating calendar colors create vertical rhythm in dense weeks
- **Month grid is uniformly gridded** — every cell is the same size; events shrink to fit
- **The FAB never disappears** — always 16pt from bottom-right, always Material Blue

### Border Radius Scale
- Square (0pt): month grid cells, gridlines
- Tiny (2pt): event card left bars
- Small (4pt): event cards, primary buttons, tinted event blocks
- Standard (8pt): bottom sheet corners, modal dialogs
- Medium (16pt): top of bottom sheet, large action sheet
- Full Pill (500pt): RSVP pills
- Circle (50%): FAB, Today highlight ring, calendar color squares

## 6. Depth & Elevation

Material Design's elevation system, lightly adapted for iOS:

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, schedule day banners, drawer |
| Card (Level 1) | `rgba(60,64,67,0.10) 0 1px 2px` + `rgba(60,64,67,0.15) 0 1px 3px` | Event cards in Schedule view, dialog buttons |
| Popover (Level 2) | `rgba(60,64,67,0.10) 0 2px 4px` + `rgba(60,64,67,0.15) 0 3px 6px` | Tooltip, calendar picker dropdown |
| Sheet (Level 4) | `rgba(60,64,67,0.10) 0 4px 8px` + `rgba(60,64,67,0.15) 0 5px 10px` | Quick-creation modal, drawer when open |
| FAB Rest (Level 6) | `rgba(60,64,67,0.15) 0 4px 8px` + `rgba(60,64,67,0.30) 0 1px 2px` | The FAB at rest |
| FAB Pressed (Level 12) | `rgba(60,64,67,0.30) 0 6px 12px` + `rgba(60,64,67,0.40) 0 2px 4px` | The FAB while pressed |
| Blur Material | Translucent `#FFFFFF` at 92% over content | Sticky app bar when content scrolls beneath |

**Shadow Philosophy**: Google Calendar uses Material Design's dual-shadow elevation system — a sharp 1pt offset for ambient definition + a softer 3pt blur for depth. The shadows are slightly warm (`rgba(60,64,67,...)`), not pure black, which is a Google detail. The FAB sits at Material Level 6 — the highest meaningful elevation in the app — and lifts further when pressed.

### Motion
- **FAB tap**: scale 1.0 → 0.95 → 1.0 over 200ms ease-out; the ripple expands from the tap point outward in a concentric `rgba(255,255,255,0.3)` circle over 300ms
- **Action menu (Event/Task/Goal)**: slides in as a sheet from the FAB position with a 300ms shared-element morph
- **Schedule day banner enter**: subtle fade-in as you scroll, no horizontal slide
- **Month grid tap**: tapped day scales 1.0 → 1.05 → 1.0 with a 250ms spring; transition to Day view is a 350ms zoom into the cell
- **Event detail open**: shared-element transition from the card's position with `matchedGeometryEffect` — the card morphs into the full detail screen over 300ms
- **Today indicator (red line in Day view)**: animates its position every 60 seconds in a 1s ease transition
- **Reminder bell pulse**: subtle 600ms scale 1.0 → 1.1 → 1.0 when a reminder fires
- **RSVP pill switch**: 200ms ease — color fades, scale stays the same
- **Drawer open/close**: 250ms ease-out from the left, with a `rgba(0,0,0,0.32)` scrim over the canvas

## 7. Do's and Don'ts

### Do
- Use Google Blue `#1A73E8` for the FAB, primary CTAs, links, and the Today highlight
- Use the four Material event colors for system events: Blue meetings, Red declined, Yellow tasks, Green goals
- Honor the 24-color user-selectable calendar palette — let user-named calendars choose their tint
- Use Google Sans Display for nav titles and day numbers; SF Pro Text for body and rows on iOS
- Use TABULAR FIGURES on every time and date — "9:00 AM / 10:30 AM" must align
- Render event cards with a 4pt left color bar OR a tinted fill — pick one per view, not both
- Use Material elevation: Level 1 for cards, Level 6 for the FAB at rest
- Place the FAB at bottom-right, 16pt inset, on every primary view
- Use UPPERCASE Google Sans Medium for primary button text — "SAVE", "JOIN"
- Use the slightly-warm Material gray (`rgba(60,64,67,...)`) for shadows, NOT pure black
- Highlight today with a filled `#1A73E8` circle, white number inside

### Don't
- Don't use pure black `#000000` for text — `#202124` is Google's near-black
- Don't bold-weight nav titles or section headers — Google Calendar uses Medium (500), almost never Bold
- Don't apply event colors at full saturation in Day/Week tinted blocks — use 20% saturation for the fill, full saturation for the title text and 3pt left bar
- Don't replace the Material dual-shadow with a single drop-shadow — the dual-shadow IS the Material look
- Don't put the FAB anywhere other than bottom-right
- Don't show event cards without a left color bar — it's the user's calendar-source indicator
- Don't break the 4pt grid system on event tint borders or button radii
- Don't use a 6+pt corner radius on event cards — 4pt is the Material card radius for compact components
- Don't omit tabular figures from time text — column alignment is broken without it
- Don't use the Schedule day banner for a placeholder "no events" day — show a small "No events" caption instead

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Schedule + Day views default; Week view is tight, often horizontally scrollable |
| iPhone 13/14/15 | 390pt | Standard layouts — Week view shows 5 days at once |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected in app bar |
| iPhone 15/16 Pro Max | 430pt | Week view shows full 7 days legibly |
| iPad Mini | 744pt | Sidebar drawer becomes persistent on left; main view fills the rest |
| iPad Pro 11" | 834pt | 3-column: drawer + main view + event detail pane on the right |
| iPad Pro 13" | 1024pt | Full Month view with daily event lists alongside |

### Dynamic Type
- Event titles, body, large nav titles, sidebar items: full scale
- Day numbers in Month grid, time gutter labels, weekday headers, tab labels: fixed (layout-sensitive)
- Schedule day-banner number: scales but clamps at 44pt max

### Orientation
- Portrait: default, Schedule and Day views
- Landscape on iPhone: Week view auto-rotates and becomes the dominant
- iPad: rotates freely; sidebar persistent across orientations

### Touch Targets
- Event card: full-row tap (52pt minimum)
- FAB: 56pt — full button hit
- Today / day-number tap (Month grid): the whole cell ~52pt
- RSVP pill: 40pt height, 88pt width
- Drawer rows: 48pt
- Time-gutter tap (to create event at that hour): 48pt-wide tappable zone in the gutter

### Safe Area Handling
- Top: app bar honors safe area + Dynamic Island
- Bottom: FAB respects safe area + tab bar (if present) — sits 16pt above
- Sides: 0-16pt depending on view

## 9. Quick Reference Cheat Sheet

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface Gray: `#F1F3F4`
- Divider: `#DADCE0`
- Ink (primary text): `#202124`
- Secondary text: `#5F6368`
- **Google Blue**: `#1A73E8`
- **Blue Pressed**: `#1557B0`
- **Blue Tint** (selected): `#E8F0FE`
- **Event Red** (declined): `#D93025`
- **Event Yellow** (tasks): `#F9AB00`
- **Event Green** (goals): `#188038`
- User calendar palette: pick from the 24-color set (Tomato, Tangerine, Banana, Sage, Peacock, Blueberry, Lavender, Grape, etc.)

### Example Component Prompts
- "Create a SwiftUI Google Calendar Schedule day banner: 80pt tall, leading 64pt zone with the day number '14' in Google Sans 36pt Regular `#202124`. Above the number, 'THU · MAY' in Google Sans 13pt Medium UPPERCASE `#5F6368` with 0.8pt tracking. To the right, optional weather glyph `sun.max.fill` 20pt + '72°' in 14pt Medium. If today, the day number sits inside a 56pt `#1A73E8` filled circle, number turns white."
- "Build a Google Calendar event card in Schedule view: full-width minus 16pt margins, 56pt minimum height, white background with a 4pt left color bar in `#3F51B5` Blueberry. Inside: 'Stand-up' in SF Pro Text 14pt Medium `#202124`, then below it '9:00 – 9:30 AM' in SF Pro Text 13pt Regular tabular `#5F6368', then 'Conference Room B' in 13pt Regular `#5F6368` with a `mappin.fill` SF Symbol prefix. Tap fades the row to `#F1F3F4` for 80ms, then opens the event detail."
- "Design the Google Calendar Month grid cell: ~52pt height, day number '14' in Google Sans 14pt Regular tabular `#202124` at top-left with 6pt inset. If today, wrap the number in a 28pt `#1A73E8` filled circle, number turns white and bumps to Medium. Below the number, 2-3 tiny 4pt event dots in the source calendar's color stacked vertically. Other-month numbers in `#80868B`."
- "Build the Google Calendar FAB: 56pt circle, Material Blue `#1A73E8` background, white `plus` SF Symbol at 24pt centered. Material Level 6 dual-shadow: `rgba(60,64,67,0.15) 0 4px 8px` plus `rgba(60,64,67,0.30) 0 1px 2px`. On press, shadow lifts to `0 6px 12px`. Tap opens an action sheet from the FAB position with Event / Task / Goal options. Haptic `.impactOccurred(.medium)`."
- "Create the Google Calendar Day timeline: 56pt time gutter on the left with hourly labels '9 AM', '10 AM' in SF Pro Text 11pt Regular tabular `#5F6368'. Horizontal gridlines at every hour in 0.5pt `#DADCE0`. Events render as tinted rectangles in their calendar color at 20% saturation with a 3pt left bar at full saturation. Current time indicator: a 2pt `#D93025` red line across the full width with a 10pt filled circle on the left edge, updates every 60 seconds."

### Iteration Guide
1. Canvas is `#FFFFFF`; Surface Gray `#F1F3F4` for day banners and section fills
2. Google Blue `#1A73E8` is the action color — FAB, links, primary CTAs, Today highlight
3. Event colors are the Material primaries: Blue (events), Red (declined), Yellow (tasks), Green (goals); plus the full 24-color user calendar palette
4. The FAB at bottom-right with Material Level 6 dual-shadow is non-negotiable
5. Google Sans Display for headlines, SF Pro Text for body on iOS — this is the deliberate hybrid stack
6. Use Medium (500) for emphasis; almost never Bold
7. Tabular figures on all times and day numbers — column alignment matters
8. Material elevation system: Level 1 cards, Level 6 FAB, Level 4 sheets
9. Today is always a filled blue circle with a white number inside
10. Dark mode is `#202124` canvas (Google's warm near-black) with brighter blue `#8AB4F8` for the FAB and links
