# Lyft iOS Inspired Design System

Design system docs inspired by the [Lyft iOS app](https://apps.apple.com/us/app/lyft/id529379082). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Lyft's public brand presentation and observed product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Full-screen map, no tab bar** — the map is the primary surface; controls arrive in a sheet
- **Rounded bottom sheet** — every control slides up in a soft, springy panel (28pt top corners)
- **Lyft Pink** (`#FF00BF`) as the single accent — Confirm CTA, active ride-type, pickup pin
- **Very rounded geometry** — 16-28pt radii everywhere; pill map controls; bubble cards
- **Ride-type selector** — Wait & Save / Standard / XL / Lux Black, icon + ETA + tabular price
- **Pickup pin drop** — a pink teardrop that bounces onto the map with a scaling shadow
- **Full dark mode** (`#11111F`) for night rides — the pink holds on both themes
- **Rounded bottom-sheet spring** — the slightly-overshooting detent snap is the signature feel

## Brand Sources

- [Apple App Store — Lyft](https://apps.apple.com/us/app/lyft/id529379082)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Lyft Pink `#FF00BF`, Canvas `#FFFFFF`, Dark Canvas `#11111F`
