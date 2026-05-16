# Max iOS Inspired Design System

Design system docs inspired by the [Max iOS app](https://apps.apple.com/us/app/max-stream-hbo-tv-movies/id971265422). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Max's public brand presence; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, motion + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Deep purple canvas** (`#12053A`) — the brand color IS the environment, not a neutral dark
- **Max brand gradient** (`#0046FF → #7B2FF7`) — wordmark, primary CTA, focus, billboard framing
- **Auto-trailer hero billboard** — a muted trailer that cross-fades into the next title every ~20s
- **Max Originals badge** — gradient-edged pill marking first-party prestige content
- **Profile gate** — full-screen circular-avatar grid on launch (HBO-lineage selector)
- **Solid violet fallback** (`#5A2BE0`) — tab indicator + small toggles where a gradient can't render
- **Prestige spacing** — 28pt row rhythm signaling premium over budget streamers
- **Inter typeface** (clean grotesque); weights 400/600/800

## Brand Sources

- [Apple App Store — Max](https://apps.apple.com/us/app/max-stream-hbo-tv-movies/id971265422)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Max gradient `#0046FF → #7B2FF7`, Canvas `#12053A`, Solid violet `#5A2BE0`
