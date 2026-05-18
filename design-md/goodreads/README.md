# Goodreads iOS Inspired Design System

Design system docs inspired by the [Goodreads iOS app](https://apps.apple.com/us/app/goodreads-book-reviews/id355833469). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Goodreads' public product UI and brand presentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, book detail + star rating + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Warm tan paper canvas** (`#F4F1EA`) light / ink-brown (`#161310`) dark — a library, never clinical
- **Literary brown** (`#382110`) for shelf headers, the logomark, and primary brand chrome
- **Book covers are the hero** — full-color jacket art, 3pt corners, a warm-tinted drop shadow, never UI-tinted
- **Amber five-star rating** (`#E9A100`) — the single load-bearing accent, held identical light and dark
- **"Want to Read" green CTA** (`#409D69`) — the dominant action on every book detail
- **Serif reads, sans operates** — Merriweather for titles/synopses/reviews, Lato for buttons/labels/tabs
- **1.6 reading rhythm** on body and review prose — reviews are long and must stay comfortable
- **Teal author/link color** (`#00635D`, dark `#6FB3AD`) — quiet, never blue
- **Shelf model** — Read / Currently Reading / Want to Read + unlimited custom shelves
- **Community reviews feed** — avatar + name + their amber stars + serif prose, infinite scroll
- **Brown → tan-gold on dark** (`#C9883D`) for active tabs against the dark canvas
- **Restraint is the brand** — brown, tan, teal, green, and one amber; no rainbow

## Brand Sources

- [Goodreads](https://www.goodreads.com/)
- [Merriweather by Sorkin Type (Google Fonts)](https://fonts.google.com/specimen/Merriweather) — SIL OFL
- [Lato by Łukasz Dziedzic (Google Fonts)](https://fonts.google.com/specimen/Lato) — SIL OFL
- Public brand palette: Goodreads brown `#382110`, tan paper `#F4F1EA`, rating amber `#E9A100`, link teal `#00635D`
