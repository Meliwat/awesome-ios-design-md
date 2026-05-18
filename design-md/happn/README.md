# happn iOS Inspired Design System

Design system docs inspired by the [happn iOS app](https://apps.apple.com/us/app/happn-dating-app/id620960306). Not the official system. Brand colors, font names, and component patterns are cross-referenced with happn's public product UI and brand identity; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Crossings timeline + Charm + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Crossings timeline** — the home screen: a vertical connector-line feed of location+time-stamped cards, not a swipe deck
- **happn pink** `#FF4865` as connective tissue — timeline dots, Charm heart, active tab, primary CTA, map pins
- **Crossing count in pink** — "You crossed paths N times" (`#FF4865`) is the emotional differentiator
- **Connector spine** — a 2pt `#2A2A33` line with 12pt pink dots + `rgba(255,72,101,0.18)` halos; 26pt sacred inset
- **Charm gesture** — a one-tap pink heart (`#FF4865`, shadow `rgba(255,72,101,0.5)`); ghost variant `#21212B`
- **Pink→magenta gradient** (`#FF4865 → #E91E63`) on hero buttons and the Crush star
- **Crush** — a mutual Charm that unlocks chat, celebrated with a full **gold `#FFC24B`** takeover (gold appears nowhere else)
- **Map crossing pins** — the same crossings as teardrop pins (`#FF4865` / gradient / ghost) on a city map
- **FlashNote** — a short opener attached to a Charm so first contact references something real
- **Two-font system** — Poppins for warmth (wordmark/names/headings/buttons), Inter for legibility (body/location/chat)
- **Graphite canvas** `#0E0E12` dark (not pure black) / off-white light — ships both, recognizable on dark
- **No tab tint pill** — active tab is the pink icon + pink label only

## Brand Sources

- [happn](https://www.happn.com/)
- [Poppins by Indian Type Foundry & Jonny Pinhorn](https://fonts.google.com/specimen/Poppins) — SIL OFL
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- Public brand palette: happn pink `#FF4865`, magenta `#E91E63`, reserved Crush gold `#FFC24B`
