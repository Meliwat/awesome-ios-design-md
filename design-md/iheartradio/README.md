# iHeartRadio iOS Inspired Design System

Design system docs inspired by the [iHeartRadio iOS app](https://apps.apple.com/us/app/iheartradio/id290638154). Not the official system. Brand colors, font names, and component patterns are cross-referenced with iHeartRadio's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, heart-logomark tile, pulsing LIVE badge, scanning bar (no scrubber), red play/stop button |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, `expo-linear-gradient` tile, Reanimated LIVE pulse + scan sweep + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, station tile, scanning bar + red play/stop + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Warm maroon-black canvas** (`#120A0E`) — a red-tinted dark, never pure `#000000`
- **Scarce iHeart Red→Magenta system** (`#C6002B` → `#E40A5D`) — play button, heart logomark, brand chips
- **Coral reserved for "live"** (`#F23A2F`) — the LIVE badge, scanning sweep, live tags, active tab only
- **No scrubber** — an indeterminate coral scanning bar; a live stream has no position or seek
- **Stop, not pause** — the center transport is a square; prev/next are station-skip
- **Pulsing LIVE badge** — coral pill with a radiating white dot on a ~1.6s loop, the most "alive" element
- **Heart logomark as artwork** — the iHeart heart-check on a warm radial tile when no album art
- **68pt circular red play button** — the single brightest object on the player, with a red glow (`rgba(198,0,43,0.65)`)
- **Monochrome chrome** — chevrons, secondary transport, tab icons all `#B8A0A8`
- **Colored red glows, not gray shadows** — under the play button and station tile (`rgba(198,0,43,0.45)`)
- **Heavy display type** — iHeart Sans / Inter at weights 700–800 for titles, 900 for LIVE tags
- **Coral active tab tint** (`#F23A2F`) — no Material pill, just a tinted filled icon

## Brand Sources

- [iHeartRadio](https://www.iheart.com/)
- [iHeartMedia Brand & Press](https://www.iheartmedia.com/) — iHeart Red `#C6002B`, coral/magenta system, heart-check logomark
- iHeart Sans (brand typeface) — substitute [Inter by Rasmus Andersson](https://rsms.me/inter/) (SIL OFL)
- Public brand palette: red `#C6002B`, coral `#F23A2F`, magenta `#E40A5D`, warm maroon-black canvas `#120A0E`
