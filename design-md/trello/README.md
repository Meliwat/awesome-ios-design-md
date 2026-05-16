# Trello iOS Inspired Design System

Design system docs inspired by the [Trello iOS app](https://apps.apple.com/us/app/trello-manage-team-projects/id461504587). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Trello's public brand and the Atlassian Design System; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, motion + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **No fixed canvas** — the **board background** (solid `#0079BF`, gradient, or photo) sets the screen
- **Pale lists** (`#F1F2F4`) of white cards floating on the backdrop
- **Horizontal kanban** scroll through columns — and **no bottom tab bar**
- **Trello Blue** (`#0C66E4`) for actions; classic `#0079BF` is the default board color
- **Card label palette** — a fixed six-color functional swatch set (the one free-color zone)
- **Card lift on drag** — scale 1.03, strong navy-tinted shadow, placeholder gap
- **Navy-tinted shadows** (`rgba(9,30,66,…)`) so depth reads on color and pale surfaces
- **System font** (SF Pro / Inter) — legibility on any backdrop is the point

## Brand Sources

- [Apple App Store — **Trello**: Manage Team Projects](https://apps.apple.com/us/app/trello-manage-team-projects/id461504587)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [Material 3 Design Guidelines](https://m3.material.io/) (for the Android port)
- Public brand palette: action blue `#0C66E4`, classic board blue `#0079BF`, navy ink `#172B4D`
