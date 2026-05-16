# LINE iOS Inspired Design System

Design system docs inspired by the **LINE iOS app**. Not the official system. Brand colors, font names, and component patterns are cross-referenced with LINE's public brand presentation and observable in-app behavior; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Oversized bubble-less sticker** — stickers render large (~140pt) directly on the chat backdrop with a bounce-in
- **Periwinkle chat backdrop** (`#8CABD9`) by default — not white; user-themeable
- **Pale chat-green outgoing bubble** (`#C5F0B1`) / white incoming — text always black, never the brand green for bubbles
- **LINE Green** (`#06C755`) for "go" chrome — active tab, primary buttons, official-account badge, toggles
- **Official-account badge** — a green verification mark inline after a name; LINE's trust primitive
- **Floating meta** — timestamp + "Read" sit outside the bubble on the backdrop, not inside
- **Sticker shop** — a first-class economy with priced tiles, free packs
- **Super-app tab bar** — Home / Talk / VOOM / News / Wallet; Noto Sans 400/700, CJK-friendly

## Brand Sources

- **LINE iOS app** — observed component behavior, bubble-less sticker model, official-account badge
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) — navigation, Dynamic Type, touch targets
- [Material 3 Guidelines](https://m3.material.io/) — Android `NavigationBar`, custom tail shapes, typography mapping
- Public brand palette: LINE Green `#06C755`, Chat Backdrop `#8CABD9`, Outgoing Bubble `#C5F0B1`
