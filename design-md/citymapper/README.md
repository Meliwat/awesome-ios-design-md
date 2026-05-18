# Citymapper iOS Inspired Design System

Design system docs inspired by the [Citymapper iOS app](https://apps.apple.com/us/app/citymapper-all-live-transit/id469463298). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Citymapper's public product UI and brand material; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, mode-color system, leg strip, GO button, departures board |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, leg strip + GO button + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Mode-color system** — walk `#00B894`, bus `#E8453C`, tube/metro `#2B5BFF`, rail `#8E44D8`, bike `#00A8C5`, cab `#FFB400` — theme-invariant, the brand's soul
- **Leg strip** — a route shown as a horizontal row of color-coded mode chips with `›` arrows; color first, words second
- **GO button** — fat green (`#00C281`) rounded pill with a play triangle; shape- and color-locked on every screen and theme
- **GO trip mode** — live step-by-step navigation with big countdown type and a mode-colored progress line
- **Origin→destination card** — gray "from" dot + blue "to" dot joined by a transit-map line segment
- **Route options ranked & tagged** — Fastest / Cheapest / Easiest / Rain-safe pills
- **Departures board** — live next-departure list; mode-colored line badge + destination + minutes (amber `#FF8A00` when ≤3 min)
- **Disruption banners** — inline orange (`#FF8A00`) alerts in route results; severe closures in `#E8453C`
- **Citymapper Blue accent** (`#2B5BFF`) — brand color & metro mode; green is reserved exclusively for GO
- **Calm canvas** — white light / deep blue-black (`#0C0E14`) dark so mode colors + the live map pop
- **Chunky confident type** — Inter / custom grotesque, extra-bold ETAs (800–900), readable while moving
- **Bottom-sheet-over-map** metaphor — the route list floats above a live map and drags up/down

## Brand Sources

- [Citymapper](https://citymapper.com/)
- [Citymapper press & brand](https://citymapper.com/news) — electric blue `#2B5BFF`, GO green `#00C281`
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (closest free substitute for the chunky grotesque)
- Public mode-color convention: walk `#00B894`, bus `#E8453C`, tube `#2B5BFF`, rail `#8E44D8`, bike `#00A8C5`, cab `#FFB400`
