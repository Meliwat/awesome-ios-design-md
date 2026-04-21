# Netflix iOS Inspired Design System

Design system docs inspired by the [Netflix iOS app](https://apps.apple.com/us/app/netflix/id363590051). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Netflix's public brand documentation and Dalton Maag's Netflix Sans publication; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, Play button, poster rows, Top 10 numeral overlay, hero trailer, profile picker |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, `expo-video` + Reanimated + Haptics |
| `preview.md` | Link to the interactive design token catalog on the Spectr gallery |

## Signature Moves

- **Dark-only canvas** (`#141414`) — warm charcoal, never light mode
- **Netflix Red** (`#E50914`) reserved for the NETFLIX wordmark, Play button, and Continue Watching progress bar — nothing else
- **Full-bleed hero with muted trailer auto-play** after 2s delay, vertical gradient fade into canvas
- **"Top 10" row** — giant outlined numerals (1–10) rendered behind 2:3 posters, Netflix's most distinctive row composition
- **Continue Watching progress bar** — 2pt red fill at the bottom edge of the poster
- **2:3 portrait posters** at 4pt corner radius — soft but not rounded, Netflix's signature tile
- **"Are you still watching?" modal** after extended autoplay
- **Profile picker** on launch — colored circular avatars, "Who's watching?" prompt
- **Netflix Sans** (Dalton Maag, 2018) replacing Gotham across the product; weights 400 / 500 / 700

## Brand Sources

- [Netflix Brand Site](https://brand.netflix.com/) — brand guidelines
- [Dalton Maag — Netflix Sans case study (2018)](https://www.daltonmaag.com/work/netflix)
- Public brand palette: Netflix Red `#E50914`, Canvas `#141414`, Text `#FFFFFF`
