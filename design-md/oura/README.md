# Oura iOS Inspired Design System

Design system docs inspired by the [Oura iOS app](https://apps.apple.com/us/app/oura/id1043837948). Not the official system. Brand colors, font choices, and component patterns are cross-referenced with Oura's public product presentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Cool-charcoal canvas** (`#0B0B0F`) — night, with a faint blue cast, not pure black
- **The score ring** (0–100) — the central recurring object, fills clockwise with a rounded cap
- **Tri-domain color language** — Readiness teal `#4FD1C5`, Sleep indigo `#7C6FF0`, Activity amber `#F5A623`
- **Color is information** — a domain's hue follows it across ring, contributors, and trends
- **Contributor bar list** — horizontal mini-bars breaking a score into its inputs
- **Score reveal** — the arc sweep and the central number count-up land together (~900ms)
- **Tabular figures everywhere** — scores, deltas, metrics align in columns and never reflow
- **Neutral-white tab bar** — domain color lives in content, never on chrome

## Brand Sources

- [Oura — official site](https://ouraring.com)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [Inter typeface (Google Fonts)](https://fonts.google.com/specimen/Inter)
- Public palette: Readiness teal `#4FD1C5`, Sleep indigo `#7C6FF0`, Activity amber `#F5A623`, canvas `#0B0B0F`
