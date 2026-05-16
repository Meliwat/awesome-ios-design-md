# BeReal iOS Inspired Design System

Design system docs inspired by the **BeReal** iOS app. Not the official system. Brand colors, font names, and component patterns are cross-referenced with BeReal's public app and its product behavior; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Pure-black canvas** (`#000000`) — a photographic darkroom, not warm-black
- **No brand accent color** — white is the only accent; the absence *is* the identity
- **Dual-lens composite card** — full back photo + small draggable, corner-snapping front selfie
- **2-minute countdown banner** — the daily ritual, amber `#FFD60A` with tabular digits
- **Authenticity as UI** — "⚠ late" badge, relative timestamps, retake-count dots, never hidden
- **RealMoji reactions** — circular selfie-photos, never abstract emoji, zero vanity counts
- **System SF Pro only** — no custom font, no display sizes except the timer
- **Dual-shutter haptics** — heavy on the back capture, soft on the front lens

## Brand Sources

- **BeReal** iOS app — observed product behavior and UI patterns
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) — system-font, dark-mode, and camera-UI conventions
- [Material 3 Guidelines](https://m3.material.io/) — Android `NavigationBar` + haptics parity
- Public brand palette: Canvas `#000000`, Accent White `#FFFFFF` (no brand hue by design), Late Amber `#FFD60A`
