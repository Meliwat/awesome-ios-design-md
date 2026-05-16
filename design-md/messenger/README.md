# Messenger iOS Inspired Design System

Design system docs inspired by the **Messenger iOS app**. Not the official system. Brand colors, font names, and component patterns are cross-referenced with Messenger's public brand presentation and observable in-app behavior; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Conversation-anchored gradient bubble** — outgoing messages are a `#0A7CFF → #9D4EDD → #FF5CA0` ribbon flowing down the whole run, not per-bubble
- **Neutral gray incoming** — energy stays on "you"
- **White / true-black canvas** (`#FFFFFF` / `#000000`) — pure black makes the gradient glow
- **Reactions popover** — long-press → bouncy floating row of 6 emoji with spring + staggered pop; selection lands as a corner badge
- **Big-thumb send** — a blue 👍 in the empty composer (one-tap like) that morphs into a filled send on text
- **Active-now green dot** (`#31D158`) on avatars — presence foregrounded
- **Round everything** — 18pt bubbles, circular chat-head avatars, pill fields
- **Type defers to color + motion** — Inter 400/600/700; unread is bold + a blue dot, never colored text

## Brand Sources

- **Messenger iOS app** — observed component behavior, gradient bubble model, reactions popover
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) — navigation, Dynamic Type, touch targets
- [Material 3 Guidelines](https://m3.material.io/) — Android `NavigationBar`, gradient masking, typography mapping
- Public brand palette: Gradient `#0A7CFF → #9D4EDD → #FF5CA0`, UI Blue `#0A7CFF`, Active Green `#31D158`
