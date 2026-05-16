# Grok iOS Inspired Design System

Design system docs inspired by the **Grok** iOS app (xAI). Not the official system. Brand colors, font choices, and component patterns are cross-referenced with the public X / xAI visual identity that Grok inherits; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **True-black canvas** (`#000000`) — OLED-true, inherited from X, never softened
- **Monochrome by design** — white text (`#E7E9EA`), one grey (`#71767B`), no decorative accent
- **X link blue** (`#1D9BF0`) as the only chroma — reserved for links and post citations
- **Conversation as transcript** — user message in a subtle bubble, assistant reply as plain full-width text
- **Real-time X post citation card** — Grok's signature live-data surface
- **Mode toggle** (Regular / Fun) — a centered segmented pill that shifts personality
- **Streaming token reveal** with a blinking block cursor `▍` — teletype, not a bouncy spring
- **Single-surface, no tab bar** — history is a slide-over; the conversation owns the viewport
- **Inter with slashed zero** — technical, machine-readable; weights 400/600/700

## Brand Sources

- [Apple App Store — Grok](https://apps.apple.com/us/app/grok/id6670324846)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [Inter typeface (rsms.me/inter)](https://rsms.me/inter/)
- Public palette: Canvas `#000000`, Text `#E7E9EA`, Link Blue `#1D9BF0` (inherited from the X identity)
