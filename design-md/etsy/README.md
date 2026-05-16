# Etsy iOS Inspired Design System

Design system docs inspired by the [Etsy iOS app](https://apps.apple.com/us/app/etsy-shop-handmade-vintage/id477128284). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Etsy's public brand and product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Warm-cream canvas** (`#FAF5EF`) — handmade, paper-warm, never stark white
- **Etsy Orange** (`#F1641E`) as the single accent — action, active tab, sale, favorites
- **Favorite-heart** — the emotional core; bounces (1.0→1.25→1.0) and fills orange on tap
- **Handmade product card** — large maker photo, title, price, near-black stars, "Bestseller"
- **Warm-tinted card shadow** — brown ambient so white cards feel like prints on cream paper
- **Listing image gallery** — full-bleed paged maker photos with white dots
- **Near-black review stars** (`#222222`) — Etsy stars are not gold
- **Full-pill CTAs** — friendly, rounded, never sharp-cornered

## Brand Sources

- [Apple App Store — Etsy listing](https://apps.apple.com/us/app/etsy-shop-handmade-vintage/id477128284)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Etsy Orange `#F1641E`, Canvas Cream `#FAF5EF`, Text `#222222`
