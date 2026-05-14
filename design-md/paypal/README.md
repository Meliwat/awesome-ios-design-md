# PayPal iOS Inspired Design System

Design system docs inspired by the [PayPal iOS app](https://apps.apple.com/us/app/paypal-send-shop-manage/id283646709). Not the official system. Brand colors (PayPal Blue `#003087`, PayPal Sky `#0070BA`), typography (PayPal Sans Big / PayPal Sans Small by Monotype), and component patterns are cross-referenced with PayPal's public brand evolution (2014 and 2024 refreshes), Monotype's case study, and the app's visible UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, components, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |
| `README.md` | This file — summary, signature moves, brand sources |

## Signature Moves

- **The dual-blue P-P wordmark** — italicized P in PayPal Sky `#0070BA` overlapping a second P in PayPal Blue `#003087` — never rendered in a single color
- **PayPal Blue** (`#003087`) for primary CTAs ("Send", "Continue") and active tab; **PayPal Sky** (`#0070BA`) as the secondary accent and link color
- **Balance card on Wallet tab** — white surface, 16pt corner radius, subtle shadow, $-balance in PayPal Sans Big 36pt 700 tabular at the top, Add Money / Transfer pills below
- **Send Money fullscreen** — giant $-amount in PayPal Sans Big 56pt at center, numeric keypad below, live-updating "Send $X.XX" CTA at the bottom
- **Rich circular activity icons** — colored circles with white SF Symbol glyphs, color-coded by transaction type (Sent = blue, Received = green, Card = sky, Reward = amber)
- **Soft-pill primary CTAs** — radius equals half-height (48pt button → 24pt radius), PayPal Blue background, white PayPal Sans Big 17pt 700 text
- **PayPal Sans Big + PayPal Sans Small** — proprietary Monotype family (2018); Big for hero/button/amount, Small for body/metadata
- **Tabular numerals everywhere** — every $-amount, transaction value, percentage uses `fontVariant: ['tabular-nums']`
- **Status pills with tinted backgrounds** — `#E4F5EA` for Completed (green text), `#FCE5E8` for Failed (red text), `#FFF6E0` for Pending (amber text)
- **Primary text `#001435`** — almost-black with a slight blue tint, NOT pure black — the warmth aligns with the brand
- **Gentle elevation** — `rgba(0,0,0,0.06)` shadows on the balance card; PayPal reads as a card wallet, not a flat plane

## Brand Sources

- [PayPal](https://www.paypal.com) — official site, brand color exposure
- [PayPal on the App Store](https://apps.apple.com/us/app/paypal-send-shop-manage/id283646709)
- [Monotype — PayPal Sans case study](https://www.monotype.com/) — type foundry that designed PayPal Sans Big + Small (~2018)
- 2014 PayPal rebrand by fuseproject / Pentagram — introduced the dual-blue P-P logomark
- 2024 PayPal refresh — modernized the wordmark while preserving the dual-blue overlap
- Public brand palette: PayPal Blue `#003087`, PayPal Sky `#0070BA`, PayPal Cobalt `#001C64`
