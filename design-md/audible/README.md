# Audible iOS Inspired Design System

Design system docs inspired by the [Audible iOS app](https://apps.apple.com/us/app/audible-audiobooks-podcasts/id379693831). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Audible's public brand presentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Warm charcoal canvas** (`#1A1A1A`) — a paper-at-night reading room, not cold black
- **Audible Orange** (`#FF9900`) as the only accent — play, progress, active chapter, primary CTA
- **Serif headers (Playfair Display) + sans body (Inter)** — the editorial, literary signature
- **30s-back / 30s-forward + speed dial (0.5×–3.5×)** — the defining audiobook transport
- **Cover progress ring** — an orange arc sweeping around the player's cover
- **Chapter list** with the active chapter marked orange + a leading bar
- **Large 18pt captions** (synced text) — readability while listening comes first
- **Covers cast real soft shadows** — they read as physical books on a warm shelf

## Brand Sources

- [Apple App Store — Audible](https://apps.apple.com/us/app/audible-audiobooks-podcasts/id379693831)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Audible Orange `#FF9900`, warm charcoal `#1A1A1A`, surface `#2A2A2A`
