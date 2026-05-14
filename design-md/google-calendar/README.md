# Google Calendar iOS Inspired Design System

Design system docs inspired by the [Google Calendar iOS app](https://apps.apple.com/us/app/google-calendar/id909319292). Not the official system. Brand colors, typography (Google Sans Display + SF Pro Text fallback on iOS), and component patterns are cross-referenced with Google's public Material Design 3 guidance and Google Calendar's user-selectable 24-color palette; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, components, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Google Blue** (`#1A73E8`) as the action color — FAB, links, primary buttons, Today highlight ring
- **The Material Level 6 floating "+" FAB** — bottom-right of every view, dual-shadow elevation, concentric ripple on tap
- **Four-color event system** — Material primaries: Blue events, Red declined, Yellow tasks, Green goals
- **24-swatch user calendar palette** — Tomato, Tangerine, Banana, Sage, Peacock, Blueberry, Lavender, Grape, Cocoa, and 15 more
- **Schedule view day banner** — large 36pt day number, "THU · MAY" 13pt Medium UPPERCASE label, optional weather glyph; today gets a filled blue circle
- **Event card with 4pt left color bar** matching the source calendar — title 14pt Medium, time 13pt Regular tabular, location with pin glyph
- **Today highlight ring** — every Month/Day view shows today as a filled `#1A73E8` circle with white number inside
- **Tabular figures everywhere** time or dates appear — column alignment is layout-critical
- **Google Sans Display for nav titles, SF Pro Text for body** — deliberate hybrid stack on iOS to feel native
- **Material dual-shadow elevation system** — Level 1 for cards, Level 6 for the FAB at rest

## Brand Sources

- [Material Design 3 — Color System](https://m3.material.io/styles/color/system/overview)
- [Material Design — Elevation](https://m3.material.io/styles/elevation/overview)
- [Google Sans Font on Google Fonts](https://fonts.google.com/specimen/Google+Sans)
- [Google Calendar Help — Calendar colors](https://support.google.com/calendar/answer/37095)
- Public brand palette: Google Blue `#1A73E8`, Event Red `#D93025`, Event Yellow `#F9AB00`, Event Green `#188038`; Ink `#202124`, Secondary `#5F6368`, Surface `#F1F3F4`
