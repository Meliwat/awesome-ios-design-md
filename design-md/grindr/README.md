# Grindr iOS Inspired Design System

Design system docs inspired by the [Grindr iOS app](https://apps.apple.com/us/app/grindr-gay-dating-chat/id319881193). Not the official system. Brand colors, font names, and component patterns are cross-referenced with **Grindr**'s public brand identity and product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Coil + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **True-black canvas** (`#000000`) — the photo grid *is* the interface
- **Grindr Yellow** (`#FFDE00`) as the ONLY accent — Tap action, active tab, badges, mask logo
- **Proximity cascade** — edge-to-edge 3-column square thumbnail grid, 2pt gutter, 0pt radius
- **Online green dot** (`#4CD964`) — presence overlaid on the tile corner; absence = offline
- **Scrimmed overlay text** — name + distance on a `rgba(0,0,0,0.65)` bottom gradient, weight 700
- **Black on yellow** — Tap/Send text is `#000000` on `#FFDE00`, intentional max contrast
- **Lazy-load fade** — thumbnails fade in (180ms) as the cascade scrolls; Inter fallback, 400/700

## Brand Sources

- [Apple App Store — Grindr listing](https://apps.apple.com/us/app/grindr-gay-dating-chat/id319881193)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Grindr Yellow `#FFDE00`, Canvas `#000000`, online green `#4CD964`
