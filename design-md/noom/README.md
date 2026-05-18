# Noom iOS Inspired Design System

Design system docs inspired by the [Noom iOS app](https://apps.apple.com/us/app/noom-weight-loss-health/id1056473921). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Noom's public product UI and App Store materials; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, lesson card + food log + weight graph, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, react-native-svg graph, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Canvas weight graph + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Daily psychology lesson card** — bold blue→teal gradient hero (`#2A5DF6 → #1FC29B`), the emotional front door of the app
- **Food-color system** — Green `#34C759` / Yellow `#FFC531` / Red `#FF5A52`, the signature mechanic; fixed hues, identical in light & dark
- **Stacked food-ratio bar** — green/yellow/red proportions of the day at a glance, above the food list
- **Noom Blue** (`#2A5DF6`) primary action + **Noom Teal** (`#1FC29B`) progress accent + **Noom Navy** (`#0C1B4D`) anchor
- **Weight graph** — blue trend curve over a gradient fill with a dashed teal goal line `#1FC29B`
- **Coach surface** — its own Coach Purple `#7B61FF` world, distinct from blue data surfaces
- **Fully pill-shaped buttons** (999pt radius) — soft, friendly, non-clinical
- **Poppins geometric sans** — circular bowls signal warmth; heavy numerals (700–800), conversational body (400)
- **Encouraging coach copy** — "Day 24 · You're building momentum", never clinical
- **Navy-tinted soft shadows** (`rgba(20,27,77,0.06)`) in light; flat with borders in dark
- **Soft near-white canvas** (`#FBFBFD`) light / comfortable `#121212` dark — never harsh pure white/black
- **Minimal chrome** — 5-tab bottom bar: Today / Learn / Log / Coach / Profile

## Brand Sources

- [Noom](https://www.noom.com/)
- [Poppins by Indian Type Foundry / Jonny Pinhorn (Google Fonts)](https://fonts.google.com/specimen/Poppins) — SIL OFL
- Public brand palette: Noom Blue `#2A5DF6`, Noom Teal `#1FC29B`, Noom Navy `#0C1B4D`, and the green/yellow/red food-color system
