# Facebook iOS Inspired Design System

Design system docs inspired by the [Facebook iOS app](https://apps.apple.com/us/app/facebook/id284882215). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Meta's public brand guidelines and the Facebook design blog; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `preview.md` | Interactive token catalog on the Spectr gallery |

## Signature Moves

- **Card-based feed** — every post, ad, story, suggestion sits in its own white rounded 8pt card with 8pt canvas gaps
- **Soft-gray canvas** (`#F0F2F5` light / `#18191A` dark) — the between-card color that defines the rhythm
- **Facebook Blue** (`#1877F2`) — the brand: `f` logo, active Like thumb, primary CTAs, links
- **Reactions popover** — 7-emoji floating pill on long-press of Like, scale-on-hover + haptic-on-drag + emoji swaps into Like slot on release
- **5-tab labeled bottom bar** — Home / Video / Marketplace / Notifications / Menu — labels always shown
- **Blue `f` glyph** as the top-left brand anchor (not a word mark, just the `f`)
- **SF Pro** system font at weights 400/600/700/900 — `f` logo is the only heavy-weight moment
- **Instagram-gradient stories ring** — pink → red → orange inherited from Meta

## Brand Sources

- [Meta Design — Brand & Resources](https://design.meta.com/)
- [Facebook Design Blog](https://facebook.design/)
- Public palette: Facebook Blue `#1877F2`, Love `#F3425F`, Canvas `#F0F2F5`, Success `#42B72A`
