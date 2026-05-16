# AllTrails iOS Inspired Design System

Design system docs inspired by the [AllTrails iOS app](https://apps.apple.com/us/app/alltrails-hike-bike-run/id405075943). Not the official system. Brand colors, font choices, and component patterns are cross-referenced with AllTrails' public product presentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Topo-paper light space** — pure-white canvas with a cool sage section surface (`#F2F4F1`)
- **AllTrails Green** (`#428000`) as the lead accent — action, active tab, Record, selected pin
- **Difficulty color scale** — Easy green / Moderate amber (`#C77700`) / Hard brick red (`#B3261E`)
- **The trail card** — photo, difficulty pill, name, location, tabular stat row, star rating (signature)
- **Map-first Explore** — a full-bleed topographic base with difficulty-tinted pins and floating controls
- **Record GPS button** — a prominent green capture control that becomes a red stop while tracking
- **Route trace draw** — the path animates onto the map from the trailhead (~1000ms)
- **High-contrast, legible** — built to be read outdoors in bright sun

## Brand Sources

- [AllTrails — official site](https://www.alltrails.com)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [Inter typeface (Google Fonts)](https://fonts.google.com/specimen/Inter)
- Public palette: AllTrails Green `#428000`, difficulty Moderate `#C77700` / Hard `#B3261E`, sage surface `#F2F4F1`
