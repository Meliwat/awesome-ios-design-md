# Vimeo iOS Inspired Design System

Design system docs inspired by the [Vimeo iOS app](https://apps.apple.com/us/app/vimeo/id425194759). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Vimeo's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, the cinematic player, Staff Pick badge, watch feed |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, player, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, ExoPlayer-backed player + curated feed |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Near-black ink canvas** (`#0D0E12`) — a screening room that makes video glow; surfaces lift one step to `#16181F`
- **Cinematic 16:9 player** — 14pt corners inset, translucent center play (`rgba(13,14,18,0.55)`), auto-fading minimal scrubber
- **One accent only** — Vimeo Blue `#00ADEF` (legacy `#1AB7EA`, pressed `#008FC4`) — scrubber fill, primary CTA, active tab, Follow
- **The Staff Pick gold star** (`#FFD24C`) — the brand's most iconic artifact; the *only* other saturated color, means exactly "curated"
- **Player + curated watch feed** — cinematic player on top, beautifully thumbnailed discovery list below
- **Creator-forward attribution** — avatar (gradient `#00ADEF → #8B5CF6`) + name + follower count + Follow pill on every video
- **Inter typography, editorial calm** — 18pt video titles, 13pt metadata `#6B7280`, never shouty
- **Tabular figures** for every runtime, timecode, and play count so the scrubber and metadata never jitter
- **Quiet chrome** — left-aligned wordmark top bar, 5-slot bottom tab with an emphasized center Upload action
- **Thumbnails are sacred** — never dimmed or tinted; only the duration chip overlays the frame (color-critical audience)
- **No drop shadows on dark** — cards lift via surface step + `#262A34` hairline, never elevation

## Brand Sources

- [Vimeo](https://vimeo.com/)
- [Vimeo Brand & Press](https://vimeo.com/about/brand)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (closest free face to Vimeo's product type)
- Public brand palette: Vimeo Blue `#00ADEF` / legacy `#1AB7EA`, ink canvas `#0D0E12`, Staff Pick gold `#FFD24C`
