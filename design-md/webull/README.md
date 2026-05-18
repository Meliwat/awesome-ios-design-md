# Webull iOS Inspired Design System

Design system docs inspired by the [Webull iOS app](https://apps.apple.com/us/app/webull-investing-trading/id1179213067). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Webull's public product UI and brand expression; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, candlestick chart + order ladder + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Dark-native only** — near-black canvas `#0B0E11`; no meaningful light mode (it's a trading terminal)
- **Webull blue→cyan brand gradient** (`#1B9EFB → #20D5C4`) — logo, Trade action, selected timeframes, paper badge
- **Pervasive saturated performance color** — up `#00C076`, down `#FA5252` on candles, sparklines, chips, ladder, buttons
- **Candlestick chart as centerpiece** — full-bleed, timeframe strip, dashed `#1B9EFB` prev-close, near-invisible `#1A1F26` grid
- **Quote screen + watchlist spine** — Quotes → Symbol → Chart → Trade
- **Order-book ladder** — bid/ask rows with right-anchored depth bars at ≈14% opacity + mid/spread divider
- **Split Buy (green) / Sell (red) docked pair** — equal-width, color-coded, on the quote screen
- **Options chain** — Call | Strike | Put three-column table, green calls / red puts
- **Tabular numerals everywhere** — the ladder, chain, and prices align to the decimal
- **Colored last price** — the big quote price takes the up/down color, not neutral text
- **Cyan PAPER badge** — Webull's signature paper-trading indicator
- **Region-aware up/down** — US default up=green; many Asia markets invert to up=red (never color-only)

## Brand Sources

- [Webull](https://www.webull.com/)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL, the recommended free substitute (with tabular figures enabled)
- Public brand palette: Webull Blue `#1B9EFB`, Webull Cyan `#20D5C4`, market up `#00C076` / down `#FA5252`
- iOS system fallback: SF Pro with `.monospacedDigit()` for all numeric grids (ladder, option chain)
