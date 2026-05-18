# Acorns iOS Inspired Design System

Design system docs inspired by the [Acorns iOS app](https://apps.apple.com/us/app/acorns-invest-spare-change/id883324671). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Acorns' public product UI, brand site, and design posts; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views incl. allocation donut |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, react-native-svg donut, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Canvas donut + Round-Ups + Found Money + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Dual brand**: Acorns Oak purple (`#5A2B82`) for trust/structure + Acorn Green (`#6FBF4E`) for action and growth
- **Invest dashboard** as the signature screen — portfolio total, allocation donut, Round-Ups, Found Money
- **Allocation donut** — multi-segment ring in a fixed warm palette (Oak `#5A2B82`, Green `#6FBF4E`, Teal `#45C2B0`, Gold `#F2C84B`) with a center portfolio-style label + legend
- **Round-Ups card** — Oak gradient card (`#5A2B82 → #7B43A8`) with accumulated spare change and the $5 invest threshold
- **Found Money / Earn** — partner-cashback feed with green "+$" amounts that auto-invest
- **Big friendly numbers** — the portfolio total is the largest type (Nunito Sans w900), gains in green with a ▲
- **Heavy rounded type** (Nunito Sans 800–900 fallback) — friendly, trustworthy, approachable
- **Motivational plain-English copy** — "Your money is hard at work", "Small amounts add up"
- **Pill buttons** — green primary (dark forest `#14302A` text for AA), Oak purple secondary
- **Deep-aubergine dark mode** (`#14121A`) — tinted from Oak, NOT pure black; donut stays full-color
- **No tint pill on the active tab** — active is green icon + green label only
- **Donut sweep + number ticker** — growth feels alive on load and on change

## Brand Sources

- [Acorns](https://www.acorns.com/)
- [Acorns Newsroom & brand](https://www.acorns.com/about/)
- Nunito Sans (SIL OFL) used as the free substitute for Acorns' rounded brand face
- Public brand palette: Acorns Oak `#5A2B82`, Acorn Green `#6FBF4E`
