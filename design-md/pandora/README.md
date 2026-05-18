# Pandora iOS Inspired Design System

Design system docs inspired by the [Pandora iOS app](https://apps.apple.com/us/app/pandora-music-podcasts/id284035177). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Pandora's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, thumb controls + Now Playing + station row |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, album-art gradient hero + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **The thumb up/down IS the product** — the Music Genome Project gesture that shapes every station
- **Thumb asymmetry** — active thumb-up fills solid Bright Blue `#3668FF` (a lasting vote); thumb-down stays a hollow outline always (a one-time skip)
- **Now Playing = album-art gradient hero** — a color sampled from the artwork bleeds top-down into the navy floor; chrome floats with NO card frame
- **The station list is home** — genome-built radio stations as square-art rows with a now-playing equalizer
- **Pandora Blue `#224099`** deepens to a near-navy canvas `#0B0F1C` — dark-first, never a neutral gray
- **Bright Blue `#3668FF`** is the constant accent — active thumb-up, the circular play button, the scrubber fill, every primary CTA
- **Lean-back transport** — five large, well-spaced controls: thumb down · skip back · big play · skip forward · thumb up
- **Album artwork is sacred** — never tinted; only the surrounding gradient/UI shifts per track
- **Blue-glow play button** — the only solid-blue control besides an active thumb-up
- **Calm, tabular metadata** — timecodes and thumb counts are tabular; the track title is heavy (800) to read over art
- **Now-playing equalizer** — a tiny 4-bar `#3668FF` animation marking the active station and the mini player
- **Bottom tab bar** — For You / Browse / My Collection / Recents, text-primary active tint, NO pill indicator

## Brand Sources

- [Pandora](https://www.pandora.com/)
- [Music Genome Project](https://www.pandora.com/about/mgp)
- Pandora brand sans (proprietary) — implementation fallback: [Inter by Rasmus Andersson](https://rsms.me/inter/) (SIL OFL)
- Public brand palette: Pandora Blue `#224099`, bright blue `#3668FF`, near-navy canvas `#0B0F1C`
