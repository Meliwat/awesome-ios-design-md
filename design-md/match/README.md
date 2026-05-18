# Match iOS Inspired Design System

Design system docs inspired by the [Match: Dating App iOS app](https://apps.apple.com/us/app/match-dating-app/id305939712). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Match's public product UI and marketing site; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, swipe card + dock + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Full-bleed profile photo** — the entire screen IS the photo; no card frame, no margins, no surrounding chrome
- **Bottom gradient scrim** (`transparent → rgba(20,20,20,0.95)`) carries name / age / job / distance / chips legibly over the photo
- **Floating circular action dock** — Rewind / Pass / Super Like / Like / Boost over the photo bottom
- **The Like button is the single loudest object** — solid Match Red `#E92434` (bright `#F0203E` on dark) circle, white heart, biggest size
- **One disciplined brand red** — `#E92434`, energized to `#F0203E` for the Like/active tab on dark; no second brand color
- **Functional accents only** — green `#2ECC71` is presence, blue `#1FB6FF` is Super Like / verified, gold `#D6A75B` is Rewind / Premium, purple `#B36BD8` is Boost
- **Name bold, age light** — name 26pt weight 700, age 22pt weight 500 right beside it
- **Frosted translucent interest chips** — white text on `rgba(255,255,255,0.16)`, hairline `rgba(255,255,255,0.28)` border, backdrop blur
- **"It's a Match!" full-screen celebration** — overlapping avatars on a maroon-to-ink `#2A1418 → #1A1A1A` gradient under the red wordmark
- **The blur is the paywall** — locked Likes-You tiles blurred, the unlocked one clear with a red heart badge
- **Story-style segmented progress bars** at the top of every profile card (photo carousel)
- **Warm near-black dark canvas** `#141414` — never pure black; photography never dims

## Brand Sources

- [Match](https://www.match.com/)
- [Match: Dating App on the App Store](https://apps.apple.com/us/app/match-dating-app/id305939712)
- [Poppins (Google Fonts)](https://fonts.google.com/specimen/Poppins) — SIL OFL (free substitute for Match's custom geometric face)
- Public brand palette: Match Red `#E92434` / energized `#F0203E`, Match Ink `#1A1A1A`, functional accents (`#2ECC71` presence, `#1FB6FF` Super Like, `#D6A75B` Premium)
