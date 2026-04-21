# YouTube iOS Inspired Design System

Design system docs inspired by the [YouTube iOS app](https://apps.apple.com/us/app/youtube-watch-listen-stream/id544007664). Not the official system. Brand colors, font names, and component patterns are cross-referenced with YouTube's public brand documentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, Subscribe button, video card, mini-player, Shorts rail |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, `expo-video` + Reanimated + Haptics |
| `preview.md` | Link to the interactive design token catalog on the Spectr gallery |

## Signature Moves

- **YouTube Red** (`#FF0000`) reserved for the Subscribe button, LIVE indicator, and brand logomark — nothing else
- **16:9 thumbnail with duration tag** — the atomic unit of the feed, hard edges (0pt radius), black chip bottom-right
- **Subscribe button morph** — red pill → gray "Subscribed" pill with bell icon, 200ms spring + light haptic
- **Shorts player** — portrait 9:16 vertical video with right-rail actions, channel avatar with `+` subscribe badge
- **Draggable mini-player** — full player collapses to 72pt bar above tab bar, persists across navigation
- **Hidden dislike count** — dislikes tracked privately per the 2021+ product decision
- **Filter chips with full color inversion** — black bg / white text on active selection
- **YouTube Sans + Roboto** — display font paired with body font, weights 400 / 500 / 700
- **Dark canvas `#0F0F0F`** — not pure black, YouTube's signature near-black

## Brand Sources

- [YouTube Brand Resources](https://www.youtube.com/howyoutubeworks/resources/brand-resources/)
- [Google Material Design — YouTube case study references](https://m3.material.io)
- Public brand palette: YouTube Red `#FF0000`, Dark Canvas `#0F0F0F`, Light Canvas `#FFFFFF`
