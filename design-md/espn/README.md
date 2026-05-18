# ESPN iOS Inspired Design System

Design system docs inspired by the [ESPN iOS app](https://apps.apple.com/us/app/espn-live-sports-scores/id317469184). Not the official system. Brand colors, font names, and component patterns are cross-referenced with ESPN's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, scores ticker + game card + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Near-black canvas** (`#0E0F11`) — NOT pure black, so the ticker and game cards lift off it
- **Horizontal scores ticker** pinned near the top — the fastest path to "what's the score"
- **Single brand accent** — ESPN Red (`#D50A0A`, bright `#CC0000`) for logo, CTA, active-tab dot, tags
- **Live state** — a hotter red (`#FF1A1A`) with a pulsing dot; finals dim to gray
- **Winning score turns green** (`#1FAA59`) — a finished result readable at a glance
- **Game card** — league header + two team rows (logo / name / record / score) + clock & ESPN+ footer
- **Tabular numerals** on every score, clock, record, and ranking — columns must align
- **Archivo** (ESPN-style bold condensed sans) at heavy weights — built for scanning, not reading
- **Red-dot active tab** — white icon with a small `#D50A0A` dot beneath; never a tint pill
- **SportsCenter feed** — interleaved live cards, breaking news, highlights, analysis at 16pt insets
- **Information density** — ESPN packs maximum signal per glance; airy layouts feel un-ESPN
- **Calm motion + two scoreboard cues** — the live pulse and the score pop are the only lively beats

## Brand Sources

- [ESPN](https://www.espn.com/)
- [ESPN Press / Brand](https://espnpressroom.com/) — ESPN Red `#D50A0A`
- [Archivo by Omnibus-Type](https://fonts.google.com/specimen/Archivo) — SIL OFL (closest free analog to ESPN's proprietary face)
- Public brand palette: ESPN Red `#D50A0A` / bright `#CC0000`, near-black `#0E0F11` canvas, live red `#FF1A1A`, win green `#1FAA59`
