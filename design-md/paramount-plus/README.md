# Paramount+ iOS Inspired Design System

Design system docs inspired by the [Paramount+ iOS app](https://apps.apple.com/us/app/paramount/id530168168). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Paramount+'s public product UI and Paramount Global's brand identity; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, brand-hubs row + hero billboard + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Dark-only** — midnight-navy canvas `#0A0E2D` lifting to `#0E1438` under hero art; no light mode
- **Brand-hubs row** — network-colored chips (CBS, MTV, Nickelodeon, Comedy Central, Showtime) high on Home; the bundle identity
- **Single accent** — Paramount+ Blue `#0064FF` for every primary CTA, progress, and selection
- **Sky-blue active tint** `#4D9DFF` — the active-tab color and eyebrow labels (legibility on navy)
- **Hub + hero pairing** — the hubs row comes first, *then* the full-bleed featured billboard
- **Live TV & Sports** flagged with red `#FF2D46` + a pulsing dot — a first-class Paramount+ pillar
- **Content rows** — tight free-scroll 2:3 poster strips that bleed off the trailing edge
- **Mountain logomark** + "Paramount**+**" wordmark with the blue "+" — the brand signature
- **Network hub re-skin** — tapping a hub cross-fades the whole browse into that network's brand color
- **Broadcast-bold typography** — Inter (Paramount Sans fallback), hero weight 900, 11pt uppercase tracked eyebrows
- **Monochrome navy chrome** — white `#FFFFFF` / `#AEB4D6` so full-color art is the loudest thing on screen
- **Prestige-quiet motion** — 120-400ms ease-out; hub re-skin cross-fades 300ms, live dot pulses on a 1.2s loop

## Brand Sources

- [Paramount+](https://www.paramountplus.com/)
- [Paramount Global brand](https://www.paramount.com/)
- [Inter (Google Fonts)](https://fonts.google.com/specimen/Inter) — SIL OFL (closest substitute for proprietary Paramount Sans)
- Public brand palette: Paramount+ Blue `#0064FF`, midnight-navy canvas `#0A0E2D`, broadcast live red `#FF2D46`
