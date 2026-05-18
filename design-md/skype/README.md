# Skype iOS Inspired Design System

Design system docs inspired by the [Skype iOS app](https://apps.apple.com/us/app/skype/id304878510). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Microsoft's public Skype product UI and brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, bubble + call card + video grid, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, bubble + call card + video grid + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Skype Blue is the brand** — bright cloud-cyan `#00AFF0` on buttons/FABs/tabs, text-safe deep `#0078D4` on the outgoing bubble
- **Neutral near-black dark mode** — canvas `#16161A` is a true neutral, NOT color-cast, so the cloud blue stays vivid
- **Gray/blue bubble pair** — outgoing deep blue `#0078D4`, incoming gray `#EBEBEF` (light) / `#2A2A30` (dark), ~14pt radius with a ~4pt tail
- **Call-first chrome** — phone + video icons in every chat header, a dedicated Calls tab and Contacts tab
- **Inline call cards** — call history interleaved in the chat as rounded cards (glyph + duration + participants)
- **Video call grid** — gradient participant tiles, pinned self-view, per-tile mic, a big circular control bar (mute / video / share / end)
- **Presence dots everywhere** — green active `#2DC26B`, yellow away `#FFC400`, red DND `#E8364F`, gray offline ring
- **Accept/End semantics** — green `#2DC26B` accept, red `#E8364F` end-call + unread badges
- **Deep-cyan ink on bright blue** — text on `#00AFF0` is `#00343F` for WCAG AA; white on the deep `#0078D4` bubble
- **Reactions strip** — heart-led emoji row on long-press, docked as a chip
- **Clean humanist type** — Segoe UI brand sans (Inter / Open Sans substitute), heavy 700/800 titles & buttons
- **Bottom tabs** — Chats / Calls / Contacts / Notifications with bright-blue active tint and no Material pill

## Brand Sources

- [Skype (Microsoft)](https://www.skype.com/)
- [Skype on the App Store](https://apps.apple.com/us/app/skype/id304878510)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (free Segoe-UI substitute); [Open Sans](https://fonts.google.com/specimen/Open+Sans) — SIL OFL alternative
- Public brand palette: Skype Blue `#00AFF0`, deep `#0078D4`, cyan `#34C3FF`, accept green `#2DC26B`, end red `#E8364F`, away `#FFC400`
