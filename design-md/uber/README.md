# Uber iOS Inspired Design System

Design system docs inspired by the [Uber iOS app](https://apps.apple.com/us/app/uber-request-a-ride/id368677368). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Uber's public [Base design system](https://base.uber.com) and verified screenshots; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, map integration, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, `react-native-maps`, `@gorhom/bottom-sheet`, Reanimated |
| `preview.html` | Interactive design token catalog (light) |
| `preview-dark.html` | Interactive design token catalog (dark) |

## Signature Moves

- **Map as hero** — every primary screen is 60-80% map, with UI layered in a bottom sheet
- **Pure black primary CTA** (`#000000`) — Uber never uses a brand-green "confirm" button
- **"Where to?" bottom sheet** — 16pt top corners, grabber, the signature rideshare entry point
- **Uber Move font family** — Display / Text / Mono cuts, with Mono used for every ETA, fare, and address
- **Stark monochrome chrome** — no gradients, no glossy surfaces, minimal shadow
- **Functional-only color** — green (`#05A357`) = driver arrived, red (`#D72113`) = surge/cancel, blue (`#0A47FF`) = info
- **Driver arrival beacon** — pulsing green rings on the driver's car icon on the map
- **License plate pill** — `#F6F6F6` background, Uber Move Mono 14pt, instantly scannable

## Brand Sources

- [Uber Base Design System](https://base.uber.com) — public design system
- [Uber Brand Guidelines](https://brand.uber.com) — public brand assets
- [Uber Design Blog](https://medium.com/uber-design) — typography and system evolution
- Verified palette: Black `#000000`, Green `#05A357`, Red `#D72113`, Blue `#0A47FF`, Amber `#FFCB00`
