# Wise iOS Inspired Design System

Design system docs inspired by the [Wise iOS app](https://apps.apple.com/us/app/wise-ex-transferwise/id612261027) (formerly TransferWise). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Wise's public brand and product surfaces; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, motion + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Bright white canvas** (`#FFFFFF`) with one forest anchor — the dark account hero
- **Forest green** (`#163300`) as the structural ink — titles, hero, primary numbers
- **Bright Green** (`#9FE870`) for action/highlight — always with forest text, never white
- **Fee-transparency card** — the itemized "here's exactly the cost" centerpiece
- **Multi-currency balance stack** — flag chip + code + tabular figure, decimal-aligned
- **Mid-market rate ticker** — the live rate stated plainly, guaranteed for 24h
- **Send-money flow stepper** — a numbered, connected money journey
- **Number roll-up** — balances and the total roll digits into place; money lands

## Brand Sources

- [Apple App Store — **Wise**, ex-TransferWise](https://apps.apple.com/us/app/wise-ex-transferwise/id612261027)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [Material 3 Design Guidelines](https://m3.material.io/) (for the Android port)
- Public brand palette: forest `#163300`, Bright Green `#9FE870`, canvas `#FFFFFF`
