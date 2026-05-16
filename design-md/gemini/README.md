# Google Gemini iOS Inspired Design System

Design system docs inspired by the [Google Gemini iOS app](https://apps.apple.com/us/app/google-gemini/id6477489729). Not the official system. Brand colors, font names, and component patterns are cross-referenced with **Google Gemini**'s public brand identity and product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Coil + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Document-not-feed canvas** — white (`#FFFFFF`) light / charcoal (`#1E1E1E`) dark, no cards
- **The Gemini gradient** (`#4285F4 → #9B72CB → #D96570`) — the only accent, rationed to brand moments
- **Gradient sparkle ✦** — the mark for "intelligence here" (turn marker, hero, chip accent)
- **User chip + plain assistant text** — gray trailing chip vs. unboxed markdown on the canvas
- **Streaming gradient shimmer** — answers generate token-by-token with a gradient edge sweep
- **Gradient focus ring** — the prompt bar's signature "listening" cue (not a blue glow)
- **Drawer-only navigation** — no bottom tabs; recents + Gems in a side drawer
- **Google Sans typeface** (Inter fallback) — reading-optimized, weights 400/500/700, full light+dark parity

## Brand Sources

- [Apple App Store — Google Gemini listing](https://apps.apple.com/us/app/google-gemini/id6477489729)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Gemini gradient `#4285F4 → #9B72CB → #D96570`, Google Blue `#4285F4`
