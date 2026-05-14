# Bumble iOS Inspired Design System

Design system docs inspired by the [Bumble iOS app](https://apps.apple.com/us/app/bumble-dating-app-meet-date/id930441707). Not the official system. Brand colors, typography (Brando), and component patterns are cross-referenced with Bumble's public brand site, marketing materials, and the in-app surface; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, components, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, hexagon helper |
| `README.md` | This file — overview, signatures, sources |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Bumble Yellow** (`#FFC629`) — the dominant brand color, used on splash, primary CTAs, tab indicator, match notification, and the iconic heart action button
- **Pure black on yellow** — `#000000` text on Bumble Yellow surfaces, because WCAG AA requires it; warm Bumble Black (`#1F1F1F`) only on white surfaces
- **Hexagon iconography** — match notification photos hex-cropped, app icon is a hex with a bee, mode toggle buttons are literal hex shapes; the hex is the brand's spatial signature
- **Big yellow heart with glow shadow** — 64pt circle, `#FFC629` fill, black heart glyph, `rgba(255, 198, 41, 0.5) 0 6px 16px` halo; the most-pressed button in the app
- **Swipe card with photo progress bar** — full-bleed cover photo with a 4-segment Stories-style progress bar at the top, dark gradient overlay at the bottom carrying name + verified check + bio
- **24-hour countdown chip** — `#FFC629` capsule with pure black tabular text, sits on every new matched-but-unmessaged chat; expires to error red — the core Bumble mechanic visualized
- **It's a Match! celebration** — full-screen Bumble Yellow canvas with two hex-cropped photos side-by-side and a Match Pink (`#E94B7B`) heart between, scale-bouncing in
- **Brando weights 500/700/900** — the brand has no Light or Thin; Brando Black at 44pt headlines and Bold (700) for card names is the confidence
- **Mode-switching chrome** — Date/BFF/Bizz replaces all yellow with `#11AAA8` teal or `#FF8000` orange, but the hexagon shape stays constant

## Brand Sources

- [Bumble — Make the First Move](https://bumble.com/)
- [Bumble Brand Press](https://bumble.com/press)
- [Mike Abbink — Brando type system](https://www.abbink.com/) — the proprietary slab-influenced sans commissioned for Bumble
- Public brand palette: Bumble Yellow `#FFC629`, Honey Deep `#F5B616`, Match Pink `#E94B7B` (match heart only), BFF Teal `#11AAA8`, Bizz Orange `#FF8000`
