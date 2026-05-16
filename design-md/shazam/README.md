# Shazam iOS Inspired Design System

Design system docs inspired by the [Shazam iOS app](https://apps.apple.com/us/app/shazam-music-discovery/id284993459). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Shazam's public brand presentation; exact private tokens may differ from production. The Shazam mark is a proprietary logo — ship the licensed asset.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Radial blue gradient canvas** (`#0050FF` core → `#0088FF` → `#08090E` edge) — a glowing well, not a flat color
- **One giant central Shazam button** with concentric pulse rings — the entire UI's center of gravity
- **No tab bar** — a single hero screen plus a draggable bottom sheet for history
- **Concentric pulse rings** emitting outward while listening — the signature interaction
- **Translucent glass surfaces** (`rgba(255,255,255,0.08)`) so the gradient reads through cards
- **Periwinkle secondary text** (`#B8C4FF`) tuned to the blue, never plain white-gray
- **Depth via blue-tinted glow + glass**, never neutral-gray shadows
- **Montserrat typography** (SF Pro fallback); weights 500/700; airy hero spacing; dark-only

## Brand Sources

- [Apple App Store — Shazam](https://apps.apple.com/us/app/shazam-music-discovery/id284993459)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Shazam Blue `#0088FF`, bright core `#0050FF`, space tint `#08090E`
