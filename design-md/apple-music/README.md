# Apple Music iOS Inspired Design System

Design system docs inspired by the [Apple Music iOS app](https://apps.apple.com/us/app/apple-music/id1108187390). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Apple's public Human Interface Guidelines and SF Pro documentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` extensions, Play button, album tile, mini-player, Now Playing, time-synced lyrics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, `expo-audio` + Reanimated + BlurView |
| `preview.md` | Link to the interactive design token catalog on the Spectr gallery |

## Signature Moves

- **Apple Music Red** (`#FA2D48`) — Play button, heart liked, shuffle active, scrubber fill, tab bar indicator
- **Pure SF Pro** — no custom font, full reliance on Apple's system typeface with Dynamic Type from XS to AX5
- **12pt corner radius on album tiles** — signature softer-than-Spotify corner
- **`#000000` dark canvas** — full-contrast true black, not warm `#121212`
- **Time-synced lyrics** with word-by-word highlight — the defining Apple Music feature
- **Dolby Atmos gold badge** (`#D4A857`) on supported tracks, **Lossless silver** (`#8E8E93`) alongside
- **Album-art-extracted gradient** on Now Playing — Core Image `CIAreaAverage` with complementary color
- **`.regularMaterial` throughout** — nav bar, tab bar, and mini-player all use iOS system translucency
- **SF Symbols for every icon** — leaning fully into Apple's system design language

## Brand Sources

- [Apple Human Interface Guidelines — iOS](https://developer.apple.com/design/human-interface-guidelines/)
- [Apple Music for Artists — Brand Guidelines](https://artists.apple.com)
- [SF Symbols](https://developer.apple.com/sf-symbols/)
- Public brand palette: Apple Music Red `#FA2D48`, Canvas `#FFFFFF` / `#000000`
