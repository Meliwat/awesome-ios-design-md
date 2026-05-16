# Domino's iOS Inspired Design System

Design system docs inspired by the [Domino's iOS app](https://apps.apple.com/us/app/dominos-pizza-usa/id436491861). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Domino's public brand presentation and observed product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Clean white canvas** (`#FFFFFF`) — bright, scannable, high-contrast catalog
- **Domino's Red** (`#E31837`) for action — order, build, deals, active tracker stage
- **Domino's Blue** (`#006491`) for info — links, completed checkmarks, supporting badges
- **Five-stage pizza tracker** — Prep → Bake → Box → Quality Check → Out for delivery
- **Build-your-pizza live preview** — a circular pie that updates as options change
- **Deal cards** — red-bordered tiles with big tabular price callouts
- **Domino-square mark** — the red/blue split square as a recurring brand motif
- **Sturdy Archivo type** — weights 400/700; bold carries the hierarchy

## Brand Sources

- [Apple App Store — Domino's Pizza](https://apps.apple.com/us/app/dominos-pizza-usa/id436491861)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Domino's Red `#E31837`, Domino's Blue `#006491`, Canvas `#FFFFFF`
