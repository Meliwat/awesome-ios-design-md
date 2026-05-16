# Waze iOS Inspired Design System

Design system docs inspired by the [Waze iOS app](https://apps.apple.com/us/app/waze-navigation-live-traffic/id323229106). Not the official system. Brand colors, typography (Boing — proprietary rounded sans, with SF Pro Rounded as the iOS fallback), and component patterns are cross-referenced with Waze's [public brand guidelines](https://www.waze.com/brands) and observation of the iOS app. Exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, components, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, cyan arrow puck + speech-bubble hazard reports + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Waze Purple** (`#7E55BE`) + **Waze Cyan** (`#33CCFF`) — the electric cool-tone brand duo no other map app uses; both stay at full saturation across light and dark modes
- **Cartoon speech-bubble hazard reports** — chunky rounded rectangles with triangular tails: Police Red `#EF6A65`, Traffic Orange `#F69833`, Closure Yellow `#F9C42E`, Cleared Green `#75C73E`
- **The cyan arrow puck** — a chunky 32×36pt arrow shape (NOT a circle), white-outlined, rotating with heading; outer pulse at 0.15 opacity expands to 1.5x over 2 seconds
- **Loud saturated cartography** — bright cyan water `#9BDEEF`, mint parks `#C5E89B`, warm white roads `#FFFCF2` — louder than Apple Maps or Google Maps by design
- **The Big Purple "Go" button** — full-pill 56pt height, `#7E55BE` with a tinted `rgba(126,85,190,0.30)` shadow
- **The Report FAB** — bottom-right Waze Purple circle with a TINTED PURPLE shadow `rgba(126,85,190,0.40)` (signature)
- **Speed-limit + current-speed tile** — US-style speed-limit signage rendered as a bottom-left tile with white circle + 35 inside, current speed below, flips red when speeding
- **Boing typography (SF Pro Rounded fallback)** — roundness is the brand; Boing Mono on every distance, time, ETA, and speed
- **Cartoon report wheel** — 280pt cartoon dial with 8 wedges for hazard type selection — playful by design, never toned down
- **Polyline ant trail** — chevron pattern animating along the route polyline at 1 chevron per 2 seconds — the "moving" visual indicator
- **Wazer mood avatars** — the user's chosen emoji-style character floats next to the location puck, scales 0 → 1.2 → 1.0 on selection

## Brand Sources

- [Waze Brand Guidelines](https://www.waze.com/brands)
- [Waze Carpool Brand Resources](https://blog.waze.com/)
- [Waze for Cities](https://www.waze.com/wazeforcities/) — cartography reference
- [Google Brand Resource Center — Waze](https://about.google/brand-resource-center/)
- Public observation of the iOS Waze app (versions 4.x and later)
- Public brand palette: Waze Purple `#7E55BE`, Cyan `#33CCFF`, Police Red `#EF6A65`, Traffic Orange `#F69833`, Closure Yellow `#F9C42E`, Cleared Green `#75C73E`
