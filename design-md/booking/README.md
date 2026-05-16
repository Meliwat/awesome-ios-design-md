# Booking.com iOS Inspired Design System

Design system docs inspired by the [Booking.com iOS app](https://apps.apple.com/us/app/booking-com-hotels-travel/id367003839). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Booking.com's public brand presentation and observed product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Dense white marketplace** (`#FFFFFF`) — conversion-first card feed, packed but scannable
- **Booking Blue** (`#003580`) for brand/structure — app bar, Genius banner, the score badge
- **CTA Blue** (`#0071C2`) for action — Search, Reserve, links, selected filters
- **Review-score badge** — a solid-navy rounded chip with a one-decimal score + word ("8.9 Fabulous")
- **Search form card** — destination + dates + guests + Search button in one elevated card
- **Genius loyalty banner** — a full-navy strip surfacing member discounts
- **Map toggle + price pins** — flip the list into a map of price bubbles
- **Border-defined cards** — feed cards use a 1pt `#E0E0E0` border, not heavy shadows

## Brand Sources

- [Apple App Store — Booking.com](https://apps.apple.com/us/app/booking-com-hotels-travel/id367003839)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Booking Blue `#003580`, CTA Blue `#0071C2`, Brand Yellow `#FEBB02`
