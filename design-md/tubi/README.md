# Tubi iOS Inspired Design System

Design system docs inspired by the [Tubi iOS app](https://apps.apple.com/us/app/tubi-movies-live-tv-shows/id886445756). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Tubi's public product UI and brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / gradient / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, expo-linear-gradient, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, gradient brushes, Material 3 `Typography`, composables, Live TV EPG |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Deep indigo-black canvas** (`#0A0A2A`) — cool and slightly violet so the gradient glows; dark-only, no light mode
- **Purple→magenta brand gradient** (`#7408FF` → `#FF00FF`) — wordmark, focus, active tab, progress, secondary CTA
- **Oversized featured hero** — single title, gradient "TUBI ORIGINAL" badge, white "Play Free" button
- **Dense horizontal poster rows** — tall 2:3 cards, 3+ visible, 10pt gutters, bleeding off the right edge
- **Yellow `#FFD400` "FREE" tag** on every poster — Tubi's promise stated as a UI feature, never hidden
- **Pure white `#FFFFFF` Play button** with `#0A0A2A` text — the single highest-contrast CTA
- **Continue-watching progress** — 4pt gradient bar pinned to the poster's bottom edge
- **Live TV EPG guide** — channel rows + sticky 30-min time header + gradient-marked "On Now" blocks
- **Heavy display type** — Inter 800–900 titles with tight negative tracking; only badges are ALL-CAPS
- **Cool lavender-grey metadata** (`#B9B9D6`) — harmonizes with the indigo canvas, not neutral grey
- **Lit-by-brand focus** — focused posters gain a 2pt gradient ring + scale 1.04, not just a shadow
- **Bottom tabs** Home / Search / Live TV / My List / Account — active icon magenta `#FF00FF`, no pill

## Brand Sources

- [Tubi](https://tubitv.com/)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (closest free analog to Tubi's custom geometric grotesque)
- [Archivo (Google Fonts)](https://fonts.google.com/specimen/Archivo) — SIL OFL (optional punchier display alternative)
- Public brand palette: purple `#7408FF` → magenta `#FF00FF` gradient on indigo-black `#0A0A2A`, FREE-tag yellow `#FFD400`
