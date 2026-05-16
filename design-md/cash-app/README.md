# Cash App iOS Inspired Design System

Design system docs inspired by the [Cash App iOS app](https://apps.apple.com/us/app/cash-app/id711923939). Not the official system. Brand colors (Cash Green `#00D632`), typography (Cash Sans / Cash Sans Mono), and component patterns are cross-referenced with Block's public design moments, Cash App's marketing, and the app's visible UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, components, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, $-amount keypad engine + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |
| `README.md` | This file — summary, signature moves, brand sources |

## Signature Moves

- **Pure black canvas** (`#000000`) — not warm gray, not `#111`; matte ink-on-paper aesthetic
- **Cash Green** (`#00D632`) is the only accent — Pay CTA, success states, active tab glyph, success amounts
- **The giant $-amount keypad** — `$` + amount in Cash Sans Mono at 96pt+ Bold dominating the upper half of the Pay screen, with auto-shrink as digits add
- **Black-on-green Pay button** — black text on the green button (NOT white) is core to the brand
- **Icon-only tab bar** — five tabs (Money / Card / Pay / Activity / Bitcoin), no labels, ever, with the center Pay tab visually larger
- **Cash Sans Black weight** for screen titles like "Money", "Card", "Activity" at 28pt — the visual signature of the home screens
- **Cash Sans Mono with tabular numerals** for every $-amount and every $Cashtag — non-negotiable
- **Matte black Cash Card** with the user's `$Cashtag` etched in a chosen accent color — no shadow, hairline border
- **Random saturated avatar colors** (pink, orange, blue, green, purple) on Activity rows for users without photos
- **Commissioned artist-illustration Boost glyphs** — hand-drawn, NOT stock iconography
- **Flat depth philosophy** — no shadows; depth is fill-color contrast (`#000` → `#0F0F0F` → `#1A1A1A`)

## Brand Sources

- [Cash App](https://cash.app) — official site, brand color exposure
- [Cash App on the App Store](https://apps.apple.com/us/app/cash-app/id711923939)
- [Block, Inc. Design](https://block.xyz/) — parent company design moments
- Cash App marketing (Instagram, Twitter, YouTube) for the urban / streetwear visual register
- Public brand palette: Cash Green `#00D632`, pure black canvas, Cash Sans / Cash Sans Mono custom typefaces
- Block's annual letters and design talks have referenced the in-house Cash Sans typography work
