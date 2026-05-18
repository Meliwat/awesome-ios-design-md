# SoFi iOS Inspired Design System

Design system docs inspired by the [SoFi iOS app](https://apps.apple.com/us/app/sofi-invest-banking-loans/id1191985809). Not the official system. Brand colors, font names, and component patterns are cross-referenced with SoFi's public product UI and brand assets; exact private tokens may differ from production. SoFi services are offered by SoFi-affiliated entities and partner banks.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, gradient member hero, account tiles, cross-sell cards, activity row |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, gradient hero, account tiles, activity row, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, gradient hero, tile grid, activity row + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Dark-native brand** — electric SoFi Blue `#00A0DF` on deep navy `#0A0E27`; navy is the canonical canvas in both themes
- **Member hero gradient** — a `#00A0DF → #1B53C4 → #0A0E27` panel carrying net worth in white at 40pt 800 + a green month-change pill
- **Consolidated account/product tile grid** — 2-column rounded cards (Banking, Invest, Loans, Card) with icon chips and colored sub-stats
- **Cross-sell product cards** — "one member, many products" is the business; rounded cards with a blue-soft icon + value prop
- **Color-coded performance** — gains green `#2FD08A`, losses coral `#FF6B6B`, rewards gold `#F2C14E` (never neutral)
- **Blue brightens to `#29C2FF`** for accents, links, and active tabs so it pops on navy
- **Fully pill-shaped buttons** (500pt radius) — premium fintech, never boxy
- **Heavily rounded tiles** (18pt) lifted off navy with a 1pt `#252C55` border + soft dark shadow
- **Manrope typography** — confident geometric sans; net worth, balances, %s always 700-800 and tabular
- **Surfaces lift navy → `#121736` → `#1B2147`**; soft blue fills at `#0E2A44`; on-blue ink `#042235`
- **Bottom tab bar** — Home, Invest, Banking, Borrow, Me — active in bright blue, no tint pill

## Brand Sources

- [SoFi](https://www.sofi.com/)
- [SoFi Newsroom & Brand](https://www.sofi.com/newsroom/)
- [Manrope (Google Fonts)](https://fonts.google.com/specimen/Manrope) — SIL OFL
- Public brand palette: SoFi Blue `#00A0DF`, navy `#0A0E27`, gradient `#00A0DF → #1B53C4 → #0A0E27`
