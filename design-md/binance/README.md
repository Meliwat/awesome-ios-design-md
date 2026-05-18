# Binance iOS Inspired Design System

Design system docs inspired by the [Binance iOS app](https://apps.apple.com/us/app/binance-buy-bitcoin-crypto/id1436799971). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Binance's public product UI, the Binance Design brand pages, and the company's design posts; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, markets list + order book + trade ticket |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Dark-native canvas** (`#0B0E11`) — the trading terminal was designed in dark; light is an afterthought
- **One brand accent** — Binance Yellow (`#F0B90B`) for the primary CTA, active tab, and links; black text on it, never white
- **Absolute market semantics** — green (`#0ECB81`) = up/buy/bid, red (`#F6465D`) = down/sell/ask; never inverted across themes
- **Sans/mono split** — UI words in IBM Plex Sans; every number in IBM Plex Mono with tabular figures so columns align
- **Dense markets list** — 12pt rows, 1pt `#2B3139` hairlines, coin icon + pair + volume + mono price + solid colored % pill
- **Depth-shaded order book** — translucent quantity bars (`rgba(246,70,93,0.14)` ask / `rgba(14,203,129,0.14)` bid) grow from the trailing edge
- **Price-tick flash** — a cell flashes green/red ~150ms when its price updates; the app's heartbeat
- **Candlestick chart** — bullish `#0ECB81` / bearish `#F6465D` candles, grid `#2B3139` @ 40%, time-interval chips
- **Buy/Sell trade ticket** — green/red segmented toggle, 25/50/75/100% slider chips, full-width colored submit
- **Convert flow** — single-screen from/to swap with one yellow confirm and a locked-rate countdown
- **Tight radii on trading UI** — 2pt depth bars, 4pt pills/chips, 8pt buttons/inputs
- **Surface-tone elevation** — layers separated by `#0B0E11 → #181A20 → #1E2026 → #2B3139` + 1pt borders, not heavy shadows

## Brand Sources

- [Binance](https://www.binance.com/)
- [Binance Design](https://www.binance.com/en/brand) — Binance Yellow `#F0B90B`
- [IBM Plex Sans](https://www.ibm.com/plex/) — SIL OFL (open stand-in for BinancePlex)
- [IBM Plex Mono](https://www.ibm.com/plex/) — SIL OFL (open stand-in for BinancePlex Mono)
- Public brand palette: Binance Yellow `#F0B90B`, up green `#0ECB81`, down red `#F6465D`, dark canvas `#0B0E11`
