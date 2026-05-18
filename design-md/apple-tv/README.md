# Apple TV iOS Inspired Design System

Design system docs inspired by the [Apple TV iOS app](https://apps.apple.com/us/app/apple-tv/id1174078549). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Apple's public product UI and the Human Interface Guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, inset hero + Up Next rail + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **True-black canvas** `#000000` — OLED pixels off; inset rounded artwork floats in void
- **Watch Now home** — large SF Pro title + inset rounded hero card + the Up Next rail
- **Up Next rail** — 16:9 thumbnails with a thin white resume bar + "Apple TV+" channel tags
- **One accent** — system blue `#0A84FF` for the active tab and text links only; no second hue, no gradient
- **White primary CTA** — "Play" is solid `#FFFFFF` with a black label, 12pt rounded; glass `rgba(120,120,128,0.36)` secondary
- **Inset rounded hero** — 14pt inset, 16pt radius — the signature floating-gallery treatment (NOT full-bleed)
- **MLS Season Pass** — the one non-system color: league hot-pink `#ED1A6F` on its shelf & live chips
- **Glass-and-blur chrome** — translucent `blur(20px)` tab bar over true black, 0.33pt hairline divider
- **Live sports red** `#FF453A` + a pulsing dot — standard iOS, used sparingly
- **SF Pro typography** (Inter fallback) — HIG text styles, Dynamic Type first-class, optical tracking on titles
- **Restraint is the brand** — generous whitespace (16-24pt inter-shelf), no badge clutter, system motion only
- **Surface ramp** — `#000` → `#1C1C1E` → `#2C2C2E` (iOS dark system backgrounds)

## Brand Sources

- [Apple TV](https://www.apple.com/apple-tv-app/)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [Inter (Google Fonts)](https://fonts.google.com/specimen/Inter) — SIL OFL (standard substitute for proprietary SF Pro; prefer `-apple-system` on iOS)
- Public brand palette: true black `#000000`, system blue `#0A84FF`, MLS Season Pass `#ED1A6F`, iOS dark label & background ramp
