# Zoom iOS Inspired Design System

Design system docs inspired by the [Zoom iOS app](https://apps.apple.com/us/app/zoom-one-platform-to-connect/id546505307). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Zoom's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, gallery grid + control bar + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Dark in-call theater** (`#1A1A1A`) — committed dark inside calls so video is the brightest element
- **Zoom Blue** (`#2D8CFF`) as the single accent — the big Join button, Start pill, active controls, links
- **In-call red** (`#E02828`) reserved for Leave/End and the muted-mic state only
- **Gallery video grid** — rounded 8pt tiles with small corner name labels and a mic-mute badge
- **Active-speaker highlight** — a 3pt Zoom-blue inset border, debounced so it never flickers
- **Floating control bar** — Mute / Video / Share / Participants / React / Leave; auto-hides after ~4s
- **Oversized blue "Join"** (48pt / 17pt bold) — the app's center of gravity
- **Dual appearance outside calls**, dark theater inside; tabular numerics on the timer and meeting IDs

## Brand Sources

- [Zoom](https://www.zoom.com/)
- [Zoom Brand Guidelines](https://brandfolder.com/zoom)
- [Lato by Łukasz Dziedzic](https://fonts.google.com/specimen/Lato) — SIL OFL (closest free substitute)
- Public brand palette: Zoom Blue `#2D8CFF`, in-call red `#E02828`, dark theater `#1A1A1A`
