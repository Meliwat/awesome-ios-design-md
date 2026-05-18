# Kindle iOS Inspired Design System

Design system docs inspired by the [Amazon Kindle iOS app](https://apps.apple.com/us/app/amazon-kindle/id302584613). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Kindle's public product UI and Amazon's brand system; exact private tokens may differ from production. Bookerly and Amazon Ember are Amazon-licensed faces — these docs name free analogs (Bitter, system sans) for non-Amazon builds.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, reading-theme model, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, reading-theme model, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, reader + Aa panel + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **The reading page is the product** — all chrome fades out 250ms on book open; a center-tap restores it
- **Sepia default reading theme** — paper `#FBF0D9` / warm ink `#5F4B32`; paper, not screen
- **Five reading themes** — White / Sepia / Green / Dark `#2A2A2A` / Black `#000000`, user-chosen, OS-independent
- **Reading serif, justified + hyphenated** — Bookerly (Bitter analog) ~15.5pt, 1.72 line-height
- **Amazon Orange is the only accent** (`#FF9900`) — CTA, progress fill, active-theme ring, selected tab
- **Library cover grid with progress** — every cover wears a thin orange progress bar on its bottom edge
- **The Aa typography panel** — font / size / spacing / margins + five theme swatches, active ringed orange
- **Cover art is the only saturation** — chrome is neutral Amazon Ember on near-black `#1A1A1A`
- **The reading surface is flat** — no cards, borders, or shadows; only covers and sheets are elevated
- **Black-theme ink is dimmed** `#C8C8C8` — never pure white, to reduce halation
- **Two surface contexts** — the reader obeys the user's theme; app chrome follows system dark
- **Quiet, fast motion** — page turn + fade only; no decorative animation on the page

## Brand Sources

- [Amazon Kindle](https://www.amazon.com/kindle)
- Bookerly (Dalton Maag for Amazon) — Amazon-licensed; free analog: [Bitter (Google Fonts)](https://fonts.google.com/specimen/Bitter) — SIL OFL
- Amazon Ember (Dalton Maag for Amazon) — Amazon-licensed; non-Amazon builds fall back to the system sans
- Public brand palette: Amazon Orange `#FF9900`, Kindle black `#1A1A1A`, Sepia page `#FBF0D9` / ink `#5F4B32`, the five reading themes
