# Starbucks iOS Inspired Design System

Design system docs inspired by the [Starbucks iOS app](https://apps.apple.com/us/app/starbucks/id331177714). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Starbucks' public brand materials and the Siren Brand Identity guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, Stars ring, size selector, category card, pump stepper |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, Reanimated SVG Stars ring, pump stepper, scan-to-pay |
| `preview.md` | Link to the interactive Spectr gallery |

## Signature Moves

- **Rewards Stars dashboard** — 180pt gold-gradient progress ring with Stars count center and "X Stars until reward" subtitle
- **Siren Green** (`#00704A`) as the primary CTA — the brand's defining color
- **House Green** (`#1E3932`) as the warm dark canvas — NOT pure black, it's deep forest green
- **Gold Star** (`#CBA258`) for rewards, Stars counts, and tier badges
- **Size selector pill row** — Short / Tall / Grande / Venti / Trenta with escalating cup icons
- **Category grid** — photography-first cards (Hot Coffees / Cold Coffees / Refreshers / Frappuccino / Tea / Bakery)
- **Drink builder** — size selector, milk picker, syrup pumps with +/− steppers
- **SoDo Sans typography** — humanist proprietary face designed by Dalton Maag (2020)
- **Warm White canvas** (`#F9F9F9`) — softer than pure white for a welcoming feel
- **Scan to Pay** — QR/barcode card with Stars balance header and Reload button

## Brand Sources

- [Starbucks Stories (corporate brand)](https://stories.starbucks.com/brand-standards/)
- [SoDo Sans typeface by Dalton Maag (2020)](https://www.daltonmaag.com/work/starbucks)
- Public brand palette: Siren Green `#00704A`, House Green `#1E3932`, Accent Green `#D4E9E2`
