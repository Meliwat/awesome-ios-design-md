# Amazon Music iOS Inspired Design System

Design system docs inspired by the [Amazon Music iOS app](https://apps.apple.com/us/app/amazon-music-songs-podcasts/id510855668). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Amazon Music's public product UI and Amazon's brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, player + X-Ray lyrics + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Deep teal-navy canvas** (`#0C1B22`) — a tinted dark, NOT neutral grey; the brand thumbprint
- **Full-screen player + X-Ray lyrics** — synced line-by-line; the current line in bright cyan `#25D1DA`
- **"X-RAY" badge** — the cyan chip marking the lyrics/credits panel (Amazon's cross-product feature brand)
- **Amazon Cyan** (`#00A8E1`) accent + glow cyan (`#25D1DA`) — both drawn from the canvas hue family
- **Cyan play button** — a `#00A8E1` circle with a white glyph and a soft cyan glow (cyan IS the action)
- **Top-down player gradient** — lifts toward `#16404C` behind the art, settles back to `#0C1B22`
- **Tap-a-lyric-to-seek** — tapping any X-Ray line scrubs playback to that timestamp
- **Teal-tinted greys** — secondary `#9FB6BF`, divider `#234653` (never neutral grey)
- **Amazon Ember type** — Amazon's corporate face, shared across Amazon.com / Kindle / Alexa
- **Bright-cyan active tab** — `#25D1DA`, brighter than the structural `#00A8E1`; no tint pill
- **Time-of-day greeting** — the Home title shifts ("Good morning" / "Good evening")

## Brand Sources

- [Amazon Music](https://music.amazon.com/)
- [Amazon Brand Guidelines](https://advertising.amazon.com/resources/ad-policy/brand-usage-guidelines)
- Amazon Ember (Amazon's corporate typeface, by Dalton Maag) — documented fallback: [Inter](https://fonts.google.com/specimen/Inter)
- Public brand palette: Amazon Music Cyan `#00A8E1`, glow cyan `#25D1DA`, deep teal-navy canvas `#0C1B22`
