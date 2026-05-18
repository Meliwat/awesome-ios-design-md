# Deezer iOS Inspired Design System

Design system docs inspired by the [Deezer iOS app](https://apps.apple.com/us/app/deezer-music-podcast-player/id292738169). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Deezer's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, living-gradient Flow artwork, embedded equalizer, gradient play button & scrubber |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, `expo-linear-gradient` artwork, Reanimated equalizer + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, Flow artwork, gradient play button + scrubber + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Violet-tinted near-black canvas** (`#0F0D13`) — dark-native, no real light theme, never pure `#000000`
- **Scarce purple→pink gradient** (`#A238FF` → `#FF0092`) — only on the play button, scrubber fill, FLOW badge, now-playing equalizer
- **Flow** — personalized infinite mix with a living-gradient cover + lightning "FLOW" badge
- **Embedded equalizer** — luminous white bars *inside* the Flow artwork; the cover is the visualizer
- **68pt circular gradient play button** — the single brightest object on Now Playing, with a pink glow (`rgba(255,0,146,0.6)`)
- **Gradient scrubber** — track fill is the brand gradient over a `#221E2B` rail; everything else monochrome
- **Now-playing-aware song rows** — the active track recolors to pink `#FF0092` with a live 4-bar equalizer
- **Monochrome chrome** — chevrons, secondary transport, tab icons all `#A29CB0`
- **Colored glows, not gray shadows** — violet under artwork (`rgba(162,56,255,0.55)`), pink under play
- **Heavy display type** — Deezer Sans / Inter at weights 700–800 for titles, Inter 400/500 for body
- **Pill geometry** — buttons, chips, and the FLOW badge are fully rounded (999pt); 16pt artwork tile
- **Purple active tab tint** (`#A238FF`) — no Material pill, just a tinted filled icon

## Brand Sources

- [Deezer](https://www.deezer.com/)
- [Deezer Brand & Press](https://www.deezer-blog.com/press/) — purple `#A238FF`, pink `#FF0092`, Flow identity
- Deezer Sans (proprietary brand typeface) — substitute [Inter by Rasmus Andersson](https://rsms.me/inter/) (SIL OFL)
- Public brand palette: gradient `#A238FF → #FF0092`, violet near-black canvas `#0F0D13`
