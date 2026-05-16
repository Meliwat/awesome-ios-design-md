# Walmart iOS Inspired Design System

Design system docs inspired by the [Walmart iOS app](https://apps.apple.com/us/app/walmart-shopping-grocery/id338137227). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Walmart's public brand guidelines and the Walmart Living Design / Glass design system; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, motion + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Bright white canvas** (`#FFFFFF`) with a cool-tint surface (`#F2F8FD`) — never dark
- **Walmart Blue** (`#0071DC`) as the only action color — CTA, links, active tab
- **Spark Yellow** (`#FFC220`) reserved for the Spark logo and savings chrome only
- **Rollback price tag** — the signature dark-on-yellow savings callout above the price
- **Six-ray Spark logomark** — the instantly recognizable sunburst, with a fan-in launch
- **Pickup / Delivery toggle pill** at the top of Shop — fulfillment is a primary choice
- **Price is the heaviest element** — 22pt weight 700 with tabular numerals
- **Add-to-cart bump** — a 260ms scale confirmation with a cart-badge pop and soft haptic

## Brand Sources

- [Apple App Store — **Walmart** Shopping & Grocery](https://apps.apple.com/us/app/walmart-shopping-grocery/id338137227)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [Material 3 Design Guidelines](https://m3.material.io/) (for the Android port)
- Public brand palette: Walmart Blue `#0071DC`, Spark Yellow `#FFC220`, charcoal text `#2E2F32`
