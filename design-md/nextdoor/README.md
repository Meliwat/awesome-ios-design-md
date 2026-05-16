# Nextdoor iOS Inspired Design System

Design system docs inspired by the **Nextdoor** iOS app. Not the official system. Brand colors, font names, and component patterns are cross-referenced with Nextdoor's public app and brand usage; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Warm cream canvas** (`#FAF9F6`) with crisp white cards — paper, not screen
- **Nextdoor Green** (`#00B246`) for primary actions, the center Post FAB, follow/join, verified
- **Hyperlocal map** with category-colored pins at real addresses — the hero screen
- **Location line** under every name (distance · neighborhood · time) — local context is the product
- **Verified-neighbor badge** in green — trust as a first-class visual
- **Group/topic chips** to scope the feed to a community
- **Lato typeface** (warm humanist sans) with an aligned system fallback
- **Light mode only** with gentle, civic motion — fades and soft scales, never flashy

## Brand Sources

- **Nextdoor** iOS and Android apps — component patterns and color usage
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) — MapKit, system-font, and tab-bar conventions
- [Material 3 Guidelines](https://m3.material.io/) — Android `NavigationBar` + docked FAB parity
- Public brand palette: Nextdoor Green `#00B246`, Cream Canvas `#FAF9F6`, Warm Ink `#221E1F`
