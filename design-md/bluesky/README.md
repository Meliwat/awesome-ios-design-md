# Bluesky iOS Inspired Design System

Design system docs inspired by the **Bluesky** iOS app. Not the official system. Brand colors, font names, and component patterns are cross-referenced with Bluesky's open-source app and public brand usage; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Triple theme** — Light `#FFFFFF`, Dim `#1E2936`, Dark `#0B0F14`; the user picks comfort
- **Bluesky Blue** (`#1185FE`) for links, active tab, compose FAB, primary actions, butterfly
- **Like pop-scale** — heart fills pink (`#EC4899`) and springs 1.0 → 1.25 → 1.0
- **Pinned custom-feed selector** at the top of Home — the feed is a user-controlled object
- **Reply-controls chip** on the composer — reply-gating made visible
- **Butterfly logomark** in `#1185FE` as the brand anchor
- **Flat, divider-separated cards** with 1pt embed borders — a quiet, conversational timeline
- **System font at 15pt / 1.4** with Inter parity for the web app

## Brand Sources

- **Bluesky** iOS and Android apps (open source) — component patterns and theme tokens
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) — system-font, blur, and tab-bar conventions
- [Material 3 Guidelines](https://m3.material.io/) — Android `NavigationBar` + FAB parity
- Public brand palette: Bluesky Blue `#1185FE`, Dim `#1E2936`, Dark `#0B0F14`
