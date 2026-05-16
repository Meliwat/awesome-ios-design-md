# Things 3 iOS Inspired Design System

Design system docs inspired by the [Things 3 iOS app](https://apps.apple.com/us/app/things-3/id904237743). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Cultured Code's public product UI and design writing; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, checkbox + Magic-Plus + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Pure white canvas** (`#FFFFFF`) with serene, generous whitespace — the calm is the product
- **Things Blue** (`#4F97FF`) as the single accent — selection, primary action, checkbox fill
- **Today-yellow** (`#FFD60A`) star — the one warm note, marking Today and scheduled-today
- **Circular checkbox** with a center-out fill + spring checkmark, then a gentle fade-out
- **Magic-Plus** — a floating blue "+" you can press-and-drag to insert a to-do exactly where you want
- **Project pie-progress** — a quiet circular ring that animates forward as to-dos complete
- **Headings + hairline dividers** — bold 17pt section labels that chunk long projects calmly
- **Sidebar navigation** (no bottom tab bar) — Inbox / Today / Upcoming / Anytime / Someday

## Brand Sources

- [Things — Cultured Code](https://culturedcode.com/things/)
- [Cultured Code — Things Blog](https://culturedcode.com/things/blog/)
- [San Francisco — Apple Fonts](https://developer.apple.com/fonts/) — system typeface
- Public brand palette: Things Blue `#4F97FF`, Today yellow `#FFD60A`, white canvas `#FFFFFF`
