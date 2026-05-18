# United iOS Inspired Design System

Design system docs inspired by the [United Airlines iOS app](https://apps.apple.com/us/app/united-airlines/id449945214). Not the official system. Brand colors, font names, and component patterns are cross-referenced with United Airlines' public product UI, brand guidelines, and the United app; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, boarding pass + interactive seat map, `ViewModifier`s |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, components, Reanimated + Haptics + Brightness |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, boarding pass + seat map composables, navigation + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Flight detail → boarding pass flow** — the detail-rich flight screen (left/center/right zones) leads, then the pass
- **Boarding pass** — a solid United Blue `#002244` header with the globe mark + gold `PREMIER · GROUP 1` band, a `Gate / Seat / Boards` grid, a perforated dashed divider with circular notch cut-outs, and a scannable barcode
- **Interactive seat map** — Economy Plus tinted translucent blue, taken seats recessed, selected seat glowing Rhapsody Blue `#1414FF`
- **Two-blue brand** — United Blue (`#002244`) for brand/surface; Rhapsody Blue (`#1414FF`) for action (button, selected seat, active tab) — never swapped
- **Premier gold** (`#C2A14D`) for MileagePlus elite status / priority bands only
- **Functional status palette** — on-time `#1E8E5A`, boarding `#1414FF`, delayed `#C98A1E`, canceled `#D8434F` — kept distinct from the brand blues, constant across modes
- **Big bold data** — airport IATA codes, gate, seat, times rendered at weight 800 for at-a-glance scanning
- **Crisp 4pt button radius** — United's low-radius signature
- **Deep-navy dark canvas** (`#0A1424`) — never neutral black, so the brand navy stays alive
- **Barcode inverts on dark** — a light bar pattern so gate scanners still read a dimmed screen
- **Apple Wallet pass** — the boarding pass is a first-class live `.pkpass` (gate-change push)
- **Refined functional motion** — aircraft glide, seat spring, status cross-fade — no decorative bounce

## Brand Sources

- [United Airlines](https://www.united.com/)
- [United Airlines iOS app](https://apps.apple.com/us/app/united-airlines/id449945214)
- [Open Sans (Google Fonts)](https://fonts.google.com/specimen/Open+Sans) — Apache 2.0 (free analog to United's licensed corporate face)
- Public brand palette: United Blue `#002244`, Rhapsody Blue `#1414FF`, Premier gold `#C2A14D`
