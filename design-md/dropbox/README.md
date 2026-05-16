# Dropbox iOS Inspired Design System

Design system docs inspired by the [Dropbox iOS app](https://apps.apple.com/us/app/dropbox-cloud-photo-storage/id327630330). Not the official system. Brand colors, font names, and component patterns are cross-referenced with **Dropbox**'s public brand guidelines and design language; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Coil + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Paper-white canvas** (`#FFFFFF`) with a warm-cream grouped surface (`#F7F5F2`)
- **Dropbox Blue** (`#0061FF`) as the ONLY UI accent — primary actions, active tab, links, selection
- **File-type row list** — each row leads with a colored type icon (PDF red, Doc blue, Sheet green, Image teal)
- **Floating blue Upload FAB** — 56pt circle, bottom-right, blue-tinted shadow
- **Determinate upload progress bar** — real byte progress, never a fake indeterminate spinner
- **Preview thumbnail grid** — edge-to-edge 3-column contact sheet, 0pt tile radius
- **Warm ink, not pure black** — text `#1E1919`, dividers `#E6E1DA`; Sharp Grotesk (Inter fallback), 400/600/700

## Brand Sources

- [Apple App Store — Dropbox listing](https://apps.apple.com/us/app/dropbox-cloud-photo-storage/id327630330)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Dropbox Blue `#0061FF`, Canvas `#FFFFFF`, warm ink `#1E1919`
