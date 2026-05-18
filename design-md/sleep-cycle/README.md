# Sleep Cycle iOS Inspired Design System

Design system docs inspired by the [Sleep Cycle iOS app](https://apps.apple.com/us/app/sleep-cycle-sleep-tracker/id320606217). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Sleep Cycle's public product UI and App Store materials; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, hypnogram + score ring, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, react-native-svg charts, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, Canvas hypnogram + ring + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Indigo→night gradient shell** — `#2A2D5A → #3B4371 → #14152E`; dark-first, soft glow, never pure black
- **Sleep-analysis graph (hypnogram)** — a smooth glowing aqua wave (`#7FE3F0` stroke over `#6C7BFF` gradient fill) over color-coded stages
- **Sleep-quality ring** — circular arc with a big 0–100 score, color-banded `#FF8FB1` / `#6C7BFF` / `#4FD1E6`
- **Time-asleep hero stat** — the emotional headline (e.g. "7h 36m"), heaviest type on screen
- **Smart-alarm card** — gradient icon tile + "wakes you in your lightest sleep" + big wake time
- **One bright accent only** — Aqua `#4FD1E6`; everything else is muted indigo/lavender
- **Sleep-stage color system** — Deep `#3D4ABF`, Light `#6C7BFF`, REM `#4FD1E6`, Awake `#FF8FB1`
- **Soft floating cards** — 16–20pt radius, low soft shadow `rgba(0,0,0,0.35)` over the gradient
- **Colored glow button** — primary CTA gradient `#6C7BFF → #3D4ABF` with `rgba(108,123,255,0.35)` glow, not a gray drop shadow
- **Nunito rounded sans** — heavy numerals (800–900), light body (400) in muted lavender `#A6A9D4`
- **Auto-dim** — screen brightness drops further during an active tracking session
- **Minimal chrome** — 5-tab bar (Sleep / Journal / Alarm / Sounds / Profile), no opaque nav bar

## Brand Sources

- [Sleep Cycle](https://www.sleepcycle.com/)
- [Nunito by Vernon Adams / Jacques Le Bailly (Google Fonts)](https://fonts.google.com/specimen/Nunito) — SIL OFL
- Public brand palette: indigo→night gradient `#2A2D5A → #3B4371`, deep canvas `#14152E`, aqua accent `#4FD1E6`, sleep-stage colors
