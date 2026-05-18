# The League iOS Inspired Design System

Design system docs inspired by the [The League: Date Intelligently iOS app](https://apps.apple.com/us/app/the-league-date-intelligently/id1163031681). Not the official system. Brand colors, font names, and component patterns are cross-referenced with The League's public product UI and marketing site; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, prospect card + concierge note + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Near-true-black canvas** (`#0A0A0A`) — a private club after dark; dark-native, no light mode in spirit
- **One rationed gold** (`#C8A35A`, bright `#DBBA71`, deep `#A8863F`) does ALL accent work — crest, hairlines, one CTA, credential marks
- **No drop shadows anywhere** — depth is created entirely by 1px gold-tinted hairline borders (`#2A2620`) + an embossed treatment
- **The prospect card is an embossed calling card** — full-color photo, serif name, short gold rule, credential stack — not a swipe-app photo
- **Serif/sans split encodes hierarchy** — Cormorant Garamond–class serif for names / wordmark / titles / concierge; Jost-class sans for credentials / UI / labels
- **The concierge is a recurring character** — italic serif notes behind a 2px gold left rule, personal introductions ("you both ran the NY Marathon and studied at HBS")
- **Scarcity made visual** — a daily "Today's Batch · 7 Prospects" banner; curated, finite, never infinite scroll
- **Credential stack** — title · company / school / neighborhood with small gold `◦` marks; a LinkedIn-meets-Vogue dossier replaces a bio
- **Sharp engraved geometry** — 2–6pt corner radii; no pills, no friendly rounding
- **Quiet letter-spaced uppercase** — eyebrows `+2px`, buttons `+1.6px`, tabs `+1px` — black-tie restraint
- **Gold fill rationed to one CTA** — the Heart card action is a gold *outline*, not a fill
- **Restrained motion** — fades and draws, a drawn champagne wax seal on a match, never a thrown swipe or confetti
- **Warm off-white text** (`#EDE9E2`) — never pure white; pure white reads too clinical

## Brand Sources

- [The League](https://www.theleague.com/)
- [The League: Date Intelligently on the App Store](https://apps.apple.com/us/app/the-league-date-intelligently/id1163031681)
- [Cormorant Garamond (Google Fonts)](https://fonts.google.com/specimen/Cormorant+Garamond) — SIL OFL (display-serif substitute for The League's editorial serif)
- [Jost (Google Fonts)](https://fonts.google.com/specimen/Jost) — SIL OFL (geometric-sans substitute for The League's Futura-class UI face)
- Public brand palette: near-true-black `#0A0A0A`, League Gold `#C8A35A`, gold hairline `#2A2620`, warm off-white text `#EDE9E2`
