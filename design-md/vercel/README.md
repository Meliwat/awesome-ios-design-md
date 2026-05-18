# Vercel iOS Inspired Design System

Design system docs inspired by the [Vercel iOS app](https://apps.apple.com/us/app/vercel/id1559758631). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Vercel's public product UI, the Geist design system, and the company's brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, deployments list + build-log viewer |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **True black canvas** (`#000000`) — OLED-true black, NOT charcoal; the single most identifying trait
- **Deployments list as home** — status dot + state + env tag + mono URL + git metadata
- **Status traffic-light** — Ready `#0CCE6B` / Building `#F5A623` / Error `#E5484D` is the only systemic color
- **No brand-accent CTA** — the primary button is a white fill (`#EDEDED` on black)
- **Hairline borders, not shadows** — 1pt `#2E2E2E` separators do all elevation work
- **Geist Sans + Geist Mono** — proportional for prose, monospace for every URL / branch / hash / log
- **Geometric triangle logomark** — single-fill equilateral triangle, never restyled (`M37.527 0L75.054 65H0z`)
- **Vercel Blue `#0070F3`** — links, focused inputs, and analytics charts (no gridlines)
- **Environment tags** — `Production` / `Preview` mono pills, the most-repeated chip in the app
- **Avatar gradient** — `#7928CA` → `#0070F3`, the only place the purple gradient appears
- **Light mode is a clean inversion** — white `#FFFFFF` canvas, `#000000` text, same triangle

## Brand Sources

- [Vercel](https://vercel.com/)
- [Geist Design System](https://vercel.com/geist) — Geist Sans + Geist Mono, SIL OFL
- [Vercel Brand](https://vercel.com/design/brand) — triangle logomark + color usage
- Public brand palette: pure black `#000000`, white `#EDEDED`, blue `#0070F3`, status `#0CCE6B` / `#F5A623` / `#E5484D`
