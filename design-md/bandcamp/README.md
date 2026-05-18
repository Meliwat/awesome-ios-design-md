# Bandcamp iOS Inspired Design System

Design system docs inspired by the [Bandcamp iOS app](https://apps.apple.com/us/app/bandcamp/id507014823). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Bandcamp's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, big square art, buy/support card, inline teal-waveform player, tracklist, collection card |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, `expo-image` square art, buy card, tracklist + collection card + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, buy/support card, inline player + tracklist + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Paper-first canvas** (`#FFFFFF` / `#F4F4F4`) — light, editorial; the artwork is the brand, not the chrome
- **Big square album art** — full-bleed 1:1 covers, never cropped to a circle, never decorated or tinted
- **Scarce Bandcamp Teal** (`#1DA0C3`) — artist links, the buy price, the Buy button, collection accents
- **Buy/Support card** — bordered paper-gray block with price + "name your price" + teal Buy button + wishlist
- **Fan collection feed** — social "{Fan} bought {Album}" cards with cover, title, artist, and buyer note
- **Inline teal-waveform player** — a banded strip (not a card) with a teal waveform and tabular time
- **Clean numbered tracklist** — hairline-separated rows; the playing track is teal `#1DA0C3` + 700
- **Borders over shadows** — hairline gray `#E2E2E2` and surface steps organize the page (Bandcamp is flat)
- **Restrained geometry** — 4pt buttons, 2–4pt art tiles, 6pt the buy card; no pills, no big radii
- **Editorial type** — humanist sans (DM Sans fallback) at weights 400/600/700, record-sleeve rhythm
- **Ink, not black** — text is `#1A1A1A`; tabular numerals for track numbers, durations, prices
- **True dark inversion** — cool charcoal `#101417`, teal shifts to `#3DB5D6`; NOT the default, art never dims

## Brand Sources

- [Bandcamp](https://bandcamp.com/)
- [Bandcamp Help & Brand](https://get.bandcamp.help/) — Bandcamp Teal `#1DA0C3`, paper-editorial UI, the buy/support model
- Bandcamp product humanist sans — substitute [DM Sans (Google Fonts)](https://fonts.google.com/specimen/DM+Sans) (SIL OFL)
- Public brand palette: teal `#1DA0C3` / `#629AA9`, paper canvas `#FFFFFF` / `#F4F4F4`, ink `#1A1A1A`
