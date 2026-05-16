# Tripadvisor iOS Inspired Design System

Design system docs inspired by the [Tripadvisor iOS app](https://apps.apple.com/us/app/tripadvisor-plan-book-trips/id284876795). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Tripadvisor's public brand and the post-rebrand owl identity; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Bright white canvas** (`#FFFFFF`) — a clean gallery wall, never tinted
- **Tripadvisor Green** (`#34E0A1`) as the single accent — primary action, active tab, bubble rating
- **Five-circle bubble rating** — the brand's most iconic, unmistakable unit (bubbles, never stars)
- **Owl logomark** — green bubble-eyes on a black silhouette, never recolored
- **Traveler photography as the color story** — full-bleed 3:2 place-card photos
- **"Travelers' Choice" badge** — green pill on top-rated places
- **Category tile grid** — Hotels / Things to do / Restaurants / Flights fast entry
- **Black text on the green CTA** — intentional contrast on the bright mint

## Brand Sources

- [Apple App Store — Tripadvisor listing](https://apps.apple.com/us/app/tripadvisor-plan-book-trips/id284876795)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Tripadvisor Green `#34E0A1`, Owl Black `#000000`, Canvas `#FFFFFF`
