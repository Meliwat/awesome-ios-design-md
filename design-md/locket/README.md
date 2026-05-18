# Locket iOS Inspired Design System

Design system docs inspired by the [Locket Widget iOS app](https://apps.apple.com/us/app/locket-widget/id1600999940). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Locket's public product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, capture button, square viewfinder, WidgetKit |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, capture button, Reanimated + Haptics, widget note |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, Glance home-screen widget |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Widget-first** — the real product surface is the Home Screen widget; the app is the camera + history behind it
- **Warm cream-gold world** — cream canvas (`#FFF7EC`), peach gradient (`#FFE9CC → #FFD9A8`); light-UI by design, no dark mode in spirit
- **Square full-bleed friend photo** in a very-rounded viewfinder (40pt corners) — like a locket, never rectangular
- **Big white circular capture button** — 84pt white disc with a gold (`#FFB02E`) inner ring; instant send to your tiny circle
- **Sender chip + caption pill** on warm frosted glass (`rgba(44,32,20,…)`) over the photo — never gray glass
- **Friends photo-history grid** — 4-up square thumbnails with avatar dots; scroll back like a photo album
- **Single warm brand accent** — Locket Gold (`#FFB02E`), deep amber (`#F08A1D`) on cream, warm coral (`#FF7A59`) secondary
- **Honey-tinted shadows** (`rgba(180,120,30,…)`) — never neutral gray; depth feels cozy and analog
- **Warm brown text** (`#2C2014`) — never pure black; Poppins type at warm 600–800 weights
- **Tiny-circle intimacy** — close-friends-only, no public profiles, no followers, no infinite feed

## Brand Sources

- [Locket Widget](https://apps.apple.com/us/app/locket-widget/id1600999940)
- [Poppins (Google Fonts)](https://fonts.google.com/specimen/Poppins) — SIL OFL (closest free face to Locket's warm rounded type)
- Public brand palette: Locket Gold `#FFB02E`, warm cream `#FFF7EC`, peach gradient `#FFE9CC → #FFD9A8`
