# Coffee Meets Bagel iOS Inspired Design System

Design system docs inspired by the [Coffee Meets Bagel iOS app](https://apps.apple.com/us/app/coffee-meets-bagel-dating-app/id630109023). Not the official system. Brand colors, font names, and component patterns are cross-referenced with CMB's public product UI, brand site, and marketing; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, bagel card + action trio + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Coffee-house palette** — brew-brown (`#A0522D`) + bagel-orange (`#F4623A`) + cream (`#F3E4CF`) over warm brown-black (`#14100D`)
- **Curated & scarce** — a small daily batch of "bagels", framed as fresh and expiring — no infinite feed
- **Today's Bagel card** — one large profile at a time, 24pt radius, full-bleed photo, bottom gradient
- **Two-action commitment** — roasted-brown circular Pass + a noticeably larger bagel-orange Like
- **Send a Bagel** — brew-brown star action (`#A0522D`), spend beans to stand out
- **Cream replaces white** — on-photo text, outline buttons, prompt blocks; nothing is stark
- **Warm interest chips** — emoji + label; translucent cream over photos, brew-brown when selected
- **Batch header** — "6 fresh picks · expires in 23h" — finite, fresh, daily
- **Like heart pop** — 1.0→1.15→1.0 spring + soft haptic + a fading 12% orange card wash (`#F4623A1F`)
- **Match takeover** — warm orange→brown gradient celebration with bagel confetti, like-green check (`#4CC38A`)
- **Pill & circle everything** — fully rounded (999pt) buttons, circular Pass/Like/Send
- **Brown-warm soft shadows** — like café lamplight, never neutral grey; premium gold (`#E0A82E`) for upsell only

## Brand Sources

- [Coffee Meets Bagel](https://coffeemeetsbagel.com/)
- [CMB Brand / About](https://coffeemeetsbagel.com/about/) — coffee-and-bagel identity, roasted-brown + orange
- `Brandon Grotesque` by Hannes von Döhren / Gotham-family (CMB's licensed brand sans) — substitute with [Poppins by Indian Type Foundry](https://fonts.google.com/specimen/Poppins) (SIL OFL) for free distribution
- Public brand palette: brew-brown `#A0522D`, bagel-orange `#F4623A`, cream `#F3E4CF`, match green `#4CC38A`
