# Plex iOS Inspired Design System

Design system docs inspired by the [Plex iOS app](https://apps.apple.com/us/app/plex-stream-movies-tv/id383457673). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Plex's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, server picker + On Deck rail + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Cool charcoal canvas** (`#1F2326`) — calm and neutral, intentionally NOT true black
- **Single accent** — Plex Yellow (`#E5A00D`) for Play, active tab, On Deck progress, unwatched dot, selected-server check
- **Dark-on-yellow** — yellow fills always carry dark ink (`#1A1304`), never white-on-yellow
- **Server picker pill + sheet** — status dot + name + chevron; the defining server-driven affordance
- **On Deck rail** — wide 16:9 tiles with a centered `rgba(0,0,0,0.55)` play overlay + a yellow progress bar
- **Unwatched corner dot** — one 9pt `#E5A00D` dot on library posters; the quiet status signal
- **Server status** — online green (`#4CAF50`) / offline gray (`#6B7075`) dots; yellow check on the active server
- **Inter typography** — neutral, functional weights (800 titles → 400 meta); type organizes, yellow expresses
- **Flat surfaces** — elevation is surface-color steps (`#1F2326` → `#282C30` → `#32373B`), not shadows
- **Five-tab bottom bar** — Home / Library / Discover / Live TV / You, active in `#E5A00D`, no tint pill
- **Quiet motion** — 150–280ms ease-out, no bounce; Plex is utility, not spectacle
- **Tech chips** — small `#32373B` pills for "1080p · 5.1" / "4K HDR" server-side technical truth

## Brand Sources

- [Plex](https://www.plex.tv/)
- [Plex Brand](https://www.plex.tv/about/press/) — Plex Yellow `#E5A00D`
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- Public brand palette: Plex Yellow `#E5A00D` with dark ink `#1A1304`, cool charcoal `#1F2326` canvas, online green `#4CAF50`
