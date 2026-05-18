# Character.AI iOS Inspired Design System

Design system docs inspired by the [Character.AI iOS app](https://apps.apple.com/us/app/character-ai-ai-chat-creator/id6444881436). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Character.AI's public product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, chat stream + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **The chat IS the product** — near-black canvas `#0F0F10`, circular character avatar, centered greeting, upward message stream
- **Asymmetric bubbles** — AI vs user differentiated by a 4pt tucked corner, NOT by color
- **Deliberately close bubble greys** — AI `#1C1C1F` / user `#26262A`; calm and book-like, no bright "user blue"
- **Italic `*roleplay actions*`** rendered as muted secondary text `#9A9AA2` — a load-bearing Character.AI signature
- **One rationed accent blue** `#3A7BFD` — send button, primary CTAs, links, active tab; **never** a bubble fill
- **Accent pressed** `#2E63D6`; soft-accent button background `#1B2A4A` with text `#9FC0FF`
- **Lilac** `#9D7BFF` as an alternate avatar gradient only — character variety without a second UI color
- **Character greeting** — every chat opens with the persona's in-character hello under a centered name + one-line description
- **Typing indicator** — three dimming dots in an AI-shaped (4pt-tuck) bubble; opacity 1 → 0.4 → 1 staggered loop
- **Discover grid** — character cards: avatar + name + one-line description + chat-count, 2-up
- **Two-font system** — Sora for identity (wordmark/titles/names/buttons), Inter for all message text/descriptions
- **No drop shadows** — the AI bubble's 1pt `#2A2A2E` border is the only edge; soft quiet geometry (18pt bubbles, circular avatars, pill buttons)
- **Dark-first** — a light theme exists (`#FFFFFF` canvas, `#F1F1F3`/`#E4E8F2` bubbles) with the accent identical

## Brand Sources

- [Character.AI](https://character.ai/)
- [Sora (Google Fonts)](https://fonts.google.com/specimen/Sora) — SIL OFL (identity: wordmark, titles, names, buttons)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (message text and descriptions)
- Public brand palette: canvas `#0F0F10`, accent blue `#3A7BFD`, lilac `#9D7BFF`, AI bubble `#1C1C1F`, user bubble `#26262A`
- Signature: the immersive chat — circular avatar, centered greeting, asymmetric tucked-corner bubbles, italic roleplay actions
