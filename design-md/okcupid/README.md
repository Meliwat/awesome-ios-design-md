# OkCupid iOS Inspired Design System

Design system docs inspired by the [OkCupid iOS app](https://apps.apple.com/us/app/okcupid-dating-app/id338701294). Not the official system. Brand colors, font names, and component patterns are cross-referenced with **OkCupid**'s public brand identity and product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Coil + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Bright white canvas** (`#FFFFFF`) with a soft neutral surface (`#F2F2F2`) for floating cards
- **OkCupid Magenta** (`#E2024F`) leads — Like, primary CTA, active tab, match-% badge
- **Brand indigo** (`#0500FF`) supports — Pass, secondary links, illustration detail
- **Match-percentage circular badge** — color-tiered by fit, with a count-up on first view
- **DoubleTake profile card** — rich, internally scrollable cards (substance over swipe)
- **Match-question loop** — rounded question cards with option pills + importance control
- **Playful two-color illustrations** — friendly empty and "It's a Match!" celebration states
- **Larsseit typeface** (Inter fallback) — rounded grotesque, weights 400/600/700

## Brand Sources

- [Apple App Store — OkCupid listing](https://apps.apple.com/us/app/okcupid-dating-app/id338701294)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: OkCupid Magenta `#E2024F`, brand indigo `#0500FF`, Canvas `#FFFFFF`
