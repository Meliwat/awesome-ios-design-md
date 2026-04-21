# TikTok iOS Inspired Design System

Design system docs inspired by the [TikTok iOS app](https://apps.apple.com/us/app/tiktok/id835599320). Not the official system. Brand colors, font references, and component patterns are cross-referenced with TikTok's public brand usage, industry writeups, and visible app behavior; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views (iOS 17+) |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics (Expo SDK 51+) |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **True black canvas** (`#010101`) — effectively dark-only; no light mode
- **Chromatic aberration** (cyan `#25F4EE` + rose `#FE2C55` stroke offset around a white core) — the signature brand treatment, used on the wordmark, Create button, and loading spinner
- **Rose `#FE2C55` as the verb** — Like, Follow, scrubber fill, notification badges
- **Cyan `#25F4EE` as the accent** — Create button stripe, chromatic highlights
- **Full-bleed 9:16 video feed** — video fills edge-to-edge, UI floats on top
- **Right-side action rail** — avatar + follow-badge, heart, comment, bookmark, share, spinning music disc
- **Left-side info overlay** — @username, bold-hashtag caption, horizontally scrolling music marquee
- **Double-tap to like** — 120pt center-screen rose heart burst with heavy haptic
- **4pt corner radius on buttons** — TikTok's "almost-square" softness, not a full pill
- **Proxima Nova** at weights 400 / 600 / 700 with drop-shadowed text on video
- **Haptics everywhere** — heavy on like, success on follow, medium on share

## Brand Sources

- [TikTok Brand — Logo & Guidelines](https://www.tiktok.com/about/brand-guidelines)
- TikTok in-product design patterns (For You feed, right-rail actions)
- Public brand palette: Rose `#FE2C55`, Cyan `#25F4EE`, Canvas `#010101`
- Typography: Proxima Nova is used across TikTok's product UI per multiple industry references; TikTok does not ship a proprietary typeface
