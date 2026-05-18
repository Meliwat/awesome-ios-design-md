# McDonald's iOS Inspired Design System

Design system docs inspired by the [McDonald's iOS app](https://apps.apple.com/us/app/mcdonalds/id922103212). Not the official system. Brand colors, font names, and component patterns are cross-referenced with McDonald's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, rewards card, deal tile, order-mode selector, elevated Order FAB |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Scaffold + docked FAB + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Golden Arches Yellow** (`#FFC72C`) — primary CTA, rewards number, active tab, Order FAB; pressed `#E6B015`
- **Text on yellow is ALWAYS** near-black `#1A1A1A` — never white; the arches are never recolored (hard brand rule)
- **McDonald's Red** (`#DA291C`) — deal flags, the secondary "Add to Mobile Order" button, urgency; pressed `#B71F14`
- **MyMcDonald's Rewards card** — the home hero: huge yellow points number + `linear-gradient(#FFC72C, #FFD75E)` progress bar
- **Deals grid** — two-up product-photo tiles with a red `DEAL`/`FREE`/`NEW` flag and an "or N,NNN pts" alternative
- **Order-mode selector** — Pickup / Curbside / Drive Thru / Delivery as four tiles, active outlined `#FFC72C`
- **Elevated center Order FAB** — a 50pt yellow circle with a `rgba(255,199,44,0.40)` glow, lifted above the tab bar
- **Pill buttons everywhere** — 999pt-radius CTAs; rounded 16pt cards; friendly Speedee type
- **Coupon cards** — dashed yellow border, food photo, "Add to order" yellow pill
- **Personal greeting** — "Hi, {name}" with the arches and a notification bell
- **Near-black dark canvas** `#121212` — food photography stays full-saturation; the FAB glow intensifies
- **Friendly heavy type** — Speedee (Archivo fallback); screen titles 32pt/900; weight + yellow carry hierarchy

## Brand Sources

- [McDonald's](https://www.mcdonalds.com/)
- Speedee — McDonald's proprietary brand typeface (license required to bundle)
- Catalog/preview fallback: [Archivo (Google Fonts)](https://fonts.google.com/specimen/Archivo) — SIL OFL
- Public brand palette: Golden Arches Yellow `#FFC72C`, McDonald's Red `#DA291C`, text-on-yellow `#1A1A1A`
