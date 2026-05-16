# Nike Run Club iOS Inspired Design System

Design system docs inspired by the [Nike Run Club iOS app](https://apps.apple.com/us/app/nike-run-club-running-coach/id387771637). Not the official system. Brand colors (Nike Volt, Nike Red), Trade Gothic Condensed typography, and the run-tracking progress ring are cross-referenced with Nike's public brand identity work, the Wieden+Kennedy creative direction, and visual inspection of the shipping app; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, Volt progress ring, run tracking screen, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated progress ring + medal reveal |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, 280dp Volt progress ring + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **True black canvas** (`#000000`) on every screen — Nike's stadium void; dark-first by brand
- **Nike Volt** (`#CCFF00`) — the radioactive yellow-green accent; progress rings, primary CTAs (when not white), achievements
- **Trade Gothic Next LT Heavy Condensed** — the screaming display face; UPPERCASE on every hero number and every button label
- **Run tracking progress ring** — 280pt diameter, 6pt rounded stroke, Volt fill, `rgba(255,255,255,0.1)` track
- **88pt elapsed time inside the ring** — the visual identity of NRC, Trade Gothic Heavy tabular
- **3-up stat stack** — DISTANCE / PACE / HR in massive 64pt Trade Gothic with 13pt UPPERCASE 1.0pt-tracked labels
- **White pill primary CTA** — `#FFFFFF` background, `#000000` Trade Gothic label, 56pt height, 32pt corner radius; heavy haptic
- **Hexagonal achievement medals** — Volt or Volt→Red gradient fill, milestone number inside in 56pt Trade Gothic
- **Coach Bennett voice branding** — Coach's photo + name + quote on every guided run plan card
- **Heart-rate zone bar** — 5-segment horizontal stacked bar (blue → green → yellow → orange → red) on workout results
- **Heavy haptics on commitment** — "START RUN" and "PAUSE" use `.impactOccurred(.heavy)`, the heaviest in NRC

## Brand Sources

- [Nike Brand Guidelines](https://www.nike.com/help) (public marketing)
- [Wieden+Kennedy — Nike Creative](https://www.wk.com/clients/nike) — Trade Gothic Condensed type voice
- [Linotype — Trade Gothic Next LT](https://www.linotype.com/1257886/trade-gothic-next-family.html) — typography license
- Public brand palette: Nike Volt `#CCFF00`, Nike Red `#FA5400`, Brake Red `#E50916`, True Black canvas `#000000`
