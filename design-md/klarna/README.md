# Klarna iOS Inspired Design System

Design system docs inspired by the [Klarna iOS app](https://apps.apple.com/us/app/klarna-shop-now-pay-later/id1115120118). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Klarna's public brand and product surfaces; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, motion + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Soft bright canvas** (`#FFFFFF`) with a pink-tinted surface (`#FFF2F5`)
- **Klarna Pink** (`#FFB3C7`) as the single action color — always with near-black text, never white
- **Near-black ink** (`#0B051D`) for structure: headings, amounts, the dark hero
- **Pay-in-4 schedule timeline** — 4 dated dots on a track, the signature plan device
- **In-app shopping browser** — Klarna wraps any store with a persistent pink "Pay with Klarna" footer
- **Very soft radii** (28pt CTA, 20–24pt cards) and slow gliding transitions — "smooth"
- **Order list rows** — clean purchase history with plan status pills
- **Klarna Text** (proprietary humanist sans), weights 400 / 600 / 700

## Brand Sources

- [Apple App Store — **Klarna**: Shop now, pay later](https://apps.apple.com/us/app/klarna-shop-now-pay-later/id1115120118)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [Material 3 Design Guidelines](https://m3.material.io/) (for the Android port)
- Public brand palette: Klarna Pink `#FFB3C7`, near-black `#0B051D`, canvas `#FFFFFF`
