# TIDAL iOS Inspired Design System

Design system docs inspired by the [TIDAL iOS app](https://apps.apple.com/us/app/tidal-music/id913943275). Not the official system. Brand colors, font names, and component patterns are cross-referenced with TIDAL's public brand presentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Pure black canvas** (`#000000`) — true OLED black, not softened
- **White as the action color**; **HiFi cyan** (`#00FFFF`) reserved exclusively for quality-tier badges
- **Quality badges** (Master / HiFi / Max) — fidelity made visible, the product differentiator
- **Square album art everywhere** — 0pt corner radius, the editorial signature
- **Radically flat** — no shadows; elevation via tonal steps (`#0A0A0A` / `#1A1A1A`)
- **Full-bleed Now Playing** with a darkened, monochrome album-art backdrop (no color extraction)
- **Crossfade transitions** — minimal, editorial, never bounce or slide
- **Geometric sans** (Space Grotesk fallback); weights 400/600/700; dark-only

## Brand Sources

- [Apple App Store — TIDAL Music](https://apps.apple.com/us/app/tidal-music/id913943275)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: TIDAL pure black `#000000`, HiFi cyan `#00FFFF`, white `#FFFFFF`
