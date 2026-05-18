# Zwift iOS Inspired Design System

Design system docs inspired by the [Zwift iOS app](https://apps.apple.com/us/app/zwift-ride-run-train/id1116359402). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Zwift's public product UI and App Store assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, glass `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, BlurView glass, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, rider HUD + glass |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Full-bleed 3D virtual world** is the canvas during a ride — the UI floats on a rendered game
- **Glassmorphic overlays** — all in-ride chrome is translucent dark glass (`rgba(12,12,12,0.6)` + blur)
- **Rider HUD** — big metric tiles (Power / Cadence / Heart-rate / W/kg) readable from a trainer 2m away
- **Fixed semantic metric colors** — Power orange `#FC6719`, Cadence yellow `#E8C547`, HR red `#F0413E`, W/kg cyan `#2BD4D9` (learned by muscle memory — never remap)
- **Zwift Orange** (`#FC6719`) is the brand action color — Start Ride, rider jersey, route progress, power-up
- **Tall condensed display face** (Barlow Semi Condensed) for all numbers/titles/buttons; Inter for body
- **Route banner** — translucent top strip: world + route name, distance/lap, orange progress bar
- **Power-Up** — a game-ability tile that fills solid orange when armed; a videogame affordance in a fitness app
- **Tabular figures + ~200ms tick** so the HUD never jumps as numbers change
- **App-shell fallback** — menus/route picker/profile on a solid `#161616` dark base, same orange
- **Achievement bursts** — green (`#57C84D`) badges + an orange confetti sweep + haptic
- **Glass, not shadows** — UI is separated from the world by frosted glass + a 1pt light stroke

## Brand Sources

- [Zwift](https://www.zwift.com/)
- [Zwift on the App Store](https://apps.apple.com/us/app/zwift-ride-run-train/id1116359402)
- [Barlow Semi Condensed (Google Fonts)](https://fonts.google.com/specimen/Barlow+Semi+Condensed) — SIL OFL
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- Public brand palette: Zwift Orange `#FC6719`, app canvas `#161616`, metric semantics (power orange / cadence yellow `#E8C547` / HR red `#F0413E` / W/kg cyan `#2BD4D9`)
