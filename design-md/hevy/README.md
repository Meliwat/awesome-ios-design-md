# Hevy iOS Inspired Design System

Design system docs inspired by the [Hevy iOS app](https://apps.apple.com/us/app/hevy-workout-tracker-gym-log/id1462562736). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Hevy's public product UI and App Store assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, set table + rest timer |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Near-black training canvas** (`#0E1116`) dark-first — engineered for gym lighting, no light default
- **The live workout log is the interface** — stacked exercise cards, each a dense set table
- **Set table** — Set / Previous / kg / Reps / ✓ — all numerics in tabular figures so rows never reflow
- **Hevy Blue** (`#1E6FFF`) is the *single* action color — Finish, active tab, exercise titles, every CTA
- **`Previous` column** on every set — last session's weight×reps inline; the core value proposition
- **Set-done wash** — completed rows tint green (`#2FBF71` @ 16%) with a filled checkmark
- **Gold PR badge** (`#F5B83D`) — "★ NEW PR" fires the instant a set beats a record, with a pulse + haptic
- **Rest-timer pill** — soft-blue (`#14233F`) countdown with a sweeping `#1E6FFF` ring; auto-starts on set check
- **Running duration timer** in Hevy Blue at the top of every active workout
- **Surface ladder** (`#0E1116` → `#161B22` → `#1F2630` → `#283040`) + 1pt borders carry depth, not shadows
- **Inter everywhere** with aggressive use of the tabular-figures feature for all numbers
- **No second accent** — one blue, two semantic colors (green done, gold PR); numbers stay loudest

## Brand Sources

- [Hevy](https://www.hevyapp.com/)
- [Hevy on the App Store](https://apps.apple.com/us/app/hevy-workout-tracker-gym-log/id1462562736)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- Public brand palette: Hevy Blue `#1E6FFF`, training canvas `#0E1116`, PR gold `#F5B83D`, set-done green `#2FBF71`
