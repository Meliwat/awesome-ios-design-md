# Deliveroo iOS Inspired Design System

Design system docs inspired by the [Deliveroo iOS app](https://apps.apple.com/gb/app/deliveroo-food-delivery/id1001501844). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Deliveroo's public product UI and brand presence; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, restaurant feed + menu + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Pure white canvas** (`#FFFFFF`) light / warm near-black (`#121212`) dark — clean, photo-forward
- **One brand color** — Deliveroo Teal (`#00CCBC`) for everything: logo, buttons, ratings, Plus, tab, basket
- **Teal Ink on-color** (`#003733`) — not white — on every teal fill; a Deliveroo signature
- **Kangaroo logomark** — the mascot, always in teal
- **Wide 5:3 restaurant photography** in 14pt rounded containers — full-color, the card anchor
- **Fee pill** — a `999px` chip with a teal moped icon + "£1.49 delivery · Min £12" on every card
- **Floating teal `+`** hanging off the corner of menu-item thumbnails — the core add gesture
- **Rounded-square category icons** (Restaurants / Grocery / Fast / Treats / Offers) with teal glyphs
- **Deliveroo Plus** as a teal banner + teal "PLUS" badge — same teal, no separate color
- **Heavy type** — section headers/names at weight 800, screen titles at 900, tight negative tracking
- **Pill buttons + pill search field** (`999px`); sticky basket bar embeds count + total
- **Promo gold** (`#FFC100`) reserved for percentage-off deals only

## Brand Sources

- [Deliveroo](https://deliveroo.co.uk/)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (recommended substitute for Deliveroo's custom face)
- Public brand palette: Deliveroo Teal / Aquamarine `#00CCBC`, Teal Ink `#003733`, Promo Gold `#FFC100`
