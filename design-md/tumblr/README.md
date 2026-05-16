# Tumblr iOS Inspired Design System

Design system docs inspired by the **Tumblr** iOS app. Not the official system. Brand colors, font names, and component patterns are cross-referenced with Tumblr's public app and brand usage; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Deep-navy canvas** (`#001935`) with darker navy cards — a midnight zine, not black
- **Three-accent trio** — Blue `#00B8FF` actions/links, Green `#00CF35` follow/notes, Pink-red `#FF4930` like
- **Heart-burst on like** — scattered confetti hearts, Tumblr's signature delight moment
- **Reblog chain** — indented, attributed commentary blocks with a left rule, blue source names
- **Tag bar + notes count** — first-class post metadata under every post
- **Full-bleed media** — GIFs and photosets edge-to-edge inside cards, no letterboxing
- **Rounded-square avatars** (6–8pt) — the blog-tile heritage; quote posts get 26pt editorial weight
- **Dark navy default** with a multi-format dashboard (text/photo/GIF/quote/link)

## Brand Sources

- **Tumblr** iOS and Android apps — component patterns and color usage
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) — system-font, dark-mode, and tab-bar conventions
- [Material 3 Guidelines](https://m3.material.io/) — Android `NavigationBar` + docked FAB parity
- Public brand palette: Tumblr Blue `#00B8FF`, Tumblr Green `#00CF35`, Pink-Red `#FF4930`, Navy `#001935`
