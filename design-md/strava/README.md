# Strava iOS Inspired Design System

Design system docs inspired by the [Strava iOS app](https://apps.apple.com/us/app/strava-run-ride-hike/id426826309). Not the official system. Brand colors (Strava Orange), the route polyline visual treatment, and the 3-up stat grid pattern are cross-referenced with Strava's public brand identity guidelines, the Strava engineering blog, and visual inspection of the shipping app; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, feed card, route polyline, kudos confetti |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated kudos animation |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Strava Orange** (`#FC4C02`) — the only accent color in the entire app; never a secondary brand color
- **Route polyline** — 4pt stroke in Strava Orange with a `rgba(252,76,2,0.3)` halo so it reads on busy maps
- **Activity feed card** — map snapshot on top, 3-up stat grid (DISTANCE / TIME / PACE) below, kudos + comments row at the bottom; flat, no shadow
- **Tabular numerals everywhere** — pace columns, splits, distance, elapsed time, kudos counts; SF Pro `monospacedDigit()`
- **11pt UPPERCASE Semibold stat labels** with 0.6pt tracking — the Strava data voice
- **Hero stats on activity detail** — SF Pro Display 44pt Black weight — the trophy numbers
- **Center Record button** — 56pt circular Orange, protrudes 8pt above the tab bar, glowing Orange shadow; heavy haptic on tap
- **Kudos confetti** — scale-bounce + 8 orange particles fading up + medium haptic
- **Coal-black dark mode** (`#0F0F0F`) — not pure black, so the polyline glows
- **Achievement gold** (`#F5C24A`) — PR flag color, slightly cooler than system yellow

## Brand Sources

- [Strava Brand Identity Guidelines](https://www.strava.com/brand) (public)
- [Strava Engineering Blog](https://medium.com/strava-engineering) — design notes on the activity feed
- [Strava Press Kit](https://blog.strava.com/press) — brand color references
- Public brand palette: Strava Orange `#FC4C02`, dark canvas Coal `#0F0F0F`, warm map tile `#E8E5DD`, PR Gold `#F5C24A`
