# Crypto.com iOS Inspired Design System

Design system docs inspired by the [Crypto.com iOS app](https://apps.apple.com/us/app/crypto-com-buy-bitcoin-eth/id1262148500). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Crypto.com's public product UI, the Crypto.com Visa card pages, and the company's brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views including the metallic Visa card |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, expo-linear-gradient card, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, gradient Visa card + floating Trade tab |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Navy-tinted dark canvas** (`#03060F` → `#0B1426`) — cool blue undertone, never neutral gray
- **Two-blue system** — Brand Navy (`#002D74` / `#103F68`) is decorative (hero gradient, banners); Electric Accent (`#1199FA`) is interactive (CTAs, links, active tab)
- **The metallic Visa card is the hero** — tiered brushed-metal gradients, gold (`#C8A24A`) embossing, masked mono PAN, italic VISA, heavy `0 14px 30px` shadow
- **Card tiers map to cashback** — Midnight Blue 1% → Ruby Steel 2% → Jade Green 3% → Obsidian 5% (flagship)
- **Total-balance hero** in Roboto Mono at the top of Home with today's P&L
- **Pill everything** — fully-rounded CTAs and 14–18pt card radii; premium, not terminal-tight
- **Floating center Trade tab** — a `#1199FA` circle lifted above the bar with an accent-tinted glow
- **Quick-action circle row** — Deposit / Send / Trade / Earn in 46pt accent circles
- **Monospace money** — balances, prices, APR, %, the card PAN all in Roboto Mono with tabular figures
- **Market semantics** — green (`#00C08B`) up/buy, red (`#F6485D`) down/sell; never inverted
- **Earn rows** — APR in mono green, term chips (Flexible / 1M / 3M), allocation rings
- **One large navy moment** — deep brand navy fills only the promo/brand banner; elsewhere it's a tint

## Brand Sources

- [Crypto.com](https://crypto.com/)
- [Crypto.com Visa Card](https://crypto.com/cards) — tiered metal cards & cashback structure
- [Manrope by Mikhail Sharanda](https://manropefont.com/) — SIL OFL (open stand-in for the UI grotesque)
- [Roboto Mono](https://fonts.google.com/specimen/Roboto+Mono) — Apache 2.0
- Public brand palette: brand navy `#002D74`, accent blue `#1199FA`, up `#00C08B`, down `#F6485D`
