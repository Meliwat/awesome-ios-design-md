# Linear iOS Inspired Design System

Design system docs inspired by the [Linear iOS app](https://apps.apple.com/us/app/linear/id1453523931). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Linear's public product UI and the company's design writing; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, command menu + drawer + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Near-OLED canvas** (`#08090A`) — darker than typical "dark mode", part of the identity
- **Linear Purple** (`#5E6AD2`) as the ONLY accent — primary action, focus, and selection
- **Dense issue rows** (~44pt) — status glyph → identifier → title → priority bars → assignee
- **Command menu (Cmd+K)** — a centered sheet that drives every action, keyboard-first
- **Iconographic status system** — drawn backlog/started/done glyphs, not colored text badges
- **Cycle progress bar** — a 6pt purple track measuring completed scope against the cycle
- **Inter at 400/500/600** with tabular numerics; monospace for identifiers and shortcuts
- **No bottom tab bar** — navigation is a slide-over sidebar; speed is the aesthetic

## Brand Sources

- [Linear](https://linear.app/)
- [Linear — Method (how Linear builds)](https://linear.app/method)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- Public brand palette: Linear Purple `#5E6AD2`, near-black canvas `#08090A`, cool gray text `#8A8F98`
