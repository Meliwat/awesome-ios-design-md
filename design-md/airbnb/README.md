# Airbnb iOS Inspired Design System

Design system docs inspired by the [Airbnb iOS app](https://apps.apple.com/us/app/airbnb/id401626263). Not the official system. Brand colors, typography (Airbnb Cereal), and component patterns are cross-referenced with Airbnb's public design writing and the DLS (Design Language System) publicly presented by the Airbnb Design team; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, components, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **White canvas** with photography as the hero — content-first, editorial
- **Primary Coral** (`#FF385C`) as the action color — Reserve CTA, save-heart fill, category underline, active tab
- **Rausch** (`#FF5A5F`) as the heritage red — lives on the animated Belo logomark and illustration moments
- **Stay cards** — 4:3 photos with 16pt corner radius, save heart top-right, rating row beneath, full-width stacking (never 2-col)
- **"Where to?" search pill** — full-pill white with subtle shadow, Location / Check in / Check out / Who segmentation
- **Horizontal category bar** — icon + label chips with a sliding underline on the selected category
- **Sticky Reserve footer** — translucent blur at the bottom of every stay detail, total price + date range + Primary Coral pill
- **Airbnb Cereal** — proprietary warm geometric sans by Dalton Maag (2018); weights 400/500/700/800

## Brand Sources

- [Airbnb Design — Redesigning Our DLS](https://airbnb.design/building-a-visual-language/)
- [Dalton Maag — Airbnb Cereal case study](https://www.daltonmaag.com/work/airbnb)
- [Airbnb 2020 rebrand and the Bélo](https://airbnb.design/)
- Public brand palette: Primary Coral `#FF385C` (post-2020), Rausch `#FF5A5F` (heritage), Babu `#00A699`, Arches `#FC642D`, Hof `#484848`, Foggy `#767676`
