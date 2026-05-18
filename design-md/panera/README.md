# Panera iOS Inspired Design System

Design system docs inspired by the [Panera Bread iOS app](https://apps.apple.com/us/app/panera-bread/id722304806). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Panera's public product UI, marketing site, and the MyPanera / Unlimited Sip Club program; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, order/rewards spine + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Warm white canvas** (`#FFFFFF`) with cream cards (`#F4EFE1`) light / warm near-black (`#14140F`) dark — bakery-warm, never clinical
- **Single brand action color** — Panera Green `#4C8B2B`, brightening to `#6BBE45` on dark/emphasis, for CTAs, active tabs, rewards arc, reward tags
- **Food photography is the hero** — large rounded photo cards (14pt radius) do the selling
- **MyPanera rewards card** — green gradient `#4C8B2B → #6BBE45` banner with progress bar + Unlimited Sip Club status near top of Home
- **Gold star** `#F2B705` reserved for reward / Sip Club / rating moments only
- **Order spine** — menu → item detail (hero photo + stepper + customization) → cart (pickup/delivery) → green pill checkout
- **Fully-rounded pills** (999pt) for buttons, toggles, search, reward chips, steppers — Panera's UI is soft
- **Calories shown next to prices** — a Panera signature on every item
- **Quantity stepper + customization rows** as the core ordering atoms
- **Warm dark text** `#2A2A1F` — NOT pure black; warm-tinted soft shadows, never harsh
- **Pickup / Delivery toggle** — segmented green pill at the top of the cart
- **5-tab bottom bar** — Home / Order / Rewards / Favorites / Account, active in brand green, no Material pill

## Brand Sources

- [Panera Bread](https://www.panerabread.com/)
- [MyPanera Rewards](https://www.panerabread.com/en-us/mypanera/mypanera-rewards.html) — loyalty program
- [Unlimited Sip Club](https://www.panerabread.com/en-us/mypanera/unlimited-sip-club.html) — beverage subscription
- [Poppins (Google Fonts)](https://fonts.google.com/specimen/Poppins) — SIL OFL, closest free analog to Panera's warm display face
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL, body / UI face
- Public brand palette: Panera Green `#4C8B2B` / `#6BBE45`, warm cream `#F4EFE1`, gold star `#F2B705`
