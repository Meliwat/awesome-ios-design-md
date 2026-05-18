# Nubank iOS Inspired Design System

Design system docs inspired by the [Nubank iOS app](https://apps.apple.com/br/app/nubank/id814456780). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Nubank's public product UI, brand site, and design posts; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, purple hero + tiles + quick actions + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Nu Purple** (`#820AD1`) as the identity — a full-bleed purple hero header is the first thing you see, identical in light and dark
- **Tile-based Home** — NuConta balance, quick actions, credit-card invoice, activity, each a clean rounded card
- **"Fewer, clearer choices"** — common actions surfaced large; everything else one tap deeper
- **The Roxinho** — the little purple credit card rendered as a clean `#820AD1 → #9B2BE0` gradient
- **Big confident currency numbers** — balance and invoice are the headline; tabular figures
- **Calm content canvas** — lilac-white `#F4F2F7` light / deep aubergine `#15101C` dark beneath the bold purple block
- **Pix-first** — Pix is the primary quick action with an animated green-check success receipt
- **Pill buttons** — fully rounded (500pt radius) primary and secondary actions
- **Positive green, spend in primary text** — `#1FB76A` light / `#2ED47A` dark
- **Friendly, jargon-free copy** — Portuguese-first ("Saldo disponível", "Sua fatura fecha em 5 dias")
- **No tint pill on the active tab** — active is purple icon + label (Nu Purple light / Purple Soft dark)
- **Deep-aubergine dark mode** (`#15101C`) — tinted from Nu Purple, NOT pure black

## Brand Sources

- [Nubank](https://nubank.com.br/)
- [Building Nubank's design system](https://building.nubank.com.br/)
- Inter (SIL OFL) used as the free substitute for Nubank's Graphik / Nu Sans
- Public brand palette: Nu Purple `#820AD1`, positive green `#1FB76A`
