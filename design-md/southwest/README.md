# Southwest iOS Inspired Design System

Design system docs inspired by the [Southwest Airlines iOS app](https://apps.apple.com/us/app/southwest-airlines/id344542975). Not the official system. Brand colors, the Heart logo palette, font names, and component patterns are cross-referenced with Southwest's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, boarding-position + check-in components |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Canvas countdown ring + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **No seat map** — the product is a boarding position (group A/B/C + number 1–60), never a seat
- **Giant boarding-position readout** `A23` — Warm Yellow (`#F9B612`) group letter, white number, the loudest element on screen
- **24-hour check-in countdown** — a launch-clock ring (`#F9B612` on `#2A3A57` track) that flips to the full Yellow CTA at T-24h
- **Heart tri-color role system** — Blue (`#304CB2`) = structure, Yellow (`#F9B612`) = action, Red (`#E51D23`) = alerts only
- **Wanna Get Away fare ladder** — Wanna Get Away / WGA Plus / Anytime / Business Select, dollars + Rapid Rewards points, cheapest leftmost
- **Rapid Rewards** — points-forward pill (`#304CB2` fill, `#F9B612` star), A-List / A-List Preferred tier badges, Companion Pass
- **Navy canvas** — deep `#0E1726` dark (a darkened Southwest Blue) / soft cool-gray `#F2F5FB` + white light — never neutral black
- **Heavy numeric type** — boarding group up to 88pt weight 900, tabular figures so positions/fares/clocks never jitter
- **One Yellow per screen** — exactly one Warm Yellow CTA; Blue carries all other structure
- **Bottom tab bar** — Home / Book / Trips / Rapid Rewards / Account, Warm Yellow active tint, NO pill indicator
- **Warm, plainspoken voice** — "Bags fly free", "Wanna Get Away", "Transfarency", "no change fees"
- **Blue-glow elevation** — the boarding card lifts on a Southwest-Blue-tinted shadow, not a gray one

## Brand Sources

- [Southwest Airlines](https://www.southwest.com/)
- [Southwest Rapid Rewards](https://www.southwest.com/rapidrewards/)
- Southwest Sans (proprietary, Monotype) — implementation fallback: [Inter by Rasmus Andersson](https://rsms.me/inter/) (SIL OFL)
- Public brand palette — the Heart tri-color: Southwest Blue `#304CB2`, Bold Red `#E51D23`, Warm Yellow `#F9B612`
