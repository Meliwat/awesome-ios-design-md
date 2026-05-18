# Feeld iOS Inspired Design System

Design system docs inspired by the [Feeld iOS app](https://apps.apple.com/us/app/feeld-dating-app/id1455416286). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Feeld's public product UI and brand identity; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, swipe deck + Desire chips + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **True-black canvas** (`#000000`) — Feeld is dark-native; there is no light mode in the shipping app
- **One rationed accent** — acid-yellow `#E8FF63` lands on exactly one primary action per screen (Like, active tab dot)
- **Desire pink** `#FF5C8A` (emotional/secondary) + **lilac** `#C9B8FF` (Wink/super-interest)
- **Discover stack** — one full-bleed profile card, 28pt corners, heavy bottom gradient `rgba(0,0,0,0.72)→0.92`
- **Couple/group-first** — cards render paired names ("Robin & Sky"), paired ages, shared desires; never single-only
- **Three-button action row** — outline Pass · 64pt acid Like (the only acid fill + the only glow) · lilac Wink
- **Desire chip system** — togglable pills with three states: selected (acid `#E8FF63`/black), unselected (`#1C1C1E`/hairline), emotional (`#FF5C8A` 16%)
- **"Connections", not "Matches"** — vocabulary is part of the brand; list, empty state, chat header all say it
- **Two-font system** — Space Grotesk for personality (display/name/section/button), Inter for legibility (body/meta/snippet)
- **Fully rounded** — 28pt cards, 500pt pill buttons, circular avatars and action buttons
- **No tab tint pill** — active tab is the acid icon + a 5pt acid dot only
- **Verified glass pill** — frosted `rgba(0,0,0,0.5)` blur pill with acid checkmark; verification is a trust pillar

## Brand Sources

- [Feeld](https://feeld.co/)
- [Space Grotesk by Florian Karsten](https://fonts.google.com/specimen/Space+Grotesk) — SIL OFL
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- Public brand palette: acid-yellow `#E8FF63`, desire pink `#FF5C8A`, lilac `#C9B8FF` on true-black `#000000`
