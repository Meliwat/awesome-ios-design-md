# Strong iOS Inspired Design System

Design system docs inspired by the [Strong iOS app](https://apps.apple.com/us/app/strong-workout-tracker-gym-log/id464254577). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Strong's public product UI and App Store assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, set-log table + rest bar |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Flat near-black canvas** (`#1A1A1A`) dark-first — true *neutral* grey, "instrument panel" feel
- **The set-log table is the interface** — stacked exercise cards, each a dense Set/Previous/kg/Reps/✓ grid
- **All numerics in tabular figures** so the set table never reflows as digits change
- **Strong Blue** (`#2F80ED`) is the *single* accent — Finish, active tab, exercise titles, rest-timer fill, links
- **`Previous` column** on every set — last session's weight×reps inline; the core value proposition
- **Slim rest-timer bar** — a thin progress bar (`#16263D` track, `rgba(47,128,237,0.22)` fill) that auto-runs with ±15s
- **Set-done fill** — completed rows fill green (`#27AE60` @ 18%) with a filled checkmark
- **Amber PR flag** (`#F2C94C`) — "▲ PR" appears the moment a set sets an estimated-1RM/rep record
- **Flat, borderless cards** — exercise cards have NO border; the `#242424` lift from canvas is the only depth
- **Warm-up / drop / failure tags** — `W` (amber) / `D` / `F` set-index markers
- **Inter everywhere** with aggressive tabular-figures usage; zero decoration, minimal chrome
- **No second accent** — one blue, two semantic colors (green done, amber PR); numbers stay loudest

## Brand Sources

- [Strong](https://www.strong.app/)
- [Strong on the App Store](https://apps.apple.com/us/app/strong-workout-tracker-gym-log/id464254577)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- Public brand palette: Strong Blue `#2F80ED`, neutral canvas `#1A1A1A`, PR amber `#F2C94C`, set-done green `#27AE60`
