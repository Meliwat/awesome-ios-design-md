# Spotify iOS Inspired Design System

Design system docs inspired by the [Spotify iOS app](https://apps.apple.com/us/app/spotify-music-and-podcasts/id324684580). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Spotify's public brand documentation and its Encore design system; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **True dark canvas** (`#121212`) — not pure black
- **Spotify Green** (`#1DB954`) as the ONLY accent — reserved for primary play/follow actions
- **Album art as the color story** — Now Playing gradient extracted dynamically from cover
- **72pt circular Play button** with black icon on green background (not white — intentional)
- **Spotify Mix font** (proprietary, replaced Circular in 2024); weights 400/600/700
- **Persistent Now Playing mini-bar** above the tab bar while playback is active
- **White-active tab bar** — green is reserved for structural primary actions

## Brand Sources

- [Spotify for Developers — Design & Branding Guidelines](https://developer.spotify.com/documentation/design)
- [Spotify Design — Reimagining Design Systems at Spotify](https://spotify.design/article/reimagining-design-systems-at-spotify)
- Public brand palette: Spotify Green `#1DB954`, Canvas `#121212`, Logo Green `#1ED760`
