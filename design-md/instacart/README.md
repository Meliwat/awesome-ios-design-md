# Instacart iOS Inspired Design System

Design system docs inspired by the [Instacart iOS app](https://apps.apple.com/us/app/instacart-grocery-delivery/id545599256). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Instacart's public brand presentation and observed product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Pure white grocery shelf** (`#FFFFFF`) — clean canvas so product photography reads fast
- **Instacart Green** (`#0AAD0A`) as the action color — Add buttons, stepper, cart bar, active tab
- **Carrot orange** (`#FF7009`) reserved for deals, savings, and sale prices — the incentive color
- **Quantity stepper** (− n +) — a green pill with white glyphs; the signature interaction
- **"Add" pill morphs into the stepper** on first tap (width animates 64pt → 104pt)
- **Persistent green cart bar** — running item count + subtotal, always one tap from checkout
- **Replacement-preference selector** — per-item "Best match / Pick specific / Don't replace"
- **Tabular figures everywhere** — prices, quantities, and totals align across long lists

## Brand Sources

- [Apple App Store — Instacart](https://apps.apple.com/us/app/instacart-grocery-delivery/id545599256)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Instacart Green `#0AAD0A`, Carrot `#FF7009`, Canvas `#FFFFFF`
