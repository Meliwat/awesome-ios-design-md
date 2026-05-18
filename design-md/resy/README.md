# Resy iOS Inspired Design System

Design system docs inspired by the [Resy iOS app](https://apps.apple.com/us/app/resy/id866163372). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Resy's public product UI, marketing site, and the Notify / Hit List features; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, red-outline slot grid + Notify spine + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Pure black canvas** (`#000000`) — dark-first by design, this IS the brand; photography and serif type pop like print
- **Display-serif restaurant names + editorial titles** (Playfair Display / Resy serif) vs clean **Inter** for all UI — the serif/sans split is the brand
- **A single disciplined brand color** — Resy Red `#C73E3A`, brightening to `#E2504B` for the active tab on black
- **Red-outline reservation slot system** — available slots are red-OUTLINED (transparent fill), the primary slot is SOLID red — the contrast is the signal
- **Seating-type labels on every slot** — "Dining Room" / "Bar" / "Counter" / "Patio"; Resy books specific seatings
- **Notify (waitlist) as a first-class path** — sold-out slots become dashed **amber** `#D99A2B` "Notify" chips, plus a dedicated Notify tab
- **Editorial restaurant detail** — photo carousel + serif name + gold "★ 9.4" rating + italic Playfair tagline + date strip + slot grid
- **Muted gold** `#C9A24B` reserved for the /10 rating mark and the curated Hit List only
- **Near-white text** `#F5F5F5` on black — refined, non-glaring editorial contrast
- **Hairline `#262626` borders** as the elevation cue — drop-shadows are invisible on pure black
- **Crisp 8–10pt radii** (not pill-soft) — Resy's geometry is editorial
- **5-tab bottom bar** — Search / Hit List / Reservations / Notify / Account, active in bright Resy Red, no Material pill

## Brand Sources

- [Resy](https://resy.com/)
- [Resy Notify](https://resy.com/) — sold-out-table waitlist feature
- Resy brand serif (high-contrast transitional/didone display face)
- [Playfair Display (Google Fonts)](https://fonts.google.com/specimen/Playfair+Display) — SIL OFL, closest free analog and recommended substitute
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL, UI / body face
- Public brand palette: pure black `#000000`, Resy Red `#C73E3A` / `#E2504B`, Notify amber `#D99A2B`, rating gold `#C9A24B`
