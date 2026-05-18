# Chime iOS Inspired Design System

Design system docs inspired by the [Chime iOS app](https://apps.apple.com/us/app/chime-mobile-banking/id836215269). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Chime's public product UI and brand assets; exact private tokens may differ from production. Chime is a financial technology company, not a bank — banking services provided by partner banks.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, balance hero, transaction row, SpotMe banner, instant alert |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, balance hero, transaction row, instant alert, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, gradient hero, transaction row, instant alert + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Chime Mint is the whole personality** — `#1EC677` / `#00D67E` applied head-to-toe, not a lone green button
- **Balance hero card** — a full `#1EC677 → #12A862` gradient panel, balance at 44pt 700 in near-black mint ink `#062014`, cents as a superscript, with a mint-glow shadow `rgba(30,198,119,0.45)`
- **SpotMe banner** — fee-free overdraft shown with a glyph + status + available limit in bright mint `#12B981` / `#2EE6A6`
- **Instant transaction alerts** — push + slide-down in-app toast with a solid-mint check icon the moment money moves
- **Spending is neutral, not negative** — everyday card transactions render in neutral ink; red `#E5484D` / `#FF6B6B` is reserved for declined/failed/negative only
- **Status chips** — Posted / Pending / SpotMe covered / Declined in mint / amber `#E08600` / bright-mint / coral
- **Early direct deposit celebrated inline** — "2 days early" beside the deposit row, a core trust signal
- **Heavily rounded geometry** — 24pt hero, 14pt buttons, 14pt tiles, pill chips — softness is the brand
- **DM Sans typography** — geometric, rounded, approachable; numbers always 700 and tabular so amounts align
- **Soft off-white canvas** `#F6FBF8` light / deep green-charcoal `#0E1411` dark — calm, never austere or pure black
- **Bottom tab bar** — Home, Move, Card, Grow, Me — active in bright mint, no tint pill

## Brand Sources

- [Chime](https://www.chime.com/)
- [Chime Newsroom & Brand](https://www.chime.com/newsroom/)
- [DM Sans (Google Fonts)](https://fonts.google.com/specimen/DM+Sans) — SIL OFL
- Public brand palette: Chime Mint `#1EC677` / `#00D67E`, near-black mint ink `#062014`, SpotMe bright mint
