# Fitbit iOS Inspired Design System

Design system docs inspired by the [Fitbit iOS app](https://apps.apple.com/us/app/fitbit/id462638897). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Fitbit's public product UI and brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, rings & tiles |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, SVG rings, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Canvas rings + metric tiles |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Deep teal-black canvas** (`#001017` → `#002A3A`) — cyan undertone, never neutral gray
- **Fitbit Teal** (`#00B0B9`) brand / steps / CTA; brightens to `#21D9CE` for text & links on dark
- **Per-metric color system** — every metric owns one fixed hue everywhere: steps teal `#00B0B9`, heart rate coral `#FF6B81`, sleep purple `#7C5CFF`, readiness lime `#B8E986`, active zone orange `#FF8A3D`, calories gold `#FFC233`
- **Today dashboard** — a vertical scroll of generously rounded (16–24pt) stat cards & tiles
- **Steps ring hero** — big circular progress ring + centered count + encouraging side copy + trend chip
- **Progress rings everywhere** — the recurring shape language for goals (steps, sleep, zone, readiness)
- **Big number is the hero** — 40–44pt bold with tight tracking; labels & units stay quieter
- **Sleep score & Daily Readiness** — 0–100 score badges in their metric hue with plain-language coaching
- **Warm coaching copy** — "You're close!", "Good to go" — supportive, never clinical percentages
- **Pill everything** — fully-rounded CTAs and segmented controls; soft, encouraging feel
- **Ring sweep + count-up on appear** — the satisfying "draw"; goal hits trigger confetti + a success haptic
- **Deep ink on color** — teal buttons and metric chips use `#001017` glyphs, never white

## Brand Sources

- [Fitbit](https://www.fitbit.com/)
- [Fitbit (Google) brand](https://www.fitbit.com/global/us/home) — Fitbit Teal `#00B0B9`
- [DM Sans](https://fonts.google.com/specimen/DM+Sans) — SIL OFL (open stand-in for the brand sans)
- Public brand palette: Fitbit Teal `#00B0B9`, plus the per-metric hues (HR coral, sleep purple, readiness lime, zone orange, calories gold)
