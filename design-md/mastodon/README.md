# Mastodon iOS Inspired Design System

Design system docs inspired by the **Mastodon** iOS app. Not the official system. Brand colors, font names, and component patterns are cross-referenced with Mastodon's open-source apps and public brand usage; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **White light canvas** (`#FFFFFF`) / **blue-gray dark** (`#191B22`) — never true black
- **Mastodon Purple** (`#6364FF`) for links, mentions, hashtags, active tab, compose FAB
- **Boost-with-spin** — the icon turns green (`#2DCE89`) and rotates 360° on amplification
- **Content-warning spoiler** — collapsed by default, "SHOW MORE" reveals the body inline
- **Federated `@user@instance` handle** always visible — identity is the instance
- **Home / Local / Federated switcher** surfaced as a first-class timeline control
- **Rounded-square avatars** (8–12pt), not circles — a deliberate identity choice
- **Flat, divider-separated cards** — the timeline reads like a quiet document

## Brand Sources

- **Mastodon** iOS and Android apps (open source) — component patterns and color usage
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) — system-font, blur, and tab-bar conventions
- [Material 3 Guidelines](https://m3.material.io/) — Android `NavigationBar` + FAB parity
- Public brand palette: Mastodon Purple `#6364FF`, Boost Green `#2DCE89`, Dark Canvas `#191B22`
