# Telegram iOS Inspired Design System

Design system docs inspired by the [Telegram iOS app](https://apps.apple.com/us/app/telegram-messenger/id686449807). Not the official system. Brand colors, typography conventions, and component patterns are cross-referenced with Telegram's public brand resources and the open-source Telegram iOS repo; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `preview.md` | Link to the interactive Spectr gallery preview |

## Signature Moves

- **Telegram Blue** (`#0088CC`) default accent, but the entire color system is user-themeable
- **Outgoing bubble** (`#2B86FD`) — supports solid or vertical-gradient themes (`#2B86FD → #61B3FF`)
- **17pt bubble corner radius** with a subtle 6pt notch on the tail corner (not a full point)
- **Swipe-from-edge to reply** — pan the whole bubble horizontally with haptic tick at threshold
- **Silent message send** (long-press the send button) — opens scheduled / silent / send-when-online menu
- **Floating voice mini-player** pinned below the nav bar — persistent across chat switches
- **Animated emoji + sticker packs** via Lottie — single emojis render 72pt with vector animation
- **OLED dark mode** (`#000000`) as a toggle separate from the default dark canvas (`#212121`)
- **Secret chats** with self-destruct timer and lock icon on chat header
- **Custom Telegram outlined icons** — NOT SF Symbols for everything; proprietary brand glyphs for tabs

## Brand Sources

- [Telegram Brand Assets](https://telegram.org/brand)
- [Telegram iOS GitHub](https://github.com/TelegramMessenger/Telegram-iOS) — open-source reference
- Public brand palette: Telegram Blue `#0088CC`, Sky `#40A7E3`, bubble blue `#2B86FD`
- [Telegram Themes](https://telegram.org/blog/themes) — the theming system is core to the product
