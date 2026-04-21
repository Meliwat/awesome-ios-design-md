# Tinder iOS Inspired Design System

Design system docs inspired by the [Tinder iOS app](https://apps.apple.com/us/app/tinder/id547702041). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Tinder's public brand documentation, app-store screenshots, and the Tinder Brand page; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, swipe card with drag + stamps, action bar, match screen |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, Reanimated swipe card, Gesture Handler, match screen |
| `preview.md` | Link to the interactive Spectr gallery |

## Signature Moves

- **Swipe card** — 3:4 portrait photo, full-bleed, 16pt radius, drag with -15° to 15° rotation
- **Brand gradient** (`#FD267A → #FF6036`) on Match screens and premium CTAs only — never elsewhere
- **Five action buttons**, five colors: Rewind gold, Nope red, Super Like blue, Like green, Boost purple
- **LIKE / NOPE / SUPER LIKE stamps** — rotated 15° italic display type with colored borders
- **"Name, age" formula** — 28pt w700 + 24pt w400 separated by a comma
- **Match screen** — full pink→orange gradient, "It's a Match!" in italic display type, confetti burst
- **Icon-only tab bar** — Flame | Star | Chat | Profile — no labels
- **Gradient chat bubbles** for matched sender, `#F5F5F5` bubbles for your own messages
- **Photo paginator** — thin white segments at the top of each card, tap edges to cycle

## Brand Sources

- [Tinder Press / Brand resources](https://press.tinder.com/media-assets/)
- Public brand palette: Pink `#FD267A`, Orange `#FF6036`, Super Like Blue `#5D8DF1`
- Tinder Sans typography (2021) — designed in-house, succeeded Proxima Nova
