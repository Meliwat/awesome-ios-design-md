# Uber Eats iOS Inspired Design System

Design system docs inspired by the [Uber Eats iOS app](https://apps.apple.com/us/app/uber-eats-food-delivery/id1058959277). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Uber's public brand presence; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, motion + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Pure white canvas** (`#FFFFFF`) — photo-first, gallery whitespace (dark mode supported, not default)
- **Uber Eats Green** (`#06C167`) as the single accent — every primary action and selected state
- **Photo-first restaurant card** — wide 16:9 photo + name + rating + ETA + delivery fee, borderless on white
- **Sticky cart bar** — persistent green bar with item count + total + "View cart"
- **Live map order tracking** — green courier marker easing along a near-black route under a draggable sheet
- **Category pill row** — horizontally-scrolling cuisine pills, green when selected
- **Monochrome ratings** — near-black star (not yellow) keeps merchandising quiet
- **Friendly radii** — 12pt buttons/photos, 16pt sheets; active tab is near-black/white, NOT green
- **Uber Move typeface** (Inter fallback); weights 400/500/700

## Brand Sources

- [Apple App Store — Uber Eats](https://apps.apple.com/us/app/uber-eats-food-delivery/id1058959277)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Uber Eats Green `#06C167`, Canvas `#FFFFFF`, Text `#000000`
