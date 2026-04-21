# Discord iOS Inspired Design System

Design system docs inspired by the [Discord iOS app](https://apps.apple.com/us/app/discord-chat-talk-hangout/id985746746). Not the official system. Brand colors, typography, and component patterns are cross-referenced with Discord's public brand guidelines and developer documentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `preview.md` | Link to the interactive Spectr gallery preview |

## Signature Moves

- **Three-gray surface system** — server rail `#1E1F22` → channel list `#2B2D31` → chat `#313338`
- **Blurple** (`#5865F2`) as the single brand accent — CTAs, mentions, active states
- **Server rail** with 48pt rounded-square icons — corner radius morphs 16pt → 12pt on active (the "squircle-er" effect)
- **4pt × 40pt white active-indicator bar** on the leading edge of the active server
- **Presence dots** (green/yellow/red/gray/purple) on every avatar with 2pt canvas-colored border
- **Role-colored usernames** — per-server, arbitrary hex, applied in chat and member list
- **Three-pane horizontal-swipe navigation** on mobile — server rail + channel list as a left drawer
- **Custom emoji + animated emoji reactions** with Blurple-tinted chips when you've reacted
- **Markdown inline**: bold, italic, spoiler, code block, quote — rendered in every message
- **gg sans** (proprietary, replaced Whitney in 2023) — compact 14-16pt working range

## Brand Sources

- [Discord Brand](https://discord.com/branding)
- [Discord Developer Portal](https://discord.com/developers/docs/intro)
- Public brand palette: Blurple `#5865F2`, Online Green `#23A55A`, DND Red `#F23F43`
- Three-gray surfaces: `#1E1F22` / `#2B2D31` / `#313338`
