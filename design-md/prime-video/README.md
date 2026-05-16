# Prime Video iOS Inspired Design System

Design system docs inspired by the [Prime Video iOS app](https://apps.apple.com/us/app/amazon-prime-video/id545519333). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Amazon's public brand presence; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, motion + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Dark navy canvas** (`#0F171E`) — Amazon blue-slate, not neutral black
- **Prime Blue** (`#00A8E1`) as the single accent — the "Play" verb, active tab, X-Ray, watchlist
- **X-Ray cast overlay** — tap during playback to slide up a live cast/scene/music panel
- **Hero billboard** — full-bleed auto-still with the blue Play CTA and watchlist plus
- **Watchlist plus** — a `+` that fills blue with a success haptic when added
- **Channel & category storefront rows** — subscriptions interleaved with included content
- **Row scroll snap** — rows paginate with a gentle stop; X-Ray slides up over video
- **IMDb badge** in `#F5C518` — brand-family (Amazon owns IMDb)
- **Amazon Ember typeface** (Inter fallback); weights 400/600/700

## Brand Sources

- [Apple App Store — Amazon Prime Video](https://apps.apple.com/us/app/amazon-prime-video/id545519333)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: Prime Blue `#00A8E1`, Canvas `#0F171E`, IMDb `#F5C518`
