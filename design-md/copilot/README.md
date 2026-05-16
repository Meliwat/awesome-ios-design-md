# Microsoft Copilot iOS Inspired Design System

Design system docs inspired by the [Microsoft Copilot iOS app](https://apps.apple.com/us/app/microsoft-copilot/id6472538445). Not the official system. Brand colors, font names, and component patterns are cross-referenced with **Microsoft Copilot**'s public brand identity and the Fluent design language; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Coil + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Warm Fluent workspace** — white (`#FFFFFF`) light / neutral (`#202020`) dark, layered not flat
- **Fluent acrylic surfaces** — frosted, tinted, blurred prompt bar / sheets / sidebar / chips
- **Copilot flourish gradient** (`#FF6F61 → #FFB900`) — the swirl mark, streaming shimmer, focus accent
- **Fluent Blue** (`#0078D4`) as the functional primary — active send, links, selected toggles
- **Conversational answer cards** — soft 16pt-radius acrylic cards (not bare text)
- **Tone / style toggle** — Creative / Balanced / Precise segmented control shaping responses
- **Acrylic blur-fade + flourish shimmer** — surfaces blur-in (220ms); answers stream warm
- **Segoe UI typeface** (Inter fallback) — weights 400/600/700, Cascadia Code for code, full light+dark parity

## Brand Sources

- [Apple App Store — Microsoft Copilot listing](https://apps.apple.com/us/app/microsoft-copilot/id6472538445)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Copilot flourish `#FF6F61 → #FFB900`, Fluent Blue `#0078D4`
