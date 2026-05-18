# Midjourney iOS Inspired Design System

Design system docs inspired by the [Midjourney iOS app](https://apps.apple.com/us/app/midjourney/id6446882983). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Midjourney's public product UI and the company's web app; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, job feed + prompt bar + U/V chips + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **True black canvas** (`#000000`) — OLED black so imagery glows; dark-only, no light mode
- **No brand accent** — white (`#FFFFFF`) is the only "primary"; chrome is a grey ramp `#FFFFFF → #A1A1A1 → #6E6E6E`
- **2×2 image grid is the hero** — every job renders as a 4-up quad; it carries 100% of the screen's color
- **Prompt bar** — natural language + `--flag` parameters; tap **Imagine** to submit
- **U1–U4 / V1–V4 chip grammar** — white upscale chips, dark variation chips, monospace, lifted from the Discord bot
- **Monospace for prompts & parameters** — the prompt is treated as code, not prose (`SF Mono`)
- **Grid reshapes to `--ar`** — container becomes 1:1 / 16:9 / 9:16; the 2×2 structure never changes
- **Discord blurple** (`#4D5BCE`) survives as a "Connect Discord" account accent only — never a general CTA
- **Progress blur → resolve** — jobs render as a shimmering placeholder, then crossfade to four sharp images
- **Imagery near-bleeds** — 14pt insets, 6pt seams; black is the matting around the print
- **Flat on black** — no card borders/shadows; floating uses a 1pt `#2A2A2A` border, not shadow
- **Minimal chrome** — bottom tabs (Create / Explore / Search / Library / Profile), thin segmented (Imagine / Edit / Personalize)

## Brand Sources

- [Midjourney](https://www.midjourney.com/)
- [Midjourney iOS App](https://apps.apple.com/us/app/midjourney/id6446882983)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (UI sans fallback)
- SF Mono (system monospace, iOS) — prompts & parameters
- Public brand palette: true-black canvas `#000000`, white-only primary `#FFFFFF`, Discord-heritage blurple `#4D5BCE`
