# Microsoft Teams iOS Inspired Design System

Design system docs inspired by the [Microsoft Teams iOS app](https://apps.apple.com/us/app/microsoft-teams/id1113153706). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Microsoft's public Fluent design language and Teams' product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Teams tree + join bar + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Full dual identity** — light (`#F5F5F5` / `#FFFFFF`) and dark (`#1F1F1F` / `#2D2C2C`), both first-class
- **Teams Purple** — `#6264A7` light, `#5B5FC7` dark — primary action, active tab, brand chrome
- **Presence dots** — green / red / yellow / gray — the one place Teams uses loaded semantic color
- **Teams → Channels tree** — an expandable two-level list with a purple active-channel bar
- **Message cards** — full-width cards (not bubbles) with reactions and a reply/thread count
- **Meeting join bar** — a prominent purple bar that pulses while a meeting is live
- **Fluent rounding & soft elevation** — 8–12pt radii, low-contrast shadows, surface steps in dark
- **Segoe UI 400/600/700** — semibold names, regular messages, bold headers

## Brand Sources

- [Microsoft Teams](https://www.microsoft.com/microsoft-teams/group-chat-software)
- [Microsoft Fluent Design System](https://fluent2.microsoft.design/)
- [Microsoft Design](https://microsoft.design/)
- Public brand palette: Teams Purple `#6264A7` (light) / `#5B5FC7` (dark), presence green `#6BB700`, busy red `#C4314B`
