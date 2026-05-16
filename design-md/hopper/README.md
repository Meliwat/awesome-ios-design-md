# Hopper iOS Inspired Design System

Design system docs inspired by the [Hopper iOS app](https://apps.apple.com/us/app/hopper-flight-hotel-car-deals/id904052407). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Hopper's public brand and product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Clean white canvas** (`#FFFFFF`) — bright, data-forward decision UI
- **Price-prediction calendar heatmap** — green=buy / red=wait cells, the core mechanic
- **Buy-green vs wait-red** (`#34C759` / `#FA4747`) — a strict semantic decision pair
- **Fare verdict pill** — colored "BUY" / "WAIT" beside the 28pt predicted price
- **Watch-price toggle** — a red pill switch that arms route tracking
- **Prediction confidence bar** — 4-segment bar + explicit percentage
- **Bunny mascot** — the friendly Hopper-Red voice in empty / celebration states
- **Red-active tab bar** — Hopper Red as the active indicator

## Brand Sources

- [Apple App Store — Hopper listing](https://apps.apple.com/us/app/hopper-flight-hotel-car-deals/id904052407)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Hopper Red `#FA4747`, Buy Green `#34C759`, Canvas `#FFFFFF`
