# eBay iOS Inspired Design System

Design system docs inspired by the [eBay iOS app](https://apps.apple.com/us/app/ebay-online-shopping-selling/id282614216). Not the official system. Brand colors, font names, and component patterns are cross-referenced with eBay's public four-color brand and product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Clean white canvas** (`#FFFFFF`) — dense, utilitarian, browse-velocity-first
- **Four-color brand mark** — red / blue / yellow / green, used with discipline
- **eBay Blue** (`#0064D2`) action + link; **eBay Red** (`#E53238`) urgency + watch
- **Bid vs Buy-It-Now badges** — the signature buy-mechanic distinction (blue vs neutral)
- **"Time left" countdown** — turns red under 24h, tabular seconds under 1h, never jitters
- **Red watch-heart** — eBay's watch color is red, not the brand blue
- **Equity-locked wordmark** — e red, B blue, a yellow, y green; never recolored
- **Minimal shadows** — hairlines + whitespace, not depth, keep the catalog fast

## Brand Sources

- [Apple App Store — eBay listing](https://apps.apple.com/us/app/ebay-online-shopping-selling/id282614216)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: eBay Blue `#0064D2`, Red `#E53238`, Yellow `#F5AF02`, Green `#86B817`
