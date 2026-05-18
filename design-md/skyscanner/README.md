# Skyscanner iOS Inspired Design System

Design system docs inspired by the [Skyscanner iOS app](https://apps.apple.com/us/app/skyscanner-flights-hotels/id415458524). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Skyscanner's public product UI and brand material; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, search card, price grid, flight row, sort tabs |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, price grid + segmented control + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Single brand action color** — Skyscanner Sky Blue (`#0770E3`) carries 100% of primary actions; one blue action per screen
- **Traffic-light price system** — green `#00A698` cheap / amber `#FFB81C` average / red `#E5392E` expensive — the core mental model
- **Month price grid** — a 7-column calendar where every day is tinted by fare vs. typical, never a plain date picker
- **"Everywhere" destination** — find the cheapest place to fly, not just a known route; globe-iconed first-class search target
- **Stacked search card** — From / To / Dates with a circular swap button straddling the From–To divider
- **Best / Cheapest / Fastest** — Skyscanner's signature result sort triad; Best is the blended default
- **Flight result row** — horizontal timeline: time → line+dot+stops → time → bold fare (`#0E1B2C`)
- **Theme-invariant price hues** — green/amber/red never shift between light and dark; meaning is constant
- **Pure white tool-canvas** (`#FFFFFF`) light / cool navy-black (`#0B0F14`) dark — neutral, never photo-driven
- **Blue-tinted Search shadow** (`0 8px 18px -8px rgba(7,112,227,0.7)`) — the only persistently elevated element
- **Skyscanner Relative typeface** — friendly humanist sans, tabular figures for all fares/times
- **Price alerts** — track a route, get pushed when the fare drops

## Brand Sources

- [Skyscanner](https://www.skyscanner.net/)
- [Skyscanner brand & design](https://www.skyscanner.net/media/) — Sky Blue `#0770E3`, deep navy `#05203C`
- Skyscanner Relative typeface by [Dalton Maag](https://www.daltonmaag.com/)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (closest free substitute for previews)
- Public price-guidance system: green `#00A698` (cheap), amber `#FFB81C` (average), red `#E5392E` (expensive)
