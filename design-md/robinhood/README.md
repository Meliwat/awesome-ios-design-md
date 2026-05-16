# Robinhood iOS Inspired Design System

Design system docs inspired by the [Robinhood iOS app](https://apps.apple.com/us/app/robinhood-investing-trading/id938003185). Not the official system. Brand colors (Robinhood Green `#00C805`, Robinhood Red — actually orange — `#FF5000`), typography (Capsule Sans Text / Display), and component patterns are cross-referenced with Robinhood's public brand evolution, the XYZ Type Capsule Sans release, and the app's visible UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, components, haptics, Swift Charts |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, gesture-based chart scrubber |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Canvas portfolio chart + draggable scrubber + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |
| `README.md` | This file — summary, signature moves, brand sources |

## Signature Moves

- **White canvas, true black text** — clean tech-product aesthetic, `#FFFFFF` background with `#000000` primary text (no warm grays)
- **Robinhood Green** (`#00C805`) for upward portfolio movement, profitable positions, and the buy CTA; **Robinhood Red** (`#FF5000` — actually a saturated orange) for downward and sell — color is bound to direction
- **The portfolio chart with scrubber** — full-bleed 2pt line graph in the day's color, no fill below, draggable scrubber that updates the hero portfolio value in real time
- **40pt portfolio hero in Capsule Sans Display** — the giant tabular $-amount at the top of the Investing tab is the visual anchor
- **Black Trade button** (`#000000`) — the universal "Trade" CTA is achromatic; green and orange are reserved for Buy/Sell within the trade flow
- **1D / 1W / 1M / 3M / YTD / 1Y / 5Y / ALL range chips** — text-only with a 2pt underline in the chart line's color when active
- **Tabular numerals everywhere** — every price, percentage, share count uses `fontVariant: ['tabular-nums']` so columns of numbers align visually
- **Capsule Sans Text** — proprietary geometric sans by XYZ Type (2021), with Capsule Sans Display for hero values
- **Tracked-wide ticker symbols** — AAPL, GOOGL, BTC at +0.3pt letter spacing — branded micro-monograms
- **Essentially flat** — minimal shadows (0.04 opacity), no gradient fills on the chart, no heavy elevation; the line graph is the only visual lift

## Brand Sources

- [Robinhood](https://robinhood.com) — official site, brand color exposure
- [Robinhood on the App Store](https://apps.apple.com/us/app/robinhood-investing-trading/id938003185)
- [XYZ Type — Capsule Sans](https://xyztype.com/) — type foundry that drew Capsule for Robinhood (~2021)
- Public brand palette: Robinhood Green `#00C805`, Robinhood Red `#FF5000` (saturated orange), Capsule Sans Text + Display custom typefaces, tabular numerals
- Robinhood's annual reports and brand evolution posts describe the 2021 typography transition to Capsule Sans
