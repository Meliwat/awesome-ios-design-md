# Flighty iOS Inspired Design System

Design system docs inspired by the [Flighty iOS app](https://apps.apple.com/us/app/flighty-live-flight-tracker/id1358823008). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Flighty's public product UI and Apple-grade dark-mode conventions; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Deep instrument-panel black** (`#0B0B0F`) — not pure black, premium and dark
- **Flighty Blue** (`#0A84FF`) as the single accent — primary action, live arc, active states
- **Live flight map arc** — a glowing blue great-circle over a dark world map, the signature
- **Strict status semantics** — on-time green / delay amber / cancelled red, chips only
- **32pt tabular flight times** — departure/arrival times are the screen heroes
- **Live-Activity-style status bar** — mirrors the Dynamic Island / Lock Screen layout
- **Delay timeline** — vertical rail with colored, pulsing status nodes
- **Soft blue arc glow** (`rgba(10,132,255,0.45)`) — the only colored light in the app

## Brand Sources

- [Apple App Store — Flighty listing](https://apps.apple.com/us/app/flighty-live-flight-tracker/id1358823008)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Flighty Blue `#0A84FF`, Canvas `#0B0B0F`, On-Time `#30D158`, Delay `#FFD60A`
