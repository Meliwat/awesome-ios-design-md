# SoundCloud iOS Inspired Design System

Design system docs inspired by the [SoundCloud iOS app](https://apps.apple.com/us/app/soundcloud-music-audio/id336353151). Not the official system. Brand colors, font names, and component patterns are cross-referenced with SoundCloud's public brand documentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **The waveform is the interface** — scrubber, progress bar, and comment timeline fused into one element
- **Inline commenter avatars** pinned to exact timestamps on the waveform baseline
- **SoundCloud Orange** (`#FF5500`) as the ONLY accent — play, progress, like, repost, active tab
- **Played = solid orange, unplayed = faded `#FF7700`** — the sweep advances linearly with playback
- **64pt circular orange Play FAB** with an orange-tinted glow shadow
- **4pt corner radius everywhere** — SoundCloud's signature *slight* rounding, never full pills
- **2pt orange progress line** on the persistent Now Playing mini-bar
- **Interstate typeface** (Inter fallback); weights 400/500/700; tabular numerals

## Brand Sources

- [SoundCloud Press & Brand Assets](https://press.soundcloud.com/)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- Public brand palette: SoundCloud Orange `#FF5500`, Orange Light `#FF7700`, Canvas `#FFFFFF` / `#1A1A1A`
