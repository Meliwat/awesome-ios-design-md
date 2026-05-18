# Substack iOS Inspired Design System

Design system docs inspired by the [Substack iOS app](https://apps.apple.com/us/app/substack/id1500960849). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Substack's public product UI and brand presentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, post reader + Subscribe card + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **The post reader is the product** — a clean white page (`#FFFFFF` light, soft charcoal `#121212` dark), no boxes
- **Serif reads, sans operates** — Source Serif 4 for title/dek/body/pull-quote, Inter for buttons/tabs/meta
- **Long-form body** — serif 18pt, 1.65 line-height, ~24pt editorial measure; built for 15–20 minute reads
- **The italic serif dek** — the magazine signal under every title
- **Substack Orange is the one rationed accent** (`#FF6719`) — Subscribe CTA, active tab, unread dot, liked heart, pull-quote rule
- **The Subscribe card** — the one tinted block (`#FFF7F2` wash, `#FFD9C2` border); stands out by color, not shadow
- **Inbox is home** — a chronological subscription queue with uppercase orange publication labels + unread dots
- **Pull-quotes** — 3pt orange left border, italic serif
- **The reading column is flat** — no cards, borders, or shadows; only sheets and the sticky bar float
- **One link blue** (`#4A6FE3`) for inline links — the only non-orange accent; everything else is paper + ink + grey
- **Comments are sans** — conversation set in Inter, never the serif body
- **Orange held identical light and dark** — the single constant conversion signal

## Brand Sources

- [Substack](https://substack.com/)
- [Source Serif 4 by Frank Grießhammer / Adobe (Google Fonts)](https://fonts.google.com/specimen/Source+Serif+4) — SIL OFL
- [Spectral by Production Type (Google Fonts)](https://fonts.google.com/specimen/Spectral) — SIL OFL (alternative reading serif)
- [Inter by Rasmus Andersson (Google Fonts)](https://fonts.google.com/specimen/Inter) — SIL OFL
- Public brand palette: Substack Orange `#FF6719`, paper white `#FFFFFF`, reading ink `#1F1F1F`, link blue `#4A6FE3`
