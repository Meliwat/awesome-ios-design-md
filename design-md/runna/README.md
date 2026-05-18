# Runna iOS Inspired Design System

Design system docs inspired by the [Runna iOS app](https://apps.apple.com/us/app/runna-running-training-plans/id1511428659). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Runna's public product UI and App Store materials; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, week strip + session card + structure breakdown, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, Reanimated week strip + structure bar, Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, week strip + structure breakdown + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Training-plan week strip** — seven color-coded day cells (done / today / rest / typed), the at-a-glance plan
- **Guided run-session card** — bold Indigo gradient (`#4F46E5 → #3A33B8`), workout title + stats + Lime Start button
- **Workout-structure breakdown** — segmented warm-up → main set → cooldown bar + per-step pace rows
- **Two-color brand system** — Runna Indigo `#4F46E5` (structure) + electric Runna Lime `#C2F94E` (energy/"go")
- **Lime is action-only** — Start button, today-marker dot, key CTAs, PR badges; never a text color on dark
- **Run-type color system** — Easy `#34D399`, Tempo `#FBBF24`, Intervals `#FB7185`, Long `#4F46E5` (fixed across themes)
- **Pace targets everywhere** — every workout step shows a `/km` (or `/mi`) target in heavy numerals
- **Indigo-tinted dark canvas** — `#0E0E16`, deliberately tinted toward the brand, never neutral; low-glare for dawn/dusk
- **Lime always on near-black** — `#1A1A1A` is the only text color on Lime fills
- **Sora geometric sans** — athletic, technical; heavy stats (700–800), regular prose (400)
- **Squared-soft cards** (14–22pt radius) — disciplined like a training plan, not bubbly
- **Minimal chrome** — 5-tab bottom bar: Plan / Run / Progress / Club / Profile

## Brand Sources

- [Runna](https://www.runna.com/)
- [Sora by Jonathan Hill / Soulcave (Google Fonts)](https://fonts.google.com/specimen/Sora) — SIL OFL
- Public brand palette: Runna Indigo `#4F46E5`, electric Lime `#C2F94E`, Indigo-tinted dark canvas `#0E0E16`, and the run-type color system
