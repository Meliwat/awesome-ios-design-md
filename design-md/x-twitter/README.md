# X (Twitter) iOS Inspired Design System

Design system docs inspired by the [X iOS app](https://apps.apple.com/us/app/x/id333903271). Not the official system. Brand colors, font names, and component patterns are cross-referenced with X's public brand documentation and legacy Twitter design references; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `preview.md` | Interactive token catalog on the Spectr gallery |

## Signature Moves

- **Pure black OLED canvas** (`#000000`) with dim-mode `#15202B` and light-mode `#FFFFFF` alternates
- **Twitter Blue** (`#1D9BF0`) as the structural accent — links, replies, active tab underline, verified checkmarks
- **Three action colors** — reply blue, repost green (`#00BA7C`), like pink (`#F91880`) — one per post action
- **Hairline-separated feed** — 1pt `#2F3336` dividers between posts, no cards, no shadows
- **Chirp font** (proprietary) at weights 400/500/700 only — grotesque sans with distinctive italic
- **Icon-only tab bar** — five tabs, no labels, the recognizable X pattern
- **Inverted Post FAB** — white-on-black / black-on-white floating compose circle
- **Like burst animation** — 6-particle hexagonal burst + scale bounce + count tick + haptic

## Brand Sources

- [X Press — About section and brand asset guidelines](https://about.x.com/en/who-we-are/brand-toolkit)
- [Chirp font design story — Grilli Type & Swiss Typefaces](https://grillitype.com/typefaces/gt-america/in-use/twitter)
- Public palette: Twitter Blue `#1D9BF0`, Repost Green `#00BA7C`, Like Pink `#F91880`, Canvas `#000000`
