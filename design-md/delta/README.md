# Delta iOS Inspired Design System

Design system docs inspired by the [Fly Delta iOS app](https://apps.apple.com/us/app/fly-delta/id388491656). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Delta Air Lines' public product UI, brand guidelines, and the Fly Delta app; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, boarding pass + flight-status timeline, `ViewModifier`s |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, components, Reanimated + Haptics + Brightness |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, boarding pass + timeline composables, navigation + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Digital boarding pass** — the hero surface: a `#003268 → #C8102E` navy→red top strip, uppercase passenger name, a 3-cell `Gate / Seat / Group` grid, a SKY PRIORITY zone band, and a scannable barcode
- **Flight-status timeline** — a vertical dot rail (Departed → In Air → Arrive) with on-time green dots `#1E8E5A`, times and gates
- **Delta Blue** (`#003268`) as the brand / navigation anchor
- **Widget red** (`#C8102E`) as the single primary-CTA accent (and the canceled/error color) — never decorative
- **SkyMiles gold** (`#C99700`) for Medallion / loyalty emphasis only
- **Status-first Today tab** — the imminent flight is always the hero; a navy `#003268 → #06203F` gradient header with big IATA codes
- **Functional status palette** — on-time `#1E8E5A`, delayed `#C99700`, boarding `#0F4C9A`, canceled `#C8102E` — kept distinct from brand color and constant across modes
- **Big bold data** — airport IATA codes, gate, seat, times rendered at weight 800 for at-a-glance scanning
- **Navy-charcoal dark canvas** (`#0B1320`) — never neutral black, so the brand navy stays alive
- **Barcode inverts on dark** — a light bar pattern so gate scanners still read a dimmed screen
- **Apple Wallet pass** — the boarding pass is a first-class live `.pkpass` (gate-change push)
- **Calm functional motion** — plane glide, status cross-fade, active-dot pulse — no decorative bounce

## Brand Sources

- [Delta Air Lines](https://www.delta.com/)
- [Fly Delta iOS app](https://apps.apple.com/us/app/fly-delta/id388491656)
- [Open Sans (Google Fonts)](https://fonts.google.com/specimen/Open+Sans) — Apache 2.0 (free analog to Delta's licensed Whitney-class corporate face)
- Public brand palette: Delta Blue `#003268`, widget red `#C8102E`, SkyMiles gold `#C99700`
