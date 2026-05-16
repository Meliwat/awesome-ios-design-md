# Signal iOS Inspired Design System

Design system docs inspired by the **Signal iOS app**. Not the official system. Brand colors, font names, and component patterns are cross-referenced with Signal's public brand presentation and observable in-app behavior; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Privacy-minimal** — zero ads, zero Stories pressure, zero vanity metrics; the absence is the design
- **White / soft-black canvas** (`#FFFFFF` / `#1B1B1B`) — never pure black
- **Signal Blue** (`#3A76F0`) as the only accent — outgoing bubbles, send button, links, primary actions
- **Blue outgoing / gray incoming** — outgoing blue bubble + white text, incoming neutral gray + primary text
- **Slide-up send button** — a 32pt blue circle that does not exist until you type; mic glyph when empty
- **Disappearing-message timer chip** — privacy as a quiet gray clock-ring, never a banner
- **Sealed-sender lock** — a tiny lock in message info; safety number in grouped monospace
- **Flat by default** — bubbles separate by fill contrast, real elevation only on sheets/menus

## Brand Sources

- **Signal iOS app** — observed component behavior, bubble model, slide-up send, disappearing timers
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) — navigation, Dynamic Type, touch targets
- [Material 3 Guidelines](https://m3.material.io/) — Android `NavigationBar`, per-corner shapes, typography mapping
- Public brand palette: Signal Blue `#3A76F0`, Canvas `#FFFFFF` / `#1B1B1B`, Incoming `#E9E9EB`
