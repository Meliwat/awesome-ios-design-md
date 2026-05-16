# Chipotle iOS Inspired Design System

Design system docs inspired by the [Chipotle iOS app](https://apps.apple.com/us/app/chipotle-fresh-food-fast/id327228455). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Chipotle's public brand presentation and observed product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Cream paper canvas** (`#FFF5E1`) — kraft-bag warmth, never stark white
- **Chipotle Red** (`#A81612`) as the only accent — Add to Bag, order CTA, rewards, selected state
- **Espresso brown** (`#451400`) for all text — no black anywhere in the app
- **ALL-CAPS bold condensed headers** (Archivo) — stenciled, menu-board energy
- **Build-your-burrito ingredient stepper** — sectioned single/multi-select rows with a running total
- **Rewards points ring** — circular red progress toward the next free entrée
- **Step progress bar** — slim segmented indicator that slides forward through the build
- **Slightly-squared 8pt radii** — kraft, not pill; warm espresso-tinted shadows

## Brand Sources

- [Apple App Store — Chipotle](https://apps.apple.com/us/app/chipotle-fresh-food-fast/id327228455)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Chipotle Red `#A81612`, Cream `#FFF5E1`, Espresso `#451400`
