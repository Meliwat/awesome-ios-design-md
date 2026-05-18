# Charles Schwab iOS Inspired Design System

Design system docs inspired by the [Charles Schwab iOS app](https://apps.apple.com/us/app/schwab-mobile/id407358186). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Schwab's public product UI and brand expression; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, account→trade spine + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Deep maritime navy surfaces** — light `#FFFFFF` / navy `#0A1622` dark — confident and institutional
- **Schwab Blue** (`#009DDC`) as the single action color on **Schwab Navy** (`#003B5C`)
- **Dark navy text on Schwab Blue** (`#002233`) — the bright blue fails contrast with white
- **Navy gradient account hero** (`#003B5C → #002A42`) carrying Total Value — kept even on dark
- **Sacred gain/loss color** — gain `#16895F`/`#18B07B`, loss `#C8443D`/`#E2564E` — never decorative
- **The trade ticket is the centerpiece** — segmented Buy/Sell + right-aligned tabular fields + estimated-cost card
- **Tabular / monospaced numerals everywhere** — balances, prices, quantities align down every column
- **Account → trade spine** — Accounts → Account detail → Quote → Trade ticket, one calm chrome
- **Flat hairline account rows** — icon, name, masked number, balance, % change
- **2-up Balances tiles** — Cash available / Buying power / Market value / Day change
- **Five-tab bottom nav** — Accounts / Trade / Research / Messages / More, no tint pill
- **Gold tier accent** (`#C8A24B`) — private-client / premium moments only
- **Quiet motion** — color cross-fade on balance ticks, 200ms Buy/Sell slide, 280ms chart morph, no rolling digits

## Brand Sources

- [Charles Schwab](https://www.schwab.com/)
- [Source Sans 3 (Adobe, Google Fonts)](https://fonts.google.com/specimen/Source+Sans+3) — SIL OFL, the recommended free substitute for Schwab's "Charles Modern" humanist sans
- Public brand palette: Schwab Blue `#009DDC`, Schwab Navy `#003B5C`, semantic gain/loss green/red
- iOS system fallback: SF Pro with `.monospacedDigit()` for all monetary figures
