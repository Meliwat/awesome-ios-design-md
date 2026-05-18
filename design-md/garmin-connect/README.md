# Garmin Connect iOS Inspired Design System

Design system docs inspired by the [Garmin Connect iOS app](https://apps.apple.com/us/app/garmin-connect/id583446403). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Garmin's public product UI, the Garmin brand guidelines, and the company's design posts; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, route map, stat grid, Training Status ring, Body Battery gauge, HR zones |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, SVG route, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, Canvas route + rings, navigation + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **True-black canvas** (`#000000`) — dark-first, OLED-friendly, makes route lines and zone colors pop
- **Single brand accent** — Garmin Blue `#007CC3` (brightened to `#2A9FD6` for text/active on black)
- **Functional color system** — HR zones green→gold→orange→red (`#4CAF50` / `#C8B560` / `#F0A030` / `#E5402A`), each hue a fixed meaning
- **Body Battery** gauge in `#2EA8E0`, Intensity Minutes in `#00A8A8`, Steps in `#C8B560` — theme-invariant
- **Roboto + Roboto Condensed** — condensed tabular numerals keep dense metrics on one line
- **Glowing GPS route** — `#007CC3` polyline with `drop-shadow(0 0 4px rgba(0,124,195,0.6))`, green `#4CAF50` start / red `#E5402A` end pins
- **Tight stat grid** — `#121417` cells on `#2A2E33` hairline mortar, 12pt outer radius
- **The number is the hero** — big condensed value over a 10–12pt uppercase gray eyebrow
- **Depth via surface lightness** — `#000000 → #121417 → #1B1E22`, almost no drop shadows
- **No tint pill** in the 4-tab bar — active is simply the blue glyph
- **Training Status / Body Battery / VO₂ Max** as named, ring- or gauge-backed hero metrics
- **Purposeful motion** — route draws on (700ms), rings/bars fill from 0 (600ms), stat cells stagger-rise

## Brand Sources

- [Garmin](https://www.garmin.com/) · [Garmin Connect](https://connect.garmin.com/)
- [Garmin brand & logo guidelines](https://www.garmin.com/en-US/legal/trademarks/) — Garmin Blue `#007CC3`
- [Roboto by Christian Robertson](https://fonts.google.com/specimen/Roboto) — Apache 2.0
- [Roboto Condensed](https://fonts.google.com/specimen/Roboto+Condensed) — Apache 2.0
- Public product UI: true-black canvas, single Garmin-Blue accent, HR-zone color ramp, Body Battery `#2EA8E0`
