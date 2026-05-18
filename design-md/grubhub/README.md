# Grubhub iOS Inspired Design System

Design system docs inspired by the [Grubhub iOS app](https://apps.apple.com/us/app/grubhub-food-delivery/id302920553). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Grubhub's public product UI and brand presence; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, restaurant list + live order tracking + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Pure white canvas** (`#FFFFFF`) light / warm near-black (`#121212`) dark — content-forward, low chrome
- **Wide 16:9 restaurant photography** in 14pt rounded containers — full-color, the anchor of every card
- **Two-tone brand** — Grubhub Red (`#F63440`) for actions/brand, Grubhub Orange (`#FF8000`) for deals & savings
- **Grubhub+ Perks** surfaced as a gold (`#FFB81C`) badge + "$0 delivery fee" messaging — membership only
- **Live order tracking** — persistent ETA banner + step timeline on a calm Track Blue (`#00A0DF`) accent
- **Green rating star** (`#18A957`) — Grubhub renders ratings in green, not gold
- **"Deliver to" address row** pinned top-left with a cart-count bag button top-right
- **Cuisine chip rail** — horizontal pill filters, active chip filled Grubhub Red `#F63440`
- **Pill-shaped primary buttons** (`999px`) — "Add to bag", "Checkout · $42.18", "Get Grubhub+"
- **Quantity stepper** (− n +) with Grubhub Red circular controls and tabular numerals
- **Numbers carry weight** — every price, fee, ETA and quantity is bold (700+) and tabular
- **Bottom tab bar** — Delivery / Search / Orders / Saved / Account, active icon tinted `#F63440`

## Brand Sources

- [Grubhub](https://www.grubhub.com/)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (recommended substitute for Graphik)
- [Graphik by Commercial Type](https://commercialtype.com/catalog/graphik) — Grubhub's proprietary brand face
- Public brand palette: Grubhub Red `#F63440`, Grubhub Orange `#FF8000`, Perks Gold `#FFB81C`, Track Blue `#00A0DF`
