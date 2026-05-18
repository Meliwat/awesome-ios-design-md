# Apple Fitness iOS Inspired Design System

Design system docs inspired by the [Apple Fitness iOS app](https://apps.apple.com/us/app/apple-fitness/id1208224953). Not the official system. Brand colors, the Activity-ring palette, and component patterns are cross-referenced with Apple's public product UI and Human Interface Guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, Activity rings, ring hero card, metric tiles, Fitness+ card |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, SVG rings, expo-blur frosted overlays, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, Canvas rings, navigation + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **True-black canvas** (`#000000`) on iPhone — the three rings are the brightest thing on screen
- **The brand is the three rings** — Move `#FA114F`, Exercise `#92E82A`, Stand `#1EE4E1`, immutable
- **Each ring on a 22%-opacity track of its own color** — thick, round-capped, glowing arcs
- **Grouped system cards** (`#1C1C1E`, 14–18pt radius) — depth from surface lightness, not shadows
- **Apple label-opacity ramp** — text at 100% / 60% / 30% white, not fixed gray hexes
- **Move-pink `#FF375F`** as the single chrome accent — active tab, links, primary CTA
- **Fitness+ purple** `#C969E0` for Fitness+ surfaces; ring colors never restyle
- **Cinematic Fitness+ artwork** — 200pt full-color thumbnails with frosted-glass overlays
- **Tabular numerals** so the ring legend and metric tiles never jitter
- **Rings sweep from 0** on open (staggered ~1s) and **celebrate on close** with a particle burst + success haptic
- **System blur materials** for the tab bar and frosted overlays — the Apple vibrancy look
- **No tint pill** in the 3-tab bar — Apple's tint-only active style

## Brand Sources

- [Apple Fitness](https://www.apple.com/apple-fitness-plus/) · [Apple Watch Activity rings](https://www.apple.com/apple-watch/)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) — system colors, materials, text styles
- System font: **SF Pro** (Apple) — cross-platform fallback **[Inter](https://rsms.me/inter/)** (SIL OFL)
- Public product UI: the three Activity-ring colors (Move `#FA114F`, Exercise `#92E82A`, Stand `#1EE4E1`), grouped dark surfaces, Fitness+ artwork shelves
