# Miro iOS Inspired Design System

Design system docs inspired by the [Miro iOS app](https://apps.apple.com/us/app/miro-online-whiteboard/id1180074773). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Miro's public product UI and brand site; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, infinite-canvas + sticky-note + toolbar views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, gesture-driven canvas, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, transform-gesture canvas + floating toolbar + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Infinite canvas** — pinch-zoom, two-finger pan; content lives in unbounded 2D space, not a scroll view
- **Zoom-scaled dotted/square grid** board — `#F5F5F7` light / `#202024` dark, grid `#D7D7DE` / `#38383F`
- **Miro Yellow** (`#FFD02F`) + **Miro Ink** (`#050038`) — the unmistakable brand pair; glyph on yellow is always ink
- **Miro Blue** (`#4262FF`) — functional selection accent (bounding box, handles, marquee), distinct from brand
- **Floating toolbar** — rounded bar of creation tools; active tool fills yellow with an ink glyph
- **Sticky notes** — tight 4pt-radius pastel paper squares with soft lift shadow + drag-grip; auto-fitting text
- **Connectors** — object-snapping lines/arrows that reflow live when objects move
- **Frames** — dashed named regions that group content and double as presentation slides
- **Live multiplayer cursors** — per-user colored arrows + name tags, interpolated so they glide
- **Zoom pill + minimap** — persistent wayfinding because an infinite canvas needs it
- **Paper stays full-color in dark mode** — only chrome goes dark; sticky pastels never desaturate
- **Ink-tinted modal dim** `rgba(5,0,56,0.32)` — not pure black; reinforces the brand

## Brand Sources

- [Miro](https://miro.com/)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- [Caveat (Google Fonts)](https://fonts.google.com/specimen/Caveat) — SIL OFL (optional handwriting sticky)
- [JetBrains Mono](https://www.jetbrains.com/lp/mono/) — SIL OFL (code embeds)
- Public brand palette: Miro Yellow `#FFD02F`, Miro Ink `#050038`, Miro Blue selection `#4262FF`, pastel sticky-note palette
