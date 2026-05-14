# WHOOP iOS Inspired Design System

Design system docs inspired by the [WHOOP iOS app](https://apps.apple.com/us/app/whoop/id1404139421). Not the official system. Brand colors (Strain Green, the Recovery red→yellow→green ramp, Sleep Blue), typography (DIN 2014), and component patterns (Recovery ring, Strain bar, Sleep stage chart) are cross-referenced with WHOOP's public marketing and App Store screenshots; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, Recovery ring, Strain bar, Sleep stage chart, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, SVG ring with Reanimated, Skia heart-rate trace |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery/whoop](https://www.spectr.to/gallery/whoop) |

## Signature Moves

- **Pitch-black canvas** `#0A0A0A` on every screen — dark-mode-only by design, no light theme exists
- **Strain Green** `#00FF7B` as the signature accent — primary CTA, active tab, strain bar fill, live workout banner
- **Recovery ring** — open at the top, color-shifts red → yellow → green by score in 1% increments, 240pt diameter on Overview
- **DIN 2014 typography** — flat, mechanical, instrument-panel sans; ALL CAPS headers with 0.8–1.6pt wide tracking
- **Tabular numerals everywhere** — HRV, RHR, strain score, sleep duration, percentages — non-negotiable cockpit aesthetic
- **Neon glows replace shadows** — `rgba(0,255,123,0.18) 0 0 12px` on active tabs, `0.32` heavier glow on the live workout banner
- **Sleep stage chart** — horizontal stacked bar with REM `#9C4DFF`, Deep `#3D3DFF`, Light `#3DD9FF`, Awake `#5F5F65`
- **4-tab bottom bar** — Overview, Coaching, Community, Stats; uppercase labels, Strain Green active tint
- **4pt corner radius** on primary buttons — instrument-panel rectangle, never a pill
- **Hairline borders** `1pt #252525` instead of drop shadows for card definition on the dark canvas

## Brand Sources

- [WHOOP — Official Site](https://www.whoop.com/)
- [App Store — WHOOP](https://apps.apple.com/us/app/whoop/id1404139421)
- [WHOOP 4.0 Launch — Brand & Visual Identity](https://www.whoop.com/the-locker/whoop-4-0-launch/)
- Public brand palette: Strain Green `#00FF7B`, Recovery Red `#FF0026`, Recovery Yellow `#FFDE00`, Recovery Green `#16EC06`, Sleep Blue `#0093E7`, Canvas `#0A0A0A`
- Typography: [DIN 2014 — ParaType](https://www.paratype.com/fonts/pt/din-2014)
