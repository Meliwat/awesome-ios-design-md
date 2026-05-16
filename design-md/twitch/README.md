# Twitch iOS Inspired Design System

Design system docs inspired by the [Twitch iOS app](https://apps.apple.com/us/app/twitch-live-streaming/id460177396). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Twitch's public brand presence; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, motion + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Near-black canvas** (`#0E0E10`) with a faint cool tint — quiet chrome, kinetic content
- **Strict color split** — Twitch Purple (`#9146FF`) = brand/action; Live Red (`#EB0400`) = liveness only
- **Live thumbnail cards** — preview + red "● LIVE" pill + black viewer-count pill + game tag
- **Stream view with docked chat** — video on top, continuously scrolling chat below
- **Live chat overlay** — blurred 72%-opaque chat panel over full-screen video in theater mode
- **Channel live ring** — avatar ring is purple normally, red when currently LIVE
- **Emote chips** — inline emote/badge tokens, first-class baseline-aligned type
- **Off-white text** (`#EFEFF1`) — softer than pure white for long chat reading
- **Roobert typeface** (Inter fallback); weights 400/600/700

## Brand Sources

- [Apple App Store — Twitch](https://apps.apple.com/us/app/twitch-live-streaming/id460177396)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Twitch Purple `#9146FF`, Canvas `#0E0E10`, Live Red `#EB0400`
