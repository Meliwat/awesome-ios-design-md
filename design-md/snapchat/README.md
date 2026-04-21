# Snapchat iOS Inspired Design System

Design system docs inspired by the [Snapchat iOS app](https://apps.apple.com/us/app/snapchat/id447188370). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Snap Inc.'s public brand guidelines and Snap Kit developer documentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `preview.md` | Interactive token catalog on the Spectr gallery |

## Signature Moves

- **Camera-first home screen** — the app opens directly to the live viewfinder, full-screen, no feed
- **Snap Yellow** (`#FFFC00`) — the aggressive primary accent; ghost logo, capture button ring, streak flame
- **82pt capture button** — concentric double-circle (6pt yellow outer ring + 64pt white inner circle) as the signature CTA
- **Swipe-between-five-screens nav** — Map | Chat | Camera | Stories | Spotlight (no traditional tab bar)
- **Color-coded snap types** — Red photo, Purple video, Blue chat, Green audio in the inbox
- **Bitmoji everywhere** — user's custom cartoon avatar replaces profile photos throughout
- **Avenir Next** at Medium (500) and Bold (700) only — warm humanist geometric sans
- **Friend emoji system** — 🔥 streaks, 💛 best friends, 😎 mutual BFFs, 🎂 birthdays as the habit-loop signal
- **Chromeless camera HUD** — floating icons with drop shadows, no UI frame around the viewfinder

## Brand Sources

- [Snap Inc. Brand Guidelines](https://www.snap.com/en-US/brand-guidelines)
- [Snap Kit Developer Documentation](https://kit.snapchat.com/)
- Public palette: Snap Yellow `#FFFC00`, Photo Red `#FF2E3D`, Video Purple `#9B51FF`, Chat Blue `#4DA7FF`
