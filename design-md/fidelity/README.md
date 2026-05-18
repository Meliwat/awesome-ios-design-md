# Fidelity iOS Inspired Design System

Design system docs inspired by the [Fidelity Investments iOS app](https://apps.apple.com/us/app/fidelity-investments/id348177453). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Fidelity's public product UI and brand expression; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, portfolio→quote spine + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Calm institutional surfaces** — light `#FFFFFF` / deep evergreen `#0E1411` dark — content is the data
- **Fidelity Green** (`#368727`) + **Heritage Green** (`#00754A`) — brand anchors for actions and money movement
- **Sacred gain/loss color** — gain `#15833E`/`#15B374`, loss `#D32F2F`/`#E5544B` — never decorative, never swapped
- **Total account value as the hero** — 34pt bold numeral, day-change line + sparkline directly below
- **Tabular / monospaced numerals everywhere** — dollar, price, %, and quantity columns align perfectly
- **Portfolio → quote spine** — Summary → Account → Position → Quote → Trade, one calm chrome throughout
- **Sticky green Trade button** — pinned to the bottom safe area of every quote screen
- **Sparkline + range tabs** (1D / 1W / 1M / 1Y / 5Y / All) — the recurring chart unit, active range green-filled
- **Flat hairline holding rows** — ticker chip, name, value, % change; cards only for summary/quote/estimate
- **Five-tab bottom nav** — Summary / Planning / Invest / News / Profile, no tint pill
- **Gold tier accent** (`#C8A24B`) — premium / Private Client Group moments only
- **Quiet motion** — color cross-fade on price ticks, 280ms chart morph, no rolling digits, no bounce

## Brand Sources

- [Fidelity Investments](https://www.fidelity.com/)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL, the recommended free substitute for Fidelity's "Average Sans" grotesque
- Public brand palette: Fidelity Green `#368727`, Heritage Green `#00754A`, semantic gain/loss green/red
- iOS system fallback: SF Pro with `.monospacedDigit()` for all monetary figures
