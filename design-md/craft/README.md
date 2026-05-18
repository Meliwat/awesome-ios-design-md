# Craft iOS Inspired Design System

Design system docs inspired by the [Craft iOS app](https://apps.apple.com/us/app/craft-docs-and-notes-editor/id1487937127). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Craft's public product UI and marketing site; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `LinearGradient` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, brand gradient, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, brand `Brush`, Material 3 `Typography`, composables, card-block editor + `/` inserter + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Blue→purple gradient** (`#2F5BEA` → `#6E56CF`) on every primary action — the constant brand identity
- **Card-forward blocks** — nested pages, links, toggles, to-dos render as rounded cards with icon chips + soft shadows
- **Daily Note** — auto-created date-stamped document, surfaced as a card and a dedicated entry point
- **`/` block inserter** — type `/` to insert any block type (Text, Page, To-do, Image, Code, Table, Toggle)
- **Warm off-white canvas** (`#FCFCFD`) light / soft charcoal (`#1A1A1E`) dark — never pure white or pure black
- **Soft warm text** `#1C1C22` — NOT pure black; editorial document rhythm, not UI rhythm
- **Rounded-square FAB** (14pt radius) with a blue-tinted shadow `0 8px 20px -6px rgba(47,91,234,0.6)` — never a circle
- **Generous corner radii** (10–20pt) + diffuse low-opacity shadows — tactile "pick-it-up" feel
- **Doc title at 28pt extrabold** (Inter 800) — the single most expressive type moment
- **Cross-document link cards** with backlinks — pages reference each other inline
- **Solid `#2F5BEA`** for small accents (toggles, links, focus ring, active tab — no tint pill)
- **Fluid spring motion** — sheets, cards, the `/` menu animate with iOS-native spring physics

## Brand Sources

- [Craft](https://www.craft.do/)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- [Lora (Google Fonts)](https://fonts.google.com/specimen/Lora) — SIL OFL
- [JetBrains Mono](https://www.jetbrains.com/lp/mono/) — SIL OFL
- Public brand palette: Craft Blue `#2F5BEA`, Craft Purple `#6E56CF`, blue→purple gradient, soft text `#1C1C22`
