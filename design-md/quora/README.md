# Quora iOS Inspired Design System

Design system docs inspired by the **Quora iOS app**. Not the official system. Brand colors, font names, and component patterns are cross-referenced with Quora's public brand presentation and observable in-app behavior; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Palette + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Serif-question / sans-answer split** — every question in Georgia, every answer in Inter; the brand made structural
- **Paper-white canvas** (`#FFFFFF`) with warm-gray surfaces (`#F7F7F8`) — a reading app, not a feed app
- **Quora Red** (`#B92B27`) as the only brand accent — wordmark, "Answer" CTA, editorial highlights
- **Upvote/downvote pill** — one bordered capsule; upvote-active turns blue (`#2E69FF`), downvote stays neutral gray
- **Credential byline** as a first-class type role — authority is the product, ranked above the timestamp
- **Answer body at 16pt / line-height 1.6** — long-form readability is non-negotiable
- **Spaces carousel** — the only place per-community color enters the monochrome feed
- **Near-zero shadow** — a paper metaphor; max 6% 3px card lift, real elevation only on sheets

## Brand Sources

- **Quora iOS app** — observed component behavior, the serif/sans split, vote pill, Spaces
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) — navigation, Dynamic Type, touch targets
- [Material 3 Guidelines](https://m3.material.io/) — Android `NavigationBar`, `ScrollableTabRow`, typography mapping
- Public brand palette: Quora Red `#B92B27`, Canvas `#FFFFFF`, Upvote Blue `#2E69FF`
