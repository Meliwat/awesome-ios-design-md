# Hulu iOS Inspired Design System

Design system docs inspired by the [Hulu iOS app](https://apps.apple.com/us/app/hulu-stream-tv-shows-movies/id376510438). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Hulu's public brand presence; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, motion + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Near-black canvas** (`#0B0C0F`) — warm-lifted off pure black to protect thumbnail edges
- **Hulu Green** (`#1CE783`) as the ONLY accent — the electric "Watch" verb
- **16:9 landscape content tiles** in dense horizontal rails — density over drama
- **Green progress bar** on every continue-watching tile — resume state is always visible
- **Hub chip row** under the top nav — branded destinations (Disney, FX, Hulu Originals)
- **Details hero** — darkened backdrop still with a green Watch CTA pinned over it
- **Green-active tab bar** — unlike many streamers, green IS the tab indicator
- **Graphik typeface** (Inter fallback); weights 400/600/800

## Brand Sources

- [Apple App Store — Hulu](https://apps.apple.com/us/app/hulu-stream-tv-shows-movies/id376510438)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Hulu Green `#1CE783`, Canvas `#0B0C0F`, Live Red `#F0476A`
