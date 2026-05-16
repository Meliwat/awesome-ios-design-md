# Revolut iOS Inspired Design System

Design system docs inspired by the [Revolut iOS app](https://apps.apple.com/us/app/revolut/id932493382). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Revolut's public brand and product surfaces; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, motion + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Cool near-black canvas** (`#0A0A0F`) — fintech as precision hardware, never warm
- **Violet→purple gradient** (`#5B6BFF → #9C6BFF`) as the single brand accent
- **Solid `#6B5BFF`** only where a gradient can't render — strokes, tiny icons, focus rings
- **Metal-card hero** with a moving diagonal sheen and a 3D Y-axis flip
- **Multi-currency balance tiles** — flag chip + code + tabular figure, decimal-aligned
- **Spend-analytics donut** — one ring, gradient spent arc, category legend
- **Incoming green / outgoing white** transaction amounts, tabular figures
- **Gradient CTA glow** — primary actions lit from within with a soft violet halo

## Brand Sources

- [Apple App Store — **Revolut**](https://apps.apple.com/us/app/revolut/id932493382)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [Material 3 Design Guidelines](https://m3.material.io/) (for the Android port)
- Public brand palette: violet `#5B6BFF`, purple `#9C6BFF`, solid brand `#6B5BFF`, canvas `#0A0A0F`
