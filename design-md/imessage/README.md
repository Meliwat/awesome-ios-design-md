# iMessage iOS Inspired Design System

Design system docs inspired by [iMessage](https://support.apple.com/messages), the built-in messaging app on [iOS / the Messages app](https://apps.apple.com/us/app/messages/id1146560473). Not the official system. Brand colors, system font names, and component patterns are cross-referenced with Apple's public Human Interface Guidelines and the visible product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, bubble `Shape`, tapback, typing, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, bubble + tapback + typing + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Blue/gray bubble pair** — outgoing iMessage `#007AFF` with white text, incoming gray `#E9E9EB` (light) / `#26262A` (dark)
- **SMS Green fallback** — `#34C759` when the message can't go over iMessage (the culturally-loaded blue-vs-green split)
- **Pinched tail** — ~19pt bubble radius with one corner at ~6pt on the sender's edge; only the last bubble of a same-sender run shows the tail
- **Tapback strip** — long-press surfaces a `#FFFFFF`/`#2C2C2E` capsule with ❤️ 👍 👎 😂 ‼️ ❓; the reaction docks as an overlapping chip
- **Typing dots** — three pulsing `tertiaryLabel` circles in an incoming-shaped gray bubble
- **Delivery receipts** — tiny `#3C3C4399` "Delivered" / "Read 9:41 AM" right-aligned under the last outgoing bubble
- **Blur-backed bars** — nav and compose bars use `.ultraThinMaterial`, never opaque
- **System San Francisco** — pure SF Pro at Dynamic Type sizes; no custom brand font
- **No brand palette** — `systemBlue` controls, `systemGray` ramps, `systemRed` `#FF3B30` FaceTime/destructive; the bubble dichotomy *is* the brand
- **True-black dark mode** — canvas goes `#000000` (OLED), not charcoal; brand bubble colors hold across modes
- **Message effects** — Slam / Loud / Gentle / Invisible Ink bubble effects + full-screen screen effects
- **~78% bubble max width** — the empty opposite margin is the directional cue, never full-width

## Brand Sources

- [iMessage / Messages (Apple Support)](https://support.apple.com/messages)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [San Francisco system fonts (SF Pro)](https://developer.apple.com/fonts/) — Apple system face, used via `.system`
- [SF Symbols](https://developer.apple.com/sf-symbols/) — Apple system icon set
- Public system palette: iMessage Blue `#007AFF`, SMS Green `#34C759`, incoming gray `#E9E9EB` / `#26262A`, systemRed `#FF3B30`
