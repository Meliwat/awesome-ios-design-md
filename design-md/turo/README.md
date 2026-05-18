# Turo iOS Inspired Design System

Design system docs inspired by the [Turo iOS app](https://apps.apple.com/us/app/turo-car-rental-marketplace/id555063314). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Turo's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, photo hero + host card + Book bar |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, `HorizontalPager` hero + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **The car photo is the product** — full-bleed, edge-to-edge photo carousel, NO card frame on the listing hero
- **Turo Purple `#593BFB`** is the single booking-action color — "Continue", "Book", selected pin, active filter
- **Turo Teal `#5CE0B8`** is the trust/value accent — All-Star Host badge, savings, free cancellation, instant book, favorite-saved
- **Sticky price + Book bar** — pinned to the bottom of the listing always; the conversion anchor (`$83 / day` + est. total link)
- **Host-centric trust** — avatar, All-Star teal badge, trip count, response time, reviews on every listing
- **Photo-card search twinned with a map** — list of photo-led cards + map of purple price pins (Airbnb-style); selected pin inverts to white
- **Specs strip** — fuel / seats / transmission as compact teal-iconed tiles under the title
- **Near-black canvas `#0F0F12`** dark so photography pops; photos never tinted or dimmed, only chrome
- **Pill-style active page dot** — the carousel pagination affordance (active grows to an 18pt white pill)
- **Heavy tabular prices** (800 weight) — daily rate, est. total, savings never jitter
- **Bottom tab bar** — Search / Trips / Favorites / Inbox / More, Turo Purple active tint, NO pill indicator
- **Star gold `#FFB400`** is the rating star only — never a CTA; teal carries success and savings

## Brand Sources

- [Turo](https://turo.com/)
- [Turo Host (All-Star Host program)](https://turo.com/us/en/become-a-host)
- Turo brand sans (proprietary) — implementation fallback: [Manrope by Mikhail Sharanda](https://manropefont.com/) (SIL OFL)
- Public brand palette: Turo Purple `#593BFB`, Turo Teal `#5CE0B8`, star gold `#FFB400`
