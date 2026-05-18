# Expedia iOS Inspired Design System

Design system docs inspired by the [Expedia iOS app](https://apps.apple.com/us/app/expedia-hotels-flights-car/id427916203). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Expedia's public product UI and the Expedia Group brand work; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, property card, mode switch, One Key strip |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, typography, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, navigation + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Expedia Yellow** (`#FFC94D`) is the savings/deal/loyalty highlight only — bundle tags, member prices, One Key chip, "Bundle & save" CTA; text on it is navy `#00355F`
- **Action Blue** (`#1668E3`) is the interactive color — "Reserve" CTA, links, selection; white text on it
- **Expedia Navy** (`#00355F`) is brand/trust — logomark field, review-score badge, dark headers
- **One Key gold** (`#F5C518`) is rewards-only — balances, "Earn X One Key cash" lines, the One Key chip
- **Search-and-results loop** — segmented Stays / Flights / Cars / Bundle switch → single search pill → vertical results list
- **Property/fare result card** — 16:10 hero photo, deal flag top-left, save heart top-right, title, location, review-score badge, price block, One Key earn line
- **Navy review-score badge** — "9.2 Wonderful · 1,847 reviews", color-banded (`#1A8B4B` Wonderful / `#1668E3` Excellent / `#14416B` Good)
- **Strikethrough → discounted price** pattern on nearly every card
- **Bundle + Save module** — package stay+flight+car for a discounted "Bundle" price, the core monetization gesture
- **Dark canvas is navy-biased** (`#0E1116`) — NOT pure black; cards lift to `#161B22`
- **Brief yellow price flash** when a price drops on a date change

## Brand Sources

- [Expedia](https://www.expedia.com/)
- [One Key rewards program](https://www.expedia.com/one-key)
- Expedia Sans (proprietary, by Pentagram for Expedia Group, 2017+ rebrand) — `Inter` (SIL OFL) is the closest free substitute
- Public brand palette: Expedia Yellow `#FFC94D`, Expedia Navy `#00355F`, Action Blue `#1668E3`, One Key gold `#F5C518`
