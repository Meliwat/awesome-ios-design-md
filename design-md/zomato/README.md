# Zomato iOS Inspired Design System

Design system docs inspired by the [Zomato iOS app](https://apps.apple.com/in/app/zomato-food-delivery-dining/id434613896). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Zomato's public product UI and brand presence; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, semantic rating pill, veg mark, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, rating pill + veg mark + bordered ADD + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Pure white canvas** (`#FFFFFF`) light / warm near-black (`#121212`) dark — dense, information-rich
- **Zomato Red** (`#E23744`) for brand, primary action, "ADD", active tab and key CTAs (pressed `#C42531`)
- **Semantic rating pill** — green `#267E3E` great / amber `#DB7C38` average / red `#E23744` poor; the color always means the same thing
- **Veg / non-veg mark** — bordered square: green dot `#267E3E` = veg, maroon triangle `#B91C1C` = non-veg
- **Bordered "ADD" button** — surface + 1pt Zomato-Red border + red text, overlapping the dish thumbnail, morphs into a bordered red stepper
- **Delivery / Dining Out segmented toggle** — re-themes the whole restaurant detail screen
- **Hero photo + pull-up info card** — full-bleed photo with a 22pt-radius card sliding up over it and two side-by-side rating pills
- **Amber "★ Bestseller" tags** on popular dishes
- **Dense, heavy grotesque type** — restaurant names 800, screen titles 900, small muted metadata
- **Zomato Pro / Gold** signaled with a gold `#EFC75E` accent; Dining reservations use blue `#256FEF`
- **Bottom tab bar** — Delivery / Dining / Live / Profile, active icon tinted `#E23744`

## Brand Sources

- [Zomato](https://www.zomato.com/)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (recommended substitute for Zomato's Okra-family face)
- Public brand palette: Zomato Red `#E23744`, semantic rating scale `#267E3E` / `#DB7C38` / `#E23744`, Pro Gold `#EFC75E`
