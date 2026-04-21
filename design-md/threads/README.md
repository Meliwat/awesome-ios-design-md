# Threads iOS Inspired Design System

Design system docs inspired by the [Threads iOS app](https://apps.apple.com/us/app/threads/id6446901002). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Meta's Instagram Sans documentation and public Threads design references; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `preview.md` | Interactive token catalog on the Spectr gallery |

## Signature Moves

- **The thread line** — a 1pt vertical rule from avatar to reply row, visually connecting author to conversation
- **True-black canvas** (`#000000`) default with paper-white light mode (`#FFFFFF`) alternate
- **36pt circular avatars** — larger than X's 32pt, reflecting the photo-forward Instagram heritage
- **Four-icon action row** — heart, comment, repost, share — intentionally minimal, no bookmark or views
- **Instagram-coral like heart** (`#FE2C55`) — inherited from the Instagram double-tap heart, the only saturated color in the default feed
- **Instagram Sans** (proprietary, 2022) — narrow, tall-x-height font at weights 400/600/700
- **No hashtags or trending panel** — intentional minimalism vs X
- **Multi-post thread compose** — "+ Add to thread" builds chained posts with the thread line flowing between them

## Brand Sources

- [Meta Design — Instagram Sans typeface](https://design.meta.com/)
- [Threads iOS App Store listing](https://apps.apple.com/us/app/threads/id6446901002)
- Public palette: Like Coral `#FE2C55` (inherited from Instagram), Canvas `#000000`, Link Blue `#2D7FF9`, IG Verified `#0095F6`
