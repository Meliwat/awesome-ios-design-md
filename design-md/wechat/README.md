# WeChat iOS Inspired Design System

Design system docs inspired by the **WeChat iOS app**. Not the official system. Brand colors, font names, and component patterns are cross-referenced with WeChat's public brand presentation and observable in-app behavior; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Super-app Discover hub** — a grouped list of feature rows (Moments / Channels / Scan / Mini Programs / Search / Nearby); the gateway to the whole ecosystem
- **Gray utilitarian system** — chat backdrop `#EDEDED`, white rows/cards, near-black `#181818` text; calm at a billion-user scale
- **WeChat Green** (`#07C160`) as the sole accent — used with monastic restraint
- **Pale chat-green outgoing bubble** (`#95EC69`) / white incoming — black text, never the brand green for bubbles
- **Tight squarer bubbles** — 6pt radius + a triangular tail toward the sender's avatar
- **Rounded-square avatars** (~8pt) everywhere — never circles; a constant brand tell
- **Red-packet (hóngbāo) card** — the gold-on-red money envelope with an animated opening seal
- **Universal red dot** (`#FA5151`) — the attention primitive on tabs, rows, and avatars
- One dominant 17pt body size, PingFang SC / Noto Sans 400/600, near-absent motion

## Brand Sources

- **WeChat iOS app** — observed component behavior, the Discover grouped-list hub, tailed bubbles, red packet
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) — navigation, Dynamic Type, touch targets
- [Material 3 Guidelines](https://m3.material.io/) — Android `NavigationBar`, custom tailed shapes, typography mapping
- Public brand palette: WeChat Green `#07C160`, Chat Backdrop `#EDEDED`, Outgoing Bubble `#95EC69`
