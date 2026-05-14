# Coinbase iOS Inspired Design System

Design system docs inspired by the [Coinbase iOS app](https://apps.apple.com/us/app/coinbase-buy-bitcoin-ether/id886427730). Not the official system. Brand colors (Coinbase Blue `#0052FF`), typography (Coinbase Sans / Display / Mono by Frere-Jones Type), and component patterns are cross-referenced with Coinbase's 2017 and 2021 brand evolutions, Frere-Jones Type's public case study, and the app's visible UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, components, haptics, Swift Charts |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, sparkline SVG |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |
| `README.md` | This file — summary, signature moves, brand sources |

## Signature Moves

- **Coinbase Blue** (`#0052FF`) as the single brand accent — primary CTAs, active tab, link color, the geometric C-mark logomark
- **The "C" Coin mark** — perfect circle with a horizontal line through it, used as both the brand logomark and the loading indicator (slow 1500ms rotation)
- **Coinbase Display for hero, Coinbase Sans for body, Coinbase Mono for technical readouts** — three-variant family from Frere-Jones Type (2021)
- **Portfolio chart with scrubber** — 2pt line full-bleed, no fill below, vertical scrubber drag updates the hero portfolio value in real time
- **Asset rows with brand-color icons** — 40pt circular icons in each asset's official brand color (Bitcoin orange, ETH purple, USDC blue) with white glyph centered
- **Mini sparkline inside each asset row** — 56pt × 20pt inline SVG line graph showing 24h trend, colored green or red by direction
- **4-up action quad** (Buy / Sell / Send / Receive) at the top of every asset detail screen — equal-weight buttons in Surface Gray cards with Coinbase Blue icons
- **Tabular numerals everywhere** — every $-amount, percentage, quantity uses `fontVariant: ['tabular-nums']` for clean institutional column alignment
- **Tracked-wide ticker symbols** — BTC, ETH, USDC at +0.3pt letter spacing — branded micro-monograms
- **Coinbase Black `#0A0B0D` text** — almost-black with a subtle cool tint that aligns with the blue brand identity, NOT pure black
- **Institutional minimal elevation** — `rgba(0,0,0,0.04-0.08)` shadows; the asset list density and chart are the visual lift

## Brand Sources

- [Coinbase](https://www.coinbase.com) — official site, brand color exposure
- [Coinbase on the App Store](https://apps.apple.com/us/app/coinbase-buy-bitcoin-ether/id886427730)
- [Frere-Jones Type — Coinbase Sans](https://frerejones.com/) — type foundry that designed Coinbase Sans / Display / Mono (~2021)
- 2017 Coinbase rebrand introduced the geometric C-mark logomark
- 2021 brand evolution introduced the custom Frere-Jones type system
- Public brand palette: Coinbase Blue `#0052FF`, Coinbase Black `#0A0B0D`; crypto-specific brand colors (Bitcoin `#F7931A`, ETH `#627EEA`, USDC `#2775CA`) drawn from each token's official identity
