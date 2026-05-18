# Vero iOS Inspired Design System

Design system docs inspired by the [Vero iOS app](https://apps.apple.com/us/app/vero-true-social/id1028908869). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Vero's public product UI and brand presentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, brand gradient, 7 post-type selector, chronological feed |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, gradient wordmark, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, post-type composer flow |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Pure true-black canvas** (`#000000`) — OLED-deep, gallery-wall presentation; surfaces lift only to `#0E0E10` / `#161618`
- **Strictly chronological feed** — newest-first, never reordered, no algorithm, no trending — a design *promise*, not a setting
- **7 first-class post types** — Photo / Video / Link / Music / Film / Book / Place — each with its own icon, composer, and card; none is primary
- **Post-type chip on every post** — a small teal label that tells you *what kind* of content before you read it
- **One brand expression** — a teal→blue gradient (`#00D1C1 → #0079D3`) on the wordmark, Post CTA, compose ring, active states
- **Flat teal accent** (`#00C2B8`) — liked hearts, active type icons, links, focus rings
- **Camera-amber star ratings** (`#E8B23A`) — the *only* other saturated hue, for Book/Film posts (film-buff audience)
- **No card chrome** — posts sit directly on true-black, separated by hairline `#232325` dividers — content is the interface
- **Manrope typography, calm and unhurried** — 14–18pt author, 14pt captions with 1.5 line height
- **Full-pill everything** — buttons, search, chips use 999pt radius; no heavy shadows (invisible on black)

## Brand Sources

- [Vero — True Social](https://www.vero.co/)
- [Vero on the App Store](https://apps.apple.com/us/app/vero-true-social/id1028908869)
- [Manrope (Google Fonts)](https://fonts.google.com/specimen/Manrope) — SIL OFL (closest free face to Vero's calm geometric type)
- Public brand palette: teal→blue gradient `#00D1C1 → #0079D3`, flat teal `#00C2B8`, true-black `#000000`
