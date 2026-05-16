# Calm iOS Inspired Design System

Design system docs inspired by the [Calm iOS app](https://apps.apple.com/us/app/calm/id571800810). Not the official system. Brand colors, font choices, and component patterns are cross-referenced with Calm's public brand presentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Night-sky gradient canvas** (`#2A6FD6` → `#0B1E3F`) — dusk, not pure black
- **Glass surfaces** — translucent white (`rgba(255,255,255,0.08)`) so the sky shows through
- **Calm Blue** (`#2A6FD6`) as the single accent — primary actions, active tab, progress
- **Serif/sans pairing** — **Lora** for headlines (bedtime-story warmth), **Inter** for body
- **The breathe bubble** — a soft circle on a true 4-7-8 ease-in-out cycle (signature)
- **Daily Calm hero card** — full-bleed scrim-darkened photography, the day's session
- **Full-bleed nature photography** behind content, gently navy-scrimmed for legibility
- **Slow continuous motion** — 500ms+ cross-dissolves, nothing snaps or bounces

## Brand Sources

- [Calm — official site](https://www.calm.com)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [Lora typeface (Google Fonts)](https://fonts.google.com/specimen/Lora) · [Inter typeface](https://fonts.google.com/specimen/Inter)
- Public palette: Calm Blue `#2A6FD6`, gradient base `#0B1E3F`, glass white `rgba(255,255,255,0.08)`
