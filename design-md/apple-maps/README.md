# Apple Maps iOS Inspired Design System

Design system docs inspired by the [Apple Maps iOS app](https://apps.apple.com/us/app/maps/id915056765) — a first-party Apple app that ships with iOS. Not the official system; brand colors, typography (SF Pro), cartography (cream parchment), and component patterns are cross-referenced with Apple's [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/maps), MapKit documentation, and public observation of the app. Exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, components, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, sliding bottom-sheet detents + pulsing location puck + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Cream parchment cartography** (`#F6F1E6` land, `#B9D9EB` water, `#D6E5C9` parks) — the map IS the brand
- **Maps Blue** (`#0A84FF`) for directions polyline, current-location puck, primary CTAs — the iOS system blue, slightly boosted
- **Maps Red** (`#FF3B30`) for the dropped pin — universal regardless of category, just the iOS system red
- **Sliding bottom search card** — three detents (88pt / ~50% / full-screen), `.regularMaterial` blur background, 10pt top corner radius
- **The pulsing current-location puck** — 22pt blue dot with a 3pt white ring, plus a 30pt outer ring pulsing 1.0 → 1.6 over 2 seconds infinite
- **Directional cone** extending 60pt from the puck when heading is known — `rgba(10,132,255,0.4)` to transparent gradient
- **Floating top-right map controls** — three circular `.regularMaterial` blur buttons stacked vertically with 8pt gaps (layer picker, location, 3D toggle)
- **Category color system** — Food `#FF9500`, Coffee `#A5694C`, Drinks `#5AC8FA`, Shopping `#FFCC00`, Parking `#AF52DE`
- **SF Pro at the system stack** — Display, Text, Compact — with tabular figures on every distance, time, and ETA
- **Real transit agency colors** on transit line badges — BART orange, MTA green, NYC subway color per line
- **Blur as the depth system**, not shadow — `.regularMaterial` over the map is the dominant elevation treatment

## Brand Sources

- [Apple Human Interface Guidelines — Maps](https://developer.apple.com/design/human-interface-guidelines/maps)
- [Apple Developer — MapKit](https://developer.apple.com/documentation/mapkit)
- [Apple Maps gallery (apple.com/maps)](https://www.apple.com/maps/)
- [SF Symbols app](https://developer.apple.com/sf-symbols/)
- Public observation of the iOS 17/18 Maps app
- Public iOS system colors: Maps Blue `#0A84FF`, Red `#FF3B30`, Orange `#FF9500`, Green `#34C759`, Yellow `#FFCC00`, Purple `#AF52DE`, Pink `#FF2D55`, Indigo `#5856D6`
