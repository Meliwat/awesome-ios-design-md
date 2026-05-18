# VSCO iOS Inspired Design System

Design system docs inspired by the [VSCO iOS app](https://apps.apple.com/us/app/vsco-photo-video-editor/id588013838). Not the official system. Brand colors, font names, and component patterns are cross-referenced with VSCO's public product UI and brand materials; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, hairline slider + preset carousel + tool tray |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Gesture Handler + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, hairline slider + preset carousel + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Pure black canvas** (`#000000`) — true black, dark-native editor; there is NO light editing theme
- **Monochrome UI** — white (`#FFFFFF`) + three grays (`#0E0E0E` / `#1A1A1A` / `#242424`); the photograph is the only color
- **Hairline bipolar slider** — 1px track on `#3A3A3A`, white fill from the zero-point, 18pt solid-white knob
- **Film-preset carousel** — 56pt square live-preview tiles, 2px `#FFFFFF` ring on the active preset (never a tint)
- **Adjust tool tray** — UPPERCASE Inter labels at `+1.6` tracking; selecting a tool swaps the carousel for the slider
- **Selection = white only** — a 2px white ring or a label color change (`#B3B3B3` → `#FFFFFF`), never a brand tint
- **No accent color** — VSCO has no "Spotify Green"; the only non-monochrome UI color is destructive red `#E5484D`
- **2pt corner radius** default (film-frame square); buttons are rectangles, not pills
- **Gapless Studio grid** — square thumbnails, 1px `#2A2A2A` gutters, 0pt radius — a contact sheet of negatives
- **Before/after press-and-hold** — press the photo to reveal the original; instant swap, zero chrome
- **The photo is sacred** — maximum black breathing room, never clipped or crowded by chrome
- **One content shadow only** — `rgba(0,0,0,0.6) 0 1px 4px` under the slider knob so it reads on a bright photo

## Brand Sources

- [VSCO](https://www.vsco.co/)
- [VSCO on the App Store](https://apps.apple.com/us/app/vsco-photo-video-editor/id588013838)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (free substitute for the proprietary VSCO Gothic)
- Public brand palette: true black `#000000`, white `#FFFFFF`, hairline track `#3A3A3A`, monochrome chrome, no brand accent
