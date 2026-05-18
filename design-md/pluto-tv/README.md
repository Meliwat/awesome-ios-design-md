# Pluto TV iOS Inspired Design System

Design system docs inspired by the [Pluto TV iOS app](https://apps.apple.com/us/app/pluto-tv-live-tv-and-movies/id751712746). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Pluto TV's public product UI and brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, the EPG grid, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, EPG grid, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, EPG grid, mini-player |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **The product is the EPG channel guide** — a frozen-frame grid, not a poster wall
- **Deep cool-navy canvas** (`#0B0F1F`) — late-night TV glow, not pure black; dark-only, no light mode
- **"On Now" cell** = brighter surface (`#1C2440`) + **3pt `#FFE100` yellow left border** — the #1 design signature
- **Frozen channel column** (channel number + logo tile, 86pt) — never scrolls horizontally
- **Sticky 30-minute time-bar** — stays pinned while channel rows scroll vertically
- **Pinned mini-player** on top of the guide — streams the selected channel with a LIVE badge
- **Pluto Blue** (`#0048FF`) — wordmark block, primary CTA, active category pill
- **Signature yellow** (`#FFE100`) — reserved for "on now / live" emphasis and the active tab only
- **Red `#FF3B5C` dot + "LIVE"** — the universal live indicator everywhere
- **Cable-box category-tier pills** (All / Featured / Movies / News / Sports / Comedy)
- **Tabular numerals** for channel numbers and times so the grid stays aligned while scrolling
- **Bottom tabs** Live TV / On Demand / Search / My Pluto / Account — active icon yellow `#FFE100`, no pill

## Brand Sources

- [Pluto TV](https://pluto.tv/)
- [Manrope by Mikhail Sharanda](https://fonts.google.com/specimen/Manrope) — SIL OFL (closest free analog to Pluto's brand sans)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (acceptable substitute)
- Public brand palette: Pluto Blue `#0048FF`, signature yellow `#FFE100`, navy canvas `#0B0F1F`, live red `#FF3B5C`
