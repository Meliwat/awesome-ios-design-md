# Plenty of Fish iOS Inspired Design System

Design system docs inspired by the [Plenty of Fish iOS app](https://apps.apple.com/us/app/pof-dating-app/id306407220). Not the official system. Brand colors, font names, and component patterns are cross-referenced with POF's public product UI and brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `LinearGradient` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, match grid + Meet Me + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **POF Blue is the brand** — `#0098DB → #00A6E2` on the logo, primary CTA, active tab and the like button
- **Match grid** — a 2-column scrolling grid of full-bleed portrait photo cards with bottom name/meta scrims
- **Meet Me** — one big profile card + three round buttons (✕ no / ? maybe / ♥ yes), the ♥ in POF Blue with a glow
- **"Online" presence pill** — teal dot (`#00C9B7`) + "Online", aggressive presence + pulsing dot
- **Messages-first** — a prominent Messages tab with a pink (`#FF4F8B`) unread badge
- **Cool near-black dark canvas** (`#121417`) — never pure black; cards lift to `#1A1D21`
- **Text on photos is always white** over a `transparent → rgba(0,0,0,0.78)` scrim
- **Rounded everything** — 16–20pt photo cards, 999pt pill buttons, circular actions
- **Heavy rounded type** — Nunito Sans 800/900 for names and titles; approachable, unintimidating
- **Color rationed by meaning** — blue = brand/action, teal = presence, pink = unread, gold = upgrade
- **Distance + last-active everywhere** — "3 mi · Active now" is core metadata, not decoration
- **Full-color photography edge-to-edge** in both themes — chrome dims, content does not

## Brand Sources

- [Plenty of Fish](https://www.pof.com/)
- [Nunito Sans (Google Fonts)](https://fonts.google.com/specimen/Nunito+Sans) — SIL OFL (POF UI stand-in)
- Public brand palette: POF Blue `#0098DB → #00A6E2`, online teal `#00C9B7`, unread pink `#FF4F8B`, upgrade gold `#FFB23E`
