# Monzo iOS Inspired Design System

Design system docs inspired by the [Monzo iOS app](https://apps.apple.com/gb/app/monzo-bank/id1052238659). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Monzo's public product UI, brand site, and design posts; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, card flip + transaction feed + Pots + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Hot Coral debit card** (`#FF3464`) as the Home-screen hero — the single most recognizable Monzo element, with a coral-tinted shadow
- **Monzo Navy** (`#14233C`) as the grounding brand color — light-mode ink and dark-mode card/row surface
- **Transaction feed as the interface** — circular merchant icons, category-emoji fallbacks, `category · time` meta, amount on the right, day-grouped
- **Pots** — round-cornered savings "coins" with emoji + goal, horizontal strip on Home (`#5CE0C4` mint, `#FFC75F` gold, `#5AA9F0` sky)
- **Big confident numbers** — the balance is the largest type; Inter tabular figures for all money
- **Income green, spend in primary text** — `#1FA971` light / `#2FCB8F` dark vs `#14233C` ink
- **Deep-navy dark mode** (`#0E1620`) — derived from the brand navy, NOT pure black
- **Card 3D flip** — tap the coral card to flip to card controls (freeze, PIN, details)
- **Pot money move** — coin springs into the Pot with a balance digit-ticker animation
- **Inter typeface** throughout — geometric humanist sans, tight tracking on large sizes
- **No tint pill on the active tab** — active is coral icon + coral label only
- **Conversational copy** — "Available to spend", "You've spent £24.10 today"

## Brand Sources

- [Monzo](https://monzo.com/)
- [Monzo brand & tone of voice](https://monzo.com/tone-of-voice/)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- Public brand palette: Hot Coral `#FF3464`, Monzo Navy `#14233C`, income green `#1FA971`
