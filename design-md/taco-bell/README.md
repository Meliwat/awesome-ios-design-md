# Taco Bell iOS Inspired Design System

Design system docs inspired by the [Taco Bell iOS app](https://apps.apple.com/us/app/taco-bell-fast-food-delivery/id497387361). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Taco Bell's public product UI and brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, brand gradient, box hero, step pills, option row, CTA bar |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, brand `Brush`, Material 3 `Typography`, composables, navigation + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Purple → Magenta brand gradient** `linear-gradient(120deg, #702082 → #C72BC8)` — the identity on screen: CTAs, active step, box hero
- **Dark-first, purple-tinted near-black canvas** `#0E0A14` — a violet bias so the brand feels native, never neutral gray
- **Hot Sauce Yellow** `#FFC700` — the value highlight: prices, reward chips, UPPERCASE eyebrows; text on it is `#2A1530`
- **Build-your-box customizer** — the signature flow: gradient box-preview hero + numbered step pills + radio/stepper rows
- **Numbered step pills** — Main / Side / Drink / Sweet; exactly one gradient-active, completed turn green `#36C275`
- **Box-preview hero** — gradient panel with radial magenta + yellow glow blooms, name + item count + yellow price
- **Persistent bottom CTA bar** — live-updating box total + a gradient "Add to Order" button
- **Cravings menu tiles** — purple-gradient food image, name, yellow price, gradient "Add" pill → opens customizer
- **Glow over shadow** — radial blooms on the box hero and active step; drop shadow only for the CTA bar & sheets
- **Magenta controls** — `#C72BC8` single-select radios and magenta-outline quantity steppers
- **Neon heat accents** — Fire Orange `#FF6A1A`, Hot Pink `#E928A0`, sauce-picker heat chips
- **Heavy techy-geometric type** — screen titles 32pt/800; gradient + yellow + weight carry hierarchy; yellow eyebrows

## Brand Sources

- [Taco Bell](https://www.tacobell.com/)
- Taco Bell brand sans (clean techy-geometric humanist) — license required to bundle
- Catalog/preview fallback: [Sora (Google Fonts)](https://fonts.google.com/specimen/Sora) — SIL OFL
- Public brand palette: Taco Bell Purple `#702082`, Electric Magenta `#C72BC8`, Hot Sauce Yellow `#FFC700`
