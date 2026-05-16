# Flo iOS Inspired Design System

Design system docs inspired by the [Flo iOS app](https://apps.apple.com/us/app/flo-period-pregnancy-tracker/id1038369065). Not the official system. Brand colors, font choices, and component patterns are cross-referenced with Flo's public product presentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Pure-white canvas** with a barely-there blush surface (`#FFF0F3`) — soft and unclinical
- **Flo Coral** (`#FF6B81`) as the lead accent — primary action, active tab, "today" marker
- **Lavender** (`#C5B3E6`) as the phase partner — fertile window / ovulation
- **The cycle wheel** — a circular phase calendar that rotates and morphs as the cycle advances (signature)
- **Reassuring predictions** — calm sentences ("Period in 5 days"), never alarming
- **Symptom-log chip grid** — soft, pill-rounded daily logging
- **Coral-tinted soft shadows** — depth belongs to the blush world, not neutral black
- **Gentle motion** — phase morphs and fades at 200-350ms, nothing harsh

## Brand Sources

- [Flo — official site](https://flo.health)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [Inter typeface (Google Fonts)](https://fonts.google.com/specimen/Inter)
- Public palette: Flo Coral `#FF6B81`, lavender `#C5B3E6`, blush surface `#FFF0F3`, canvas `#FFFFFF`
