# Disney+ iOS Inspired Design System

Design system docs inspired by the [Disney+ iOS app](https://apps.apple.com/us/app/disney/id1446075923). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Disney+'s public brand presentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Deep space-navy canvas** (`#0A0E2A`) with a faint starfield behind the hero — a darkened theater
- **Disney Blue** (`#0063E5`) primary; **glow blue** (`#1A75FF`) for focus halos and selected tiles
- **Brand-portal tile row** — Disney / Pixar / Marvel / Star Wars / Nat Geo as five gateway tiles
- **Starfield hero billboard** with logo artwork and a muted auto-playing trailer after ~3s
- **16:9 landscape content cards** — the streaming/TV aspect, never square
- **Unified tile glow + scale 1.04 focus** language everywhere (180ms ease-out, no bounce)
- **Bottom-up scrim** over key art so foreground UI stays legible
- **Avenir-family sans** (Inter fallback); weights 400/600/700; periwinkle-gray secondary; dark-only

## Brand Sources

- [Apple App Store — Disney+](https://apps.apple.com/us/app/disney/id1446075923)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Disney Blue `#0063E5`, glow blue `#1A75FF`, space-navy `#0A0E2A`
