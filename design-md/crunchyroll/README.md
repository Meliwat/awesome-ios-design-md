# Crunchyroll iOS Inspired Design System

Design system docs inspired by the [Crunchyroll iOS app](https://apps.apple.com/us/app/crunchyroll/id329913454). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Crunchyroll's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, resume-aware episode list + segmented control + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **True-black OLED canvas** (`#000000`) — dark-only; no light mode so key-art bleeds to a perfect black floor
- **Full-bleed key-art hero** (~60% of screen) with a bottom-to-black scrim `transparent → rgba(0,0,0,0.55) → #000`
- **Single accent** — Crunchyroll Orange (`#F47521`) for the CTA, active tab, segmented underline, and progress bar; nothing else
- **Premium Gold** (`#FFC107`, text `#1A1304`) reserved only for the Premium badge and premium-lock glyph
- **Resume-aware episode list** — every episode row carries a 3pt orange progress bar at its watched fraction
- **Simulcast badge** (green `#2BB673`) — Crunchyroll's "airing now in Japan" signature affordance
- **Sub | Dub control** — active segment `#F47521`, inactive a 1pt `rgba(255,255,255,0.5)` outline
- **Lato Black (900)** hero titles, screen titles, button labels, and badges — the heaviness is the brand
- **Sliding orange underline** on the segmented control (Episodes / Details / More Like This), 220ms ease-out
- **Five-tab bottom bar** — Home / Browse / Watchlist / Manga / Profile, active in `#F47521`, no tint pill
- **"Lights down" transition** — detail fades to black over 250ms before the player appears
- **New Episode badge** (`#2A9DF4`) — the only other status color, used sparingly on poster cards

## Brand Sources

- [Crunchyroll](https://www.crunchyroll.com/)
- [Crunchyroll Brand](https://www.crunchyroll.com/about) — Crunchyroll Orange `#F47521`
- [Lato by Łukasz Dziedzic](https://www.latofonts.com/) — SIL OFL
- Public brand palette: Crunchyroll Orange `#F47521`, Premium Gold `#FFC107`, Simulcast Green `#2BB673`, true-black `#000000` OLED canvas
