# Reddit iOS Inspired Design System

Design system docs inspired by the [Reddit iOS app](https://apps.apple.com/us/app/reddit/id1064216828). Not the official system. Brand colors, typography conventions, and component patterns are cross-referenced with Reddit's public brand guidelines and developer documentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `preview.md` | Link to the interactive Spectr gallery preview |

## Signature Moves

- **Vote column** on every post and comment — up arrow + karma + down arrow, the single most iconic Reddit control
- **Semantic vote pair** — Upvote Orange `#FF8717` and Downvote Blue-Purple `#7193FF` (complementary, warm vs cool)
- **Reddit Orange-Red** (`#FF4500`) as the brand color — logo, primary CTAs, NOT the upvote color
- **Flat card feed** with 16pt corner radius (card view) or 0pt full-bleed (classic view) — never shadows
- **Subreddit accent color customization** per-community — Join button and sidebar tint adapt
- **Flair pills** (4pt rounded rectangles) for post tags — NSFW yellow, Spoiler black, OC green, plus per-subreddit colors
- **Comment indentation** — 10pt step per reply, with a 2pt vertical rule on the leading edge
- **Reddit Sans** (proprietary, 2023+) with Noto Sans and SF Pro fallbacks; 14pt body workhorse
- **Snoo the alien mascot** as empty-state character and pull-to-refresh spinner
- **Markdown inline everywhere** — bold, italic, quote, code, link rendered in posts and comments

## Brand Sources

- [Reddit Brand Guidelines](https://www.redditinc.com/brand)
- [Reddit Blog — New Typography (Reddit Sans)](https://www.redditinc.com/blog/reddit-sans)
- Public brand palette: Reddit Orange-Red `#FF4500`, Upvote `#FF8717`, Downvote `#7193FF`
- Canvas: `#F6F7F8` (new light) / `#1A1A1B` (dark); cards `#FFFFFF` / `#272729`
